# <a name="adapters"></a>Adapters

AIVA can simultaneously connect to multiple chat platforms and still behave as one entity, due to its central [graph brain](#neo4jKB-brain). This allows you to **build once, run everywhere**.

It utilizes [hubot's adapters](https://github.com/github/hubot/blob/master/docs/adapters.md). So, feel free to add any as you wish. AIVA comes with Slack, Telegram, Fb adapters, installed with `npm run gi`, and auto-run with AIVA.

For each adapter, we encourage you to create two tokens, for **production** and **development** respectively. All bot tokens go into <a href="https://github.com/kengz/aiva/blob/aiva-v3/bin/.keys-example" target="_blank"><code>bin/.keys-aiva or bin/.keys-aiva</code></a> created automatically during `npm run gi`. If you do not wish to use an adapter, simple remove its token.

The webhooks for adapters are auto-set in [`ngrok`](#ngrok) in `index.js`; you don't even need to provide a webhook url.

As mentioned, we can use the custom message formatting of a platform, such as Slack attachment or Facebook rich messages. You can check the adapter by `robot.adapterName` to format message accordingly, then send it using the method `robot.adapter.customMessage` for any adapter. More below.

<aside class="notice">
CustomMessage will be generalized soon, and we will provide better in-code examples.
</aside>


## Slack

[hubot-slack](https://github.com/slackhq/hubot-slack) comes with AIVA. It uses Slack's RTC API. Create your bot and get the token [here](https://my.slack.com/services/new/bot), to set <a href="https://github.com/kengz/aiva/blob/aiva-v3/bin/.keys-example#L2" target="_blank"><code>HUBOT_SLACK_TOKEN</code></a>.

To use customMessage, see the [example (pending update)](https://github.com/slackhq/hubot-slack/issues/170#issuecomment-113315455), invoked when `robot.adapterName == 'slack'`. Refer to the [Slack attachments](https://api.slack.com/docs/attachments) for formatting.


## Telegram

[hubot-telegram](https://github.com/lukefx/hubot-telegram) comes with AIVA. Create your bot and get the token [here](https://core.telegram.org/bots#3-how-do-i-create-a-bot), to set <a href="https://github.com/kengz/aiva/blob/aiva-v3/bin/.keys-example#L3" target="_blank"><code>TELEGRAM_TOKEN</code></a>.

<aside class="notice">
Don't provide your webhook url; it is set up automatically in <a href="https://github.com/kengz/aiva/blob/aiva-v3/index.js#L86" target="_blank"><code>index.js with ngrok</code></a>.
</aside>

To use customMessage, see the [example](https://github.com/lukefx/hubot-telegram#telegram-specific-functionality-ie-stickers-images), invoked when `robot.adapterName == 'telegram'`. Refer to [Telegram bot API](https://core.telegram.org/bots/api) for formatting.


## Facebook

[hubot-fb](https://github.com/chen-ye/hubot-fb) comes with AIVA. Create your bot by creating a Fb App and Page, as detailed [here](https://developers.facebook.com/docs/messenger-platform/quickstart), and get the [Page Access Token in Step 3 here](https://developers.facebook.com/docs/messenger-platform/quickstart) to set <a href="https://github.com/kengz/aiva/blob/aiva-v3/bin/.keys-example#L4" target="_blank"><code>FB_PAGE_TOKEN</code></a>; set <a href="https://github.com/kengz/aiva/blob/aiva-v3/bin/.keys-example#L4" target="_blank"><code>FB_VERIFY_TOKEN</code></a> to any phrase you like.

<aside class="notice">
Don't provide your webhook url; it is set up automatically in <a href="https://github.com/kengz/aiva/blob/aiva-v3/index.js#L86" target="_blank"><code>index.js with ngrok</code></a>.
</aside>

To use customMessage, see [the examples](https://github.com/chen-ye/hubot-fb#sending-rich-messages-templates-images), invoked when `robot.adapterName == 'fb'`. Refer to the [Send API](https://developers.facebook.com/docs/messenger-platform/send-api-reference) for formatting.

