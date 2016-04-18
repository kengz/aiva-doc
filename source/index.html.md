---
title: API Reference

language_tabs:


toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - TIPS
  - HTMI
  - CGKB
  - errors

search: true
---

# AIVA [![Build Status](https://travis-ci.org/kengz/aiva.svg?branch=aiva-v3)](https://travis-ci.org/kengz/aiva) [![Coverage Status](https://coveralls.io/repos/github/kengz/aiva/badge.svg?branch=aiva-v3)](https://coveralls.io/github/kengz/aiva?branch=master) [![Dependency Status](https://gemnasium.com/kengz/jarvis.svg)](https://gemnasium.com/kengz/jarvis)

**AIVA** (A.I. Virtual Assistant): General-purpose virtual assistant for developers. 

| AIVA is | |
|:---|---|
| general-purpose | An app interface, AI assistant, anything! |
| cross-platform | Slack, Telegram, IRC, Twilio, or any [hubot adapters](https://github.com/github/hubot/blob/master/docs/adapters.md) |
| multi-language | Runs scripts among Node.js, Python3, Ruby, etc. |
| hackable | It extends [Hubot](https://github.com/github/hubot). Add your own modules! |
| powerful, easy to use | Check out [setup](#setup) and [features](#features) |

AIVA is based on a theoretical interface [HTMI](./docs/HTMI.md) and a brain [CGKB](./docs/CGKB.md) that is *human-bounded Turing complete*. The theorem establishes that HTMI can be used by a human to solve any problems or perform any functions she enumerates that are solvable by a Turing Machine. Complete implementation is still underway.



## Installation

Clone this repo:

```shell
git clone https://github.com/kengz/aiva.git
```

Use **Ubuntu >14.04** or **MacOSX**; For the fastest VM setup, I recommend [Digital Ocean](https://www.digitalocean.com), with this automatic [setup script](https://github.com/kengz/mac_setup). Optionally for manual setup, see [Dependencies](#dependencies).



## Setup, Run

### <a name="setup"></a>One-time Setup
- **install dependencies**: 

```shell
npm run gi
```

- **setup keys**: update `.env`, `bin/.key-aiva` (production), `bin/.key-aivadev` (development).

Check [**Setup tips**](#setup-tips) for tips.

<aside class="notice">
If you prefer a different bot name, replace "aiva" from the <code>bin/.keys-</code> and in <code>package.json</code>.
</aside>

### <a name="run"></a>Run
- **run**: `npm start`; append `--bot=<bot-name>` to run the non-default bots.


```shell
# alternative commands
npm stop # stop all running bots
npm run debug # run aivadev, log to terminal
npm run debug --bot=aiva # debug aiva
npm run shell # to dev, run aivadev with shell-adapter
npm test # run unit tests
forever list # see the list of bots running
```


# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

