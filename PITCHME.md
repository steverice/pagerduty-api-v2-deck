#HSLIDE

## API Creation to Iteration
### Without the Frustration

#### Steve Rice, API Captain, <span class="pd-green">PagerDuty</span>

#VSLIDE?image=assets/images/pagerduty_brand_stamp.png

## Who am I?

- software engineer at PagerDuty since 2013 <!-- .element: class="fragment" -->
- incident resolution platform: triage events, manage incident response <!-- .element: class="fragment" -->
- Rails, MySQL, Scala, Cassandra, Kafka, etc. <!-- .element: class="fragment" -->
- Backbone, Ember.js, Android + iOS <!-- .element: class="fragment" -->

#HSLIDE

# Research

<!--
As with any project, the first thing to do is gather information. Figure out what you want to do with your new API version. Chances are, there are some things about the current version you aren't too happy with, so start by finding better ways and identifying problems you didn't even realize existed.
-->

#VSLIDE?image=assets/images/turtles.jpg

## Stand on the Shoulders of Giants

<!--
APIs are not new; people have been working on this stuff for decades. Especially when the goal of your technology is to interoperate, it pays to leverage the best practices happening in the space.
-->

#VSLIDE?video=assets/videos/amber-feng.mp4

## Conferences and Talks

<!--
Talks are a great way to get ideas and get out of your day-to-day headspace.
You're here, so you're off to a good start!
This talk by Amber Feng at Stripe, entitled "Move Fast, Don’t Break the API", set me up with some great ideas about how to manage multiple versions of an API on a technical level.
-->

#VSLIDE?image=assets/images/dropbox-blog.png

## Developer Blogs

<!--
A large part of your change is going to involve publicity. So read up, and find good work by others in the space.
This post by Leah Culver at Dropbox was a great example of how to be concise and  helpful when talking about a new API version.
-->

#HSLIDE

# Plan

#VSLIDE?image=assets/images/bike-shed.jpg

## Make decisions.

<!--
Design requires decision-making. Especially when it comes to things that on the surface appear purely aesthetic (like an API), everyone will have input.
In some technical areas, it can be quite valuable to have a decision maker whose head is in the game and can make a final call and move on.
Even better, outsource your decisions if possible. Protocols like OData, JSON API, or GraphQL have done a lot of the standardization work for you so there are fewer decisions left to be made. Remember that REST is an architectural style, not a protocol — you'll end up developing your own. I said "fewer" because you'll still need to make application-specific choices, but reducing the scope of these can help maintain sanity.
-->

#VSLIDE?gist=e33e5d401e5f8fa9e260ba1b7a20bdd2

<!--
To kick off PagerDuty API v2, I gathered input from a number of sources:
- the commonalities in our existing API documentation
- what made for clean code and what didn't (experience + analysis)
- discussions with colleagues and interested parties
- feature requests and customer support: what was causing people pain?
- analyze third-party, open-source code
The result was this: an "API v2 vision" that describes the goals of the new API version and the specifics of the patterns we were to formalize.
-->

#VSLIDE

## Pick your Battles

- Don't change what works <!-- .element: class="fragment" -->
- What value does this change bring? <!-- .element: class="fragment" -->
- How will clients consume it? <!-- .element: class="fragment" -->
- Keep bullets away from feet <!-- .element: class="fragment" -->
- Documentation is the developer experience <!-- .element: class="fragment" -->

<!--
When you're working on a new version of your API, you're fighting a war on two fronts: your developers will need to work to adopt, and your company will need to provide the time and resources to make it happen. So focus only on what's most important and will make a meaningful difference for yourselves and your developers.
At PagerDuty, our API is a key competitive differentiator. When we can say to customers, "sure, you can do that with the API", it has real results. Building an ecosystem makes those conversations even easier; enabling developer productivity is the #1 goal.
But there's a secondary motivation. Designing APIs is tough. Designing an API to last 10 years is nearly impossible without change. You need to address the decisions that were fine when they were made but are now causing performance, scaling, or quality issues.
-->

#VSLIDE

## Change the Plan

![Changed Includes](assets/images/change-proposal.png)

<!--
As you develop, things will change. You'll find where your patterns break down. You'll see where you need something different. Other smart people will question your choices.
Don't ignore these inputs; adapt to them. Have a process that encourages change instead of blocking it.
-->

#VSLIDE?image=assets/images/api-concerns-proposal.png

## Process is Paramount

<!--
The most difficult part of maintaining an API is adapting to change. We need to strike a balance between building new and shiny things and preventing thrash for our developers. As consistency is one of the main tenants of our API, a big challenge was, and remains, keeping new development in line with the API goals without standing in the way of progress.
Originally, we had an email list that we'd use to discuss new API changes. It was common knowledge among the engineers that if you wanted to make an API change, it would need to be proposed to the email group where our API Czar sat. But this was slow, cumbersome, and difficult to make sure the right discussion happened and translated into the proper course of action.
-->

#VSLIDE?gist=2109c70ecff5b71d8bc0a072760b03fe

## Process is Paramount

<!--
So we used a tool that our developers were already familiar with: GitHub. Contributors can now write in Markdown, comment on specific lines within the proposal, and have the back-and-forth type of review discussion that GitHub facilities. Familiar GitHub semantics like merging a pull request to indicate finality and version tracking to see how a proposal evolved gives much more context of the past and current state of a proposal. Plus, nice things like the abilitiy to link directly to something being discussed.
-->

#VSLIDE

![Flagger in Action](assets/images/flagger.png)

https://github.com/PagerDuty/flagger

<!--
In practice, this still needs refinement. One of the big challenges is making sure that people are doing this diligence when they need to be. It's difficult enough to get dozens of engineers to buy into a new process; even moreso when they're often unaware they need to participate.

Here's one thing we experimented with: using knowledge of our Rails codebase to flag pull requests making changes to API code for further review.
-->

#HSLIDE

# Document

#VSLIDE

![Stripe API Documentation](assets/images/stripe-api-docs.png)

#VSLIDE?image=assets/images/maintainable.jpg

## Keep it maintainable

#VSLIDE?gist=956f25b0d2c3f064c278529fc475da18

#VSLIDE

## Choose domain-specific technologies

![API Spec Formats](assets/images/api-specs.png)

#VSLIDE?gist=44dca2fec85c13810f1c102bacedfa1e

#VSLIDE?image=assets/images/remember-the-humans.jpg

## Consider the Humans

#VSLIDE?image=assets/images/swagger-docs.png

## Make it interactive

#VSLIDE?image=assets/images/v2-migration-guide.png

## Remember your audience

#HSLIDE

# Develop

#VSLIDE

## Keep it Simple

```ruby
class Api::V2::EscalationPolicyAdapter::Base < ApiDuty::Adapter
  root_attribute :escalation_policy

  validate :name, validator: :is_string
  validate :escalation_rules, validator: :is_array
  validate :description, validator: :is_string
  validate :num_loops, validator: :is_integer
  validate :teams, validator: :is_array
  validate :teams, validator: make_validator_each(
      make_validator_object_resource(%w(team).freeze)
    )

  passthrough :name, :description
  generate :num_repeats, from: :num_loops do |num|
    num.to_i + 1
  end
  generate :escalation_policies_teams_attributes,
    from: :teams, generator: :teams_join_params
  generate :escalation_policies_teams_attributes,
    from: :teams, generator: make_generator_default([])
end
```

#HSLIDE

# Test

#VSLIDE?image=assets/images/dogfood.jpg

# Dogfood. Everything.

#VSLIDE

## Automate

![Automated API Testing](assets/images/automated-api-testing.png)

#HSLIDE

# Analyze

#VSLIDE

## Gather usage data

- which endpoints aren't being used?
- which endpoints are being used in ways they shouldn't?
- which customers are using the APIs?

#VSLIDE?image=assets/images/pour-over.jpg

## Pour over logs

#HSLIDE

# Evangelize

#VSLIDE

![PagerCon Talk: API 0 to 30](assets/images/pagercon-talk.jpg)

#HSLIDE

# Connect

#VSLIDE

![Customer Support](assets/images/customer-support.png)

#HSLIDE

# Market

#VSLIDE

![v1 docs banner](assets/images/v1-docs-bannerless.png)

#VSLIDE

![v1 docs banner](assets/images/v1-docs-banner.png)

#HSLIDE

# Measure

#VSLIDE

![API v2 Use Metrics](assets/images/v2-use-metrics.png)

#HSLIDE

# Appendix

#VSLIDE

## Image Credits

- "Stand on the Shoulders of Giants" By Samir Eberlin [CC0], via Wikimedia Commons — https://commons.wikimedia.org/wiki/File:Turtles.on.a.stone.in.brazil.jpg
- "Make Decisions." By SeppVei (Own work) [CC0], via Wikimedia Commons — https://commons.wikimedia.org/wiki/File:Bicycle_shed.JPG
- "Dogfood. Everything." Original source not found. Used without permission.
- "Keep it maintainable" By David Jones from Isle of Wight, United Kingdom [[CC BY 2.0](http://creativecommons.org/licenses/by/2.0)], via Wikimedia Commons — https://commons.wikimedia.org/wiki/File:Mike_O%27Callaghan-Pat_Tillman_Bridge_construction.jpg
- "Pour over logs" by Timothy Marsee [[CC BY 2.0](http://creativecommons.org/licenses/by/2.0)], via Flickr — https://www.flickr.com/photos/tmarsee530/9899684046