---
layout: post
title:  "A Ruby gem to simplify using Slack Block Kit"
date:   2023-07-31 08:05:31 +0100
categories: rails ruby
---
I put together [`slack_layout`](https://github.com/bgvo/slack_layouts), a Ruby gem that makes building Slack's [Block Kit](https://api.slack.com/reference/block-kit/blocks) layouts easy while keeping everything organized.

It started when I was reviewing a colleague's PR. Something like:

{% highlight ruby linenos %}
class SomeJob < ApplicationJob
  def perform(...)
    # Logic stuff
    $slack.chat_postMessage(
      channel: channel_id, 
      blocks: build_blocks(channel_id, "some message")
    )
  end

  def build_blocks(channel_id, message)
    [
      {
        "type": "section",
        "text": {
          "type": "mrkdwn",
          "text": message,
        },
      },
      {
        "type": "actions",
        "elements": [
          {
            "type": "button",
            "text": {
              "type": "plain_text",
              "text": "Archive Now!",
              "emoji": true,
            },
            "style": "danger",
            "value": channel_id,
            "action_id": "archive_now",
          },
          {
            "type": "button",
            "text": {
              "type": "plain_text",
              "text": "Snooze 14 days",
              "emoji": true,
            },
            "style": "primary",
            "value": channel_id,
            "action_id": "archive_snooze",
          },
        ],
      },
    ]
  end
end
{% endhighlight %}

Any method like the one above should catch your eye. The `blocks` stuff seemed to be something particular from Slack. Indeed, it is the spec to build Slack's blocks (units for Slack's Block Kit system). Roughly, it lets you compose rich UIs with these blocks. Did this mean we needed to do this JSON stuff every time we wanted to build this UI type? It seemed brittle. There probably was a better way.

### Layouts

Slack Block Kit reference docs don't especifically set an abstraction with the name layout. But the word came to me as I inspected [Christian Gregg](https://github.com/CGA1123)'s gem [`slack-ruby-block-kit`](https://github.com/CGA1123/slack-ruby-block-kit). I found Christian's gem because I thought someone had to have built something out to build these blocks more quickly. Indeed, his gem did some heavy lifting and laid down the work to speed up building these blocks. However, it is essential to develop conventions when working with a team. That's how I came up with the idea of the `SlackLayout` pattern—an interface to compose Block Kit "layouts" that can be easily reused and organized.

I released a gem in case anyone also thinks it can help: [SlackLayouts](https://github.com/bgvo/slack_layouts).

It provides a parent class (`SlackLayout`) and a clear interface (`#blocks`) to access the layout intuitively and build the blocks you need without writing JSON or remembering `slack-ruby-block-kit` namespaces.

It also makes sense. Different classes (in Rails jobs, models) can use different layouts without having to couple it to the class itself. 