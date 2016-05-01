# <a name="dev-guide"></a>Development Guide

AIVA is created as an A.I. general purpose interface for developers. You can implement any features, use it simultaneously on the major platforms, and code in multiple languages. This solves the problem that many bots out there are are too specific, often bounded to one chat platform, and can only be developed in one language.

It has a NLP parser and KB brain out of the box. We take care of the crucial backend and system, so *developers can focus on things that matter*.

Since it is a generic interface, you can focus on writing your app/module. When done, plugging it into AIVA shall be way more trivial than writing a whole app with a MEAN stack or Rails to serve it.



### Production and Development

Per common practice, we distinguish between production and development version, using `NODE_ENV` environment variables. So we generate two sets of keys for two bots:

<aside class="success">
Use "aiva" for production, "aivadev" for development. They can coexist without system conflicts.
</aside>


```shell
# alternative commands
forever list # see the list of bots running
npm stop # stop all bots
npm run debug # run aivadev, log to terminal
npm run debug --bot=aiva # debug aiva
npm run shell # fast dev, run aivadev with shell-adapter
npm test # run unit tests
```

## <a name="polyglot"></a>Polyglot Environment

**Unite we stand**. Each language has its strengths, for example Python for machine learning, Node.js for web. With a built in `Socket.io` client logic, AIVA allows you to write in multiple coordinating languages.

For now we have `/lib/client.{js, py, rb}`. Feel free to add `Socket.io` client for more languages through pull request!


>For quick multilingual dev, you can start the **polyglot server** at `lib/io_start.js` with:

```shell
# shell: start the polyglot server
npm run server
```

<aside class="warning">
This is automatically run with <code>npm run</code>, so don't manual-run it before starting the bot.
</aside>


>then import a `lib/client.js` to test a local feature from the `js` interface. Example: the snippet at the top of <a href="https://github.com/kengz/aiva/blob/master/scripts/translate.js#L5" target="_blank"><code>scripts/translate.js</code></a> quickly tests the translate function in `python`. Uncomment and run it.


```javascript
// js: scripts/translate.js
var client = require('../client.js')
global.gPass = client.gPass

global.gPass({
  input: "hola amigos",
  to: 'ai.py',
  intent: 'nlp.translate'
}).then(console.log)
// hello friends
```

<aside class="success">
For development like above, <a href="https://github.com/kengz/aiva/tree/master/lib/client.js#L85" target="_blank"><code>lib/client.js</code></a> will automatically set the environment variables using <code>bin/.keys-aivadev</code> if not already.
</aside>


## <a name="polyglot-dev"></a>Polyglot Development

The quickest way to get into dev is to look at the examples in `lib/<lang>/` and `scripts/`, which we will go over now.

Development comes down to:

- **module**: callable low level functions, lives in `/lib/<lang>/<module>.<lang>`.
- **interface**: high level user interface to call the module functions, lives in `/scripts/<interface>.js`

The module can be written in any language if it has a Socket.io client. The interface is in `js`, and that's pretty easy to write.

<aside class="notice">
It is vital to follow the <a href="#project-dir">directory structure</a> to expose them to socket.io as it calls from <code>lib/&lt;lang&gt;/*</code> but not deeper. You can import nested modules from there and call it with dot-path. More on this later.
</aside>

You write a module in `<lang>`, how do you call it from the interface? There are 3 cases depending on the number of `<lang>` (including `js` for interface) involved.

### Case: 1 `<lang>`

`<lang> = js`. If your module is in `js`, just `require` it directly in the interface script.

<aside class="notice">
Since the AIVA is based on hubot, you can refer to <a href="https://github.com/github/hubot/blob/master/docs/scripting.md">hubot scripting guide</a>. You can also <a href="https://github.com/github/hubot/blob/master/docs/scripting.md#script-loading">load hubot scripts</a> written by others.
</aside>

### Case: 2 `<lang>`s

e.g. `<lang> = js, py`. 

1\. You write a module <a href="https://github.com/kengz/aiva/tree/master/lib/py/hello.py" target="_blank"><code>lib/py/hello.py</code></a>

2\. Call it from the interface <a href="https://github.com/kengz/aiva/tree/master/scripts/hello_py.js" target="_blank"><code>scripts/hello_py.js</code></a> using the exposed `global.gPass` function, with the JSON `msg`

```javascript
// js: scripts/hello_py.js
msg = {
  input: 'Hello from user.', // input for module function
  to: 'hello.py', // the target module
  intent: 'sayHi' // the module function to call with input
  // add more as needed
}
```

3\. Ensure the called module function returns a reply JSON to the interface:

```python
# py: lib/py/hello.py
reply = {
  'output': foo(msg.get('input')), # output to interface
  'to': msg.get('from'), # is 'client.js' for interface
  'from': id, # 'hello.py'
  'hash': msg.get('hash') # callback hash for interface
}
```

The JSON fields above are required for their purposes. `global.gPass` used by the interface will auto-inject and `id` for reply, and a `hash` to resolve the promise for the interface.

>The hello_py feature calls Python; on Telegram:
<img alt="hello_py on Telegram" src="./images/hello_py.png" />


### Case: 3 `<lang>`s

e.g. `<lang> = js, py, rb`

1\. You write modules in `py, rb` <a href="https://github.com/kengz/aiva/tree/master/lib/py/hello_rb.py" target="_blank"><code>lib/py/hello_rb.py</code></a>, <a href="https://github.com/kengz/aiva/tree/master/lib/rb/Hello.rb" target="_blank"><code>lib/rb/Hello.rb</code></a>

2\. Call one (`py` in this example) from the interface <a href="https://github.com/kengz/aiva/tree/master/scripts/hello_py_rb.js" target="_blank"><code>scripts/hello_py_rb.js</code></a> as described earlier.

3\. <a href="https://github.com/kengz/aiva/tree/master/lib/py/hello_rb.py" target="_blank"><code>lib/py/hello_rb.py</code></a> passes it further to the `rb` module, by returning the JSON `msg`

```python
# py: lib/py/hello_rb.py
reply = {
  'input': 'Hello from Python from js.', # input for rb module function
  'to': 'Hello.rb', # the target module
  'intent': 'sayHi', # the module function to call with input
  'from': msg.get('from'), # pass on 'client.js'
  'hash': msg.get('hash'), # pass on callback hash for interface
}
```

4\. <a href="https://github.com/kengz/aiva/tree/master/lib/rb/Hello.rb" target="_blank"><code>lib/rb/Hello.rb</code></a> ensure the final module function returns a reply JSON `msg` to the interface. 

<aside class="warning">
For auto-id, Ruby filename need to be the same as its module name, case-sensitive.
</aside>

```ruby
# rb: lib/rb/Hello.rb
reply = {
  'output' => 'Hello from Ruby.', # output to interface
  'to' => msg['from'], # 'client.js'
  'from' => @@id, # 'Hello.rb'
  'hash' => msg['hash'] # callback hash for interface
}
```

>The hello_py_rb feature calls Python then Ruby; on Slack:
<img alt="hello_py_rb on Slack" src="./images/hello_py_rb.png" />


### Dev pattern

With such pattern, you can chain multiple function calls that bounce among different `<lang>`. Example use case: retrieve data from Ruby on Rails app, pass to Java to run algorithms, then to Python for data analysis, then back to Node.js interface.

<aside class="success">
The generic advice: ensure that your functions return the correct JSON <code>msg</code>, and we handle the inter-language dataflow logic. See the <a href="#msg-json"><code>msg</code> JSON keys</a>.
</aside>


### "Ma look! No hand(ler)s!"

"Do I really have to add a handler to reply a JSON `msg` for **every** function I call?". That's really cumbersome. 

To streamline polyglot development further we've made the `client.<lang>`'s automatically try to compile a proper reply JSON `msg`, using the original `msg` it receives for invoking a function.

What this means is you can call a **module** (`to`) by its name, and its **function** (`intent`) by specifying the dotpath (if it's nested), then providing a valid `input` format (single argument for now).

In fact, <a href="https://github.com/kengz/aiva/tree/master/scripts/translate.js" target="_blank"><code>scripts/translate.js</code></a> does just that. It uses Socket.io to call the <a href="https://github.com/kengz/aiva/tree/master/lib/py/nlp.py" target="_blank"><code>lib/py/nlp.py</code></a>, which imports <a href="https://github.com/kengz/aiva/tree/master/lib/py/ais/ai_lib/translate.py" target="_blank"><code>lib/py/ais/ai_lib/translate.py</code></a>

>To test-run it, you can start the **polyglot server** at `lib/io_start.js` with:

```shell
# shell: start the polyglot server
npm run server
```

>Uncomment the snippet at the top of <a href="https://github.com/kengz/aiva/blob/master/scripts/translate.js#L5" target="_blank"><code>scripts/translate.js</code></a> and run it.

```javascript
// js: scripts/translate.js
var client = require('../client.js')
global.gPass = client.gPass

global.gPass({
  input: "hola amigos",
  to: 'ai.py',
  intent: 'nlp.translate'
}).then(console.log)
// hello friends
```

>This calls <a href="https://github.com/kengz/aiva/tree/master/lib/py/nlp.py" target="_blank"><code>lib/py/nlp.py</code></a> that imports <a href="https://github.com/kengz/aiva/tree/master/lib/py/ais/ai_lib/translate.py" target="_blank"><code>lib/py/ais/ai_lib/translate.py</code></a>, which returns the translated string instead of a reply JSON. The client will auto-compile a proper reply JSON msg for you.

```python
# py: lib/py/ais/ai_lib/translate.py
def translate(source, to="en"):
  return t.translate(source, from_lang="auto", to_lang=to)
```

>The translate feature calls Python that returns string that is auto-compiled into JSON msg by client.py; on Slack:
<img alt="translate on Slack" src="./images/translate.png" />

On receiving a `msg`, the `client.<lang>` tries to call the function by passing `msg`. If that throws an exception, it retries by passing `msg.input`. After the function executes and returns the result, `client.<lang>`'s handler will check if the reply is a valid JSON, and if not, will make it into one via `correctJSON(reply, msg)` by extracting the information needed from the received `msg`.

Lastly, after the `js` `global.gPass` sends out a `msg`, the final reply directed at it should contain an `output` field, as good dev pattern and reliability in promise-resolution. When `global.gPass(msg)` gets its reply and its promise resolved, you will see `hasher.handle invoking cb` in stdout.


<aside class="notice">
In fact, once we finish the NLP auto-parsing feature for user sentences, we wouldn't even need to write an interface; This is still under work.
</aside>


## Unit Tests

[![Build Status](https://travis-ci.org/kengz/aiva.svg?branch=aiva-v3)](https://travis-ci.org/kengz/aiva) [![Coverage Status](https://coveralls.io/repos/github/kengz/aiva/badge.svg?branch=aiva-v3)](https://coveralls.io/github/kengz/aiva?branch=master)

This repo includes only unit tests for `js` **modules** and **interface** scripts using `mocha`, and runs with `npm test`. Note that tests should be for systems, thus the tests for AI models are excluded.

For the module of other `<lang>`, you may add any unit testing framework of your choice.


## How it works: Socket.io logic and standard

### <a name="msg-json"></a>`msg` JSON keys for different purposes.

- to call a module's function in `<lang>`: <a href="https://github.com/kengz/aiva/tree/master/scripts/hello_py.js" target="_blank"><code>scripts/hello_py.js</code></a>

```javascript
// js: scripts/hello_py.js
msg = {
  input: 'Hello from user.', // input for module function
  to: 'hello.py', // the target module
  intent: 'sayHi' // the module function to call with input
  // add more as needed
}
```

- to reply the payload to sender: <a href="https://github.com/kengz/aiva/tree/master/lib/py/hello.py" target="_blank"><code>lib/py/hello.py</code></a>

```python
# py: lib/py/hello.py
reply = {
  'output': foo(msg.get('input')), # output to interface
  'to': msg.get('from'), # is 'client.js' for interface
  'from': id, # 'hello.py'
  'hash': msg.get('hash') # callback hash for interface
}
```

- to pass on payload to other module's function: <a href="https://github.com/kengz/aiva/tree/master/lib/py/hello_rb.py" target="_blank"><code>lib/py/hello_rb.py</code></a>

```python
# py: lib/py/hello_rb.py
reply = {
  'input': 'Hello from Python from js.', # input for rb module function
  'to': 'Hello.rb', # the target module
  'intent': 'sayHi', # the module function to call with input
  'from': msg.get('from'), # pass on 'client.js'
  'hash': msg.get('hash'), # pass on callback hash for interface
}
```

### `msg` JSON keys

| key | details |
|:---|---|
| input | input to the target module function. |
| to | filename of the target module in `lib/<lang>`. |
| intent | function of the target module to call. Call nested function with dot-path, e.g. `nlp.translate`. |
| output | output from the target function to reply with. |
| from | ID of the script sending the `msg`. |
| hash | auto-generated callback-promise hash from `client.js` to callback interface. |

Of course, add additional keys to the JSON as needed by your function.

<aside class="notice">
Overall, ensure that your functions return the correct JSON `msg`, and we handle the inter-language dataflow logic.
</aside>

<aside class="success">
Socket.io can send deeply nested JSON with standard data type.
</aside>


### Server
There is a socket.io server that extends Hubot's Express.js server: <a href="https://github.com/kengz/aiva/tree/master/lib/io_server.js" target="_blank"><code>lib/io_server.js</code></a>. All `msg`s go through it. For example, let `msg.to = 'hello.py', msg.intent = 'sayHi'`. The server splits this into `module = 'hello', lang = 'py'`, modifies `msg.to = module`, then sends the `msg` to the client of `lang`.

>For quick multilingual dev, you can start the **polyglot server** at `lib/io_start.js` with:

```shell
# shell: start the polyglot server
npm run server
```

<aside class="warning">
This is automatically with <code>npm run</code>, so don't manual-run it before starting the bot.
</aside>


### <a name="clients"></a>Clients
For each language, there is a socket.io client that imports all modules of its language within `lib`. When server sends a `msg` to it, the client's `handle` will find the module and its function using `msg.to, msg.intent` respectively, then call the function with `msg` as the argument. If it gets a valid reply `msg`, it will pass it on to the server.


>then import a `lib/client.js` to test a local feature from the `js` interface. Example: the snippet at the top of <a href="https://github.com/kengz/aiva/blob/master/scripts/translate.js#L5" target="_blank"><code>scripts/translate.js</code></a> quickly tests the translate function in `python`. Uncomment and run it.


```javascript
// js: scripts/translate.js
var client = require('../client.js')
global.gPass = client.gPass

global.gPass({
  input: "hola amigos",
  to: 'ai.py',
  intent: 'nlp.translate'
}).then(console.log)
// hello friends
```

<aside class="success">
For development like above, <a href="https://github.com/kengz/aiva/tree/master/lib/client.js#L85" target="_blank"><code>lib/client.js</code></a> will automatically set the environment variables using <code>bin/.keys-aivadev</code> if not already.
</aside>

>Note due to how a module is called using `msg.to, msg.intent`, you must ensure that the functions are named properly, and `Ruby`'s requirement that module be capitalized implies that you have to name the file with the same capitalization, e.g. `lib/rb/Hello.rb` for the `Hello` module.

We now support `node.js, python, ruby`.

<aside class="notice">
To add support for other language, say Java, you can add a <code>lib/client.java</code> by following patterns in <code>lib/client.{py, rb}</code>, and starting them in <code>lib/io_client.js</code>. Please create a pull request if you do so :)
</aside>


### Entry point
The entry point is always a `js` interface script, but luckily we have made it trivial for non-js developers to write it. A full reference is [hubot scripting guide](https://github.com/github/hubot/blob/master/docs/scripting.md).

`robot.respond` takes a regex and a callback function, which executes when the regex matches the string the robot receives. `res.send` is the primary method we use to send a string to the user.

Overall, there are 2 ways to connect with `lib` modules:

**global.gPass**: <a href="https://github.com/kengz/aiva/tree/master/scripts/hello_py.js" target="_blank"><code>scripts/hello_py.js</code></a> This is a global method to pass a `msg`. It generates a `hash` using `lib/hasher.js` with a `Promise`, which is resolved whenever the `js` client receives a valid reply `msg` with same `hash`. This method returns the resolved `Promise` with that `msg` for chaining.

**wrapped global.gPass**: <a href="https://github.com/kengz/aiva/tree/master/scripts/translate.js" target="_blank"><code>scripts/translate.js</code></a>, <a href="https://github.com/kengz/aiva/tree/master/lib/js/nlp.js" target="_blank"><code>lib/js/nlp.js</code></a> This is similar to above, but the `msg` is properly generated by a `js` lib module, resulting in a much cleaner and safer interface script. The lib module needs to be imported at the top to be used.


### Data flow

The msg goes through socket.io as 

- `js(interface script)`
- `-> js(io_server.js)`
- `-> <lang>(client.<lang>)`
- `-> js(io_server.js)`
- `-> ...(can bounce among different <lang> modules)`
- `-> js(client.js)`
- `-> js(interface script)`

For the `hello_py.js` example, the path is 

- `js(scripts/hello_py.js) user input`
- `-> js(lib/io_server.js)`
- `-> py(client.py), call py function`
- `-> js(io_server.js)`
- `-> js(client.js) call Promise.resolve`
- `-> js(interface script) send back to user`


## <a name="project-dir"></a>Project directory structure

What goes where:

| Folder/File | Purpose |
|:---|---|
| bin/ | bot keys, binaries, bash setup scripts. |
| lib/&lt;lang&gt;/ | Module scripts, grouped by language, callable via socket.io. See [Polyglot Development](#polyglot-dev). |
| lib/client.&lt;lang&gt; | Import all scripts from `lib/<lang>/` and expose them to socket.io for cross-language communication. |
| lib/io_start.js | socket.io server and client logic for cross-language communication. |
| logs | Logs from bot for debugging and healthcheck. |
| scripts | The `node.js` user interface for the `lib/` modules. |
| scripts/_init.js | Kicks off AIVA setups after the base Hubot is constructed, before other scripts are lodaded. |
| test | Unit tests; uses Mocha. |
| .env | Non-bot-specific environment variables. |
| external-scripts.json | You can [load Hubot npm modules](https://github.com/github/hubot/blob/master/docs/scripting.md#script-loading) by specifying them in here and `package.json`. | Specifies project dependencies and command shortcuts with npm. |

