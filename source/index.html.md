---
title: API Reference

language_tabs:

toc_footers:
  - <a href='https://github.com/kengz/aiva'>AIVA Github</a>
  - <a href='https://github.com/kengz/aiva-doc'>AIVA documentation Github</a>

includes:
  - DEVELOPMENT
  - FEATURES
  - MODULES
  - TIPS
  - ADAPTERS

search: true
---

# AIVA [![Build Status](https://travis-ci.org/kengz/aiva.svg?branch=aiva-v3)](https://travis-ci.org/kengz/aiva) [![Coverage Status](https://coveralls.io/repos/github/kengz/aiva/badge.svg?branch=aiva-v3)](https://coveralls.io/github/kengz/aiva?branch=master) [![Dependency Status](https://gemnasium.com/kengz/jarvis.svg)](https://gemnasium.com/kengz/jarvis)

**AIVA** (A.I. Virtual Assistant): General-purpose virtual assistant for developers. 

| AIVA is | |
|:---|---|
| general-purpose | An app interface, AI assistant, anything! |
| cross-platform | Slack, Telegram, Facebook, IRC, Twilio, or any [hubot adapters](https://github.com/github/hubot/blob/master/docs/adapters.md) |
| multi-language | Run and pass data among Node.js, Python3, Ruby, etc. |
| built-in AI tools | Tensorflow, skflow, Indico.ml, spaCy, Watson, Google APIs |
| hackable | It extends [Hubot](https://github.com/github/hubot). Add your own modules! |
| powerful, easy to use | Check out [setup](#setup) and [features](#features) |

AIVA is based on a theoretical interface [HTMI](https://github.com/kengz/aiva/tree/aiva-v3/docs/HTMI.md) and a brain [CGKB](https://github.com/kengz/aiva/tree/aiva-v3/docs/CGKB.md) that is *human-bounded Turing complete*. The theorem establishes that HTMI can be used by a human to solve any problems or perform any functions she enumerates that are solvable by a Turing Machine. Complete implementation is still underway.



## Installation

Clone this repo:

```shell
git clone https://github.com/kengz/aiva.git
```

Use **Ubuntu >14.04** or **MacOSX**; For the fastest VM setup, I recommend [Digital Ocean](https://www.digitalocean.com), with this automatic [setup script](https://github.com/kengz/mac_setup). Optionally for manual setup, see [Dependencies](#dependencies).



## <a name="setup"></a>Setup, Run

### <a name="one-time-setup"></a>One-time Setup
- **install dependencies**: 

```shell
npm run gi
```

- **setup keys**: update `.env`, `bin/.key-aiva` (production), `bin/.key-aivadev` (development).

Check [**Setup tips**](#setup-tips) for more help.

<aside class="notice">
If you prefer a different bot name, replace "aiva" from the <code>bin/.keys-</code> and in <code>package.json</code>.
</aside>


### <a name="run"></a>Run
- **run**: `npm start`; append `--bot=<bot-name>` to run the non-default bots.


```shell
# alternative commands
forever list # see the list of bots running
npm stop # stop all bots
npm run debug # run aivadev, log to terminal
npm run debug --bot=aiva # debug aiva
npm run shell # fast dev, run aivadev with shell-adapter
npm test # run unit tests
```

The default hubot adapter is Slack. See [**Adapters**](#adapters) for connecting to different chat platforms.

