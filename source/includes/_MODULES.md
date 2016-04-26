# <a name="modules"></a>AI Modules

AIVA comes with a set of well-rounded set AI/machine learning tools - each is the most advanced of its type. You can use the as needed, directly from within AIVA without extra installations. This section gives a quick overview of their uses, and some useful helper modules.


| Module | Implemented? |
|:---|---|
| [general NLP](#gen_nlp) | yes |
| [TensorFlow](#tensorflow) | yes |
| [Indico.io](#indico) | yes |
| [Knowledge base](#kbase) | yes |
| [neo4jKB brain](#neo4jKB-brain) | pending |
| [Google APIs](#google-api) | yes |
| [IBM Watson](#watson) | yes |
| [general customMsg](#custom-msg) | pending |


<aside class="notice">
To see all the available AI modules and methods, print the <code>ai</code> objects from <code>lib/js/ai.js</code> and <code>lib/py/ai.py</code>.
</aside>


## <a name="gen_nlp"></a>general NLP

```javascript
// js: scripts/hello_ai.js
var genNLP = require('../lib/js/gen_nlp')

var client = require('../lib/client.js')
global.gPass = client.gPass

var input = 'find me flights from New York to London at 9pm tomorrow.'

genNLP.parse(input)
.then(function(reply) {
  console.log(JSON.stringify(reply))
})
```

NLP is core to a human-machine interactions. We provide one of the fastest and most advanced NLP module in `lib/js/gen_nlp` for generic usage. It serves as a convenient method to get all the vital NLP results. This is especially useful in the `js` **interface** scripts for *parsing user inputs, normalizing texts, and extracting information*.

A large part of it is powered by [spaCy](https://spacy.io), which is *"the fastest syntactic parser in the world and that its accuracy is within 1% of the best available"*. The time parser uses [date](https://github.com/matthewmueller/date). Together, `gen_nlp` extracts a rich and accurate set of results for connecting natural language input to your modules.

Next we show an example of how a flight finder can benefit from it. For instance, it can get the `NER` or `noun_phrases` for the locations, time, person; the `time` for a normalized form of ISO time string; `NER_POS_TREE` to understand the dependencies, e.g. what's the origin and target destinations.

>The example in <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/hello_ai.js#L5" target="_blank"><code>scripts/hello_ai.js</code></a> shows how the generic NLP module in <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/js/gen_nlp.js" target="_blank"><code>lib/js/gen_nlp.js</code></a> returns the result pulled from spaCy in Python, and Date.js in Node.js.


```javascript
// the result from genNLP.parse(input)

result = {
  "len": 13,
  "pos_fine": [
    ["find", "VB"],
    ["me", "PRP"],
    ["flights", "NNS"],
    ["from", "IN"],
    ["New", "NNP"],
    ["York", "NNP"],
    ["to", "IN"],
    ["London", "NNP"],
    ["at", "IN"],
    ["9", "CD"],
    ["pm", "NN"],
    ["tomorrow", "NN"],
    [".", "."]
  ],
  "NER": [
    ["New York", "GPE"],
    ["London", "GPE"],
    ["9pm", "TIME"]
  ],
  "lemmas": ["find", "me", "flight", "from", "new", "york", "to", "london", "at", "9", "pm", "tomorrow", "."],
  "tokens": ["find", "me", "flights", "from", "New", "York", "to", "London", "at", "9", "pm", "tomorrow", "."],
  "pos_coarse": [
    ["find", "VERB"],
    ["me", "NOUN"],
    ["flights", "NOUN"],
    ["from", "ADP"],
    ["New", "NOUN"],
    ["York", "NOUN"],
    ["to", "ADP"],
    ["London", "NOUN"],
    ["at", "ADP"],
    ["9", "NUM"],
    ["pm", "NOUN"],
    ["tomorrow", "NOUN"],
    [".", "PUNCT"]
  ],
  "NER_POS_tag": [
    ["find", "VERB"],
    ["me", "NOUN"],
    ["flights", "NOUN"],
    ["from", "ADP"],
    ["New York", "GPE"],
    ["to", "ADP"],
    ["London", "GPE"],
    ["at", "ADP"],
    ["9pm", "TIME"],
    ["tomorrow", "NOUN"],
    [".", "PUNCT"]
  ],
  "NER_POS_tree": [{
    "find": {
      "children": [{
        "flights": {
          "children": [{
            "me": {
              "children": [],
              "edge": "nsubj",
              "tag": "NOUN"
            }
          }, {
            "from": {
              "children": [{
                "New York": {
                  "children": [],
                  "edge": "pobj",
                  "tag": "GPE"
                }
              }],
              "edge": "prep",
              "tag": "ADP"
            }
          }, {
            "to": {
              "children": [{
                "London": {
                  "children": [],
                  "edge": "pobj",
                  "tag": "GPE"
                }
              }],
              "edge": "prep",
              "tag": "ADP"
            }
          }, {
            "at": {
              "children": [{
                "9pm": {
                  "children": [],
                  "edge": "pobj",
                  "tag": "TIME"
                }
              }],
              "edge": "prep",
              "tag": "ADP"
            }
          }, {
            "tomorrow": {
              "children": [],
              "edge": "npadvmod",
              "tag": "NOUN"
            }
          }],
          "edge": "ccomp",
          "tag": "NOUN"
        }
      }, {
        ".": {
          "children": [],
          "edge": "punct",
          "tag": "PUNCT"
        }
      }],
      "edge": "ROOT",
      "tag": "VERB"
    }
  }],
  "noun_phrases": ["me", "New York", "London", "9pm"],
  "text": "find me flights from New York to London at 9 pm tomorrow .",
  "time": "2016-04-24T01:00:00.042Z"
}
```

The extracted NLP properties are listed below. For more details, refer to the links.

| NLP property | Reference |
|:---|---|
| text | The original input text. |
| len | Length: [number of tokens in text](https://spacy.io/docs#doc-sequenceapi). |
| tokens | Text broken into [array of tokens](https://spacy.io/docs#annotation-tokenization). |
| lemmas | The [base form](https://spacy.io/docs#annotation-lemmatization) of each token. |
| NER | [Named Entity Recognition](https://spacy.io/docs#annotation-ner) |
| noun_phrases | [grouping of meaningful nouns](https://spacy.io/docs#doc-spans-nounchunks) |
| pos_coarse | [Coarse](https://spacy.io/docs#token-postags) Part-of-speech tagging. |
| pos_fine | [Finer](https://spacy.io/docs#token-postags) Part-of-speech tagging.  |
| NER_POS_tree | The [syntactic and dependency parse tree](https://spacy.io/docs#doc-spans-nounchunks) of text, seen in [DisplaCy](https://spacy.io/demos/displacy) |
| NER_POS_tag | The flatten array of NER_POS_tree, without the dependencies. |
| time | [Parsed time](https://github.com/matthewmueller/date) if any, as ISO time string. |


## <a name="tensorflow"></a>TensorFlow

[TensorFlow](https://www.tensorflow.org) is used mainly to build neural networks. There is a lot of comprehensive tutorials for it, so instead we will introduce you to the fastest TensorFlow interface: [SkFlow](https://github.com/tensorflow/skflow).

SkFlow makes it very easy to use TensorFlow, and insanely fast to build a model. It comes with many useful wrapped methods and models. Check out this [demo post written by the team](http://terrytangyuan.github.io/2016/03/14/scikit-flow-intro/), their [tutorials](https://github.com/tensorflow/skflow#tutorial), and [examples](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/examples/skflow).

We show how you can deploy a neural net with AIVA, with a sample Deep Neural Network trained on the Titanic data.

The TensorFlow (and other) AI models shall be grouped under <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/" target="_blank"><code>lib/py/ais/</code></a>. The top level <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ai.py" target="_blank"><code>lib/py/ai.py</code></a> script will import from this folder for usage via Socket.io.

>The Titanic DNN model can be trained using TensorFlow under <a href="https://github.com/kengz/aiva/blob/aiva-v3/lib/py/ais/dnn_titanic_train.py#L53" target="_blank"><code>lib/py/ais/dnn_titanic_train.py</code></a> after you uncomment and run it. It draws data from the sibling folder <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/data/" target="_blank"><code>lib/py/ais/data/</code></a>, and the model is saved to <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/models/" target="_blank">lib/py/ais/models/</a>. 

>For deployment, <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ai.py" target="_blank"><code>lib/py/ai.py</code></a> imports and exposes the methods in <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/dnn_titanic_deploy.py" target="_blank">lib/py/ais/dnn_titanic_deploy.py</a>. They can then be used to do predictions from the interface <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/dnn_titanic.js" target="_blank">scripts/dnn_titanic.js</a>.

>TensorFlow Titanic DNN on Slack:
<img alt="Titanic DNN on Slack" src="./images/titanic_dnn.png" />

This demonstrates another AI component of AIVA; not only AI can power the bot, the bot can serve as an interface to specific AIs too. Feel free to explore the examples in AIVA and the resources given earlier. Doing AI is much easier today!

| file/folder | details |
|:---|---|
| <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/dnn_titanic_train.py" target="_blank">lib/py/ais/dnn_titanic_train.py</a> | Train and save the Titanic DNN model. |
| <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/data/" target="_blank">lib/py/ais/data/</a> | Folder for training and test data. |
| <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/models/" target="_blank">lib/py/ais/models/</a> | Folder for the saved model for deployment. Will be created when new model is trained. |
| <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/ai_lib/" target="_blank">lib/py/ais/ai_lib/</a> | Library with helpers for the AI folder. |
| <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/dnn_titanic_deploy.py" target="_blank">lib/py/ais/dnn_titanic_deploy.py</a> | Deploy the DNN titatnic model. |
| <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/dnn_titanic.js" target="_blank">scripts/dnn_titanic.js</a> | Interface script for the deployed model. |


Yes, *you did just train a Deep Neural Network and deploy it on Slack for use*. The mythical creative of deep learning - now at your finger tip - is going to help you unleash a whole new world of possibilities. And that’s not all; remember, there is an entire package of every major type of machine learning tools, all included in AIVA. Next, we will go over them quickly, and leave you to explore them more deeply.


## <a name="indico"></a>Indico.io

[Indico.io](https://indico.io) is **the go-to machine learning API** for developers. It focuses on the most applicable and useful ML tools for [text and image analysis](https://indico.io/product). This complements the custom-trained models of TensorFlow, and is useful especially when your use case can readily be solved by standard ML models.

>The indico wrapper is grouped under the `ai` module. Remember to save your API key into the `.keys-` before using. A simple example can be found at <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/hello_ai.js#L18" target="_blank"><code>scripts/hello_ai.js</code></a>.

```javascript
// js: scripts/hello_ai.js
var ai = require('../lib/js/ai')
ai.indico.sentiment(['indico is so easy to use!', 'Still really easy, yiss'])
.then(console.log)
// [ 0.9782025594088044, 0.9895808115135271 ]
```

>We also add an interface script <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/hello_ai.js" target="_blank"><code>below that</code></a>, and demo it on Telegram. Of course the real application is likely backend oriented, such as gauging the quality of your customer service by analyzing their sentiment in chat.

>Indico on Telegram:
<img alt="Indico on Telegram" src="./images/indico.png" />

With Indico.io, everything is done via its REST API or their [wrapper clients](https://indico.io/docs) - no model/training is needed. AIVA includes the [Node.js Indico.io wrapper](https://github.com/IndicoDataSolutions/IndicoIo-node), nested under the <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/js/ai.js" target="_blank">lib/js/ai.js</a>.

In fact, Indico is currently [state of the art](https://indico.io/news/121515_indico_SentimentHQ_Release) in sentiment analysis. All their APIs are very powerful and easy to use; they even explain the ML models employed for each, and the real-world use cases of each.

[Read their docs](https://indico.io/docs), [see what they offer](https://indico.io/product), and get [your API key here](https://indico.io/plans). You can get 10k free calls per month, which is sufficient for personal/small team usage.


## <a name="kbase"></a>Knowledge base

Sometimes we need a bot to have general knowledge, such as what a phone is, as well as local knowledge, such as user preferences. This can aid with its functions, and allow for **autoplanning**.

Autoplanning is vital for a bot to be able to function on its own without being explicitly told about every small detail of a task. Take the example input "call my wife". It's true that we can hard-code the logic to find who "wife" is for a given person, get "wife"'s number, and execute the dial function with it as the argument. What about plenty of other tasks? We can't possibly hand-code each one.

Here's a slightly different approach: for every function we allow the bot to do, we only define the function and its needed arguments. For calling a number it'd be `function = dial, args = [number]`. With a knowledge base and autoplanning, the input "call my wife" will be parsed into `function = dial`, `args = [number]`. Since it's only given a `person`, it can use the knowledge base to trace the connection `person -[has]-> number`, and proceed to retrieve the needed information from memory. The bot can use its own knowledge to plan and execute the function autonomously.

This is a pretty advanced feature that's still being implemented for AIVA. It is based on the theoretical proofs and outlines for a generic interface [HTMI](https://github.com/kengz/aiva/tree/aiva-v3/docs/HTMI.md) and a brain [CGKB](https://github.com/kengz/aiva/tree/aiva-v3/docs/CGKB.md).

AIVA comes with 3 knowledge bases, from generic to local: 

- [Google Knowledge Graph Search](https://developers.google.com/knowledge-graph/)
- [MIT ConceptNet](https://github.com/Planeshifter/node-concept-net)
- the local [neo4jKB brain](#graph-brain).

>For now you can use Google Knowledge Graph directly to query knowledge. Note this is distinct from normal Google search:
<img alt="Google Knowledge Graph on Slack" src="./images/googlekg.png" />


## <a name="#neo4jKB-brain"></a>Neo4jKB brain

We discussed the [graph brain](#graph-brain) above, known as [`neo4jKB`](https://github.com/kengz/neo4jKB), implemented in neo4j. It has a defined [KB standard](https://github.com/kengz/neo4jKB#kb-standard-basic) on the graph for proper usage as a knowledge base.

This serves as the local knowledge and the central memory system. It is the reason AIVA can behave as one consistent entity across different chat platforms. All of the memory-dependent tasks shall be read from and write to the brain.

AIVA uses the brain for user serialization, standardized in <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/js/user.js" target="_blank"><code>lib/js/user.js</code></a> and called automatically on start from <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/serialize_users.js" target="_blank"><code>scripts/serialize_users.js</code></a>. It will match up the multiple identities of a same user from different platforms. You can see all the serialized user nodes on neo4j's browser interface at `http://localhost:7474/` if you are running locally, or use [SSH forwarding described here](#ssh-browser-forwarding).

>The serialized users in AIVA's neo4jKB graph brain:
<img alt="Users in brain" src="./images/serialized_users.png" />

The todo list feature is another relevant example. The todo items are connected to their owner. The module logic for neo4jKB is defined in <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/js/todo.js" target="_blank"><code>lib/js/todo.js</code></a>.

>The todos belonging to a user as connected unit of information, in AIVA's neo4jKB graph brain:
<img alt="User todos" src="./images/user_todos.png" />

These two examples show briefly how to use neo4jKB for AIVA's modules, to help you get started. On our roadmap, we plan to complete the auto-parsing NLP module that automatically canonicalize knowledge from user input, so you don't have to write the define the knowledge yourself. This is closely related to the **autoplanning** of the [knowledge base feature](#kbase), and is part of [HTMI](https://github.com/kengz/aiva/tree/aiva-v3/docs/HTMI.md) and a brain [CGKB](https://github.com/kengz/aiva/tree/aiva-v3/docs/CGKB.md).


## <a name="#google-api"></a>Google APIs

Google APIs are grouped under the `ai` module because many of them are actually AI or powered by AI. <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/js/ais/google.js" target="_blank"><code>lib/js/ais/google.js</code></a> imports the [Google APIs Node.js client](https://github.com/google/google-api-nodejs-client). Not all of the APIs are initialized, you'd have to activate each one as you wish to use on [Google API Manager](https://console.developers.google.com) and in `lib/js/ais/google.js`.

<aside class="notice">
You must "enable APIs" from Google API Manager for your Google API key to use them.
</aside>

We have seen Knowledge Graph Search earlier; Google Search actually uses a [npm module google](https://www.npmjs.com/package/google) powered by a scraper, so it is free without quota. The interface script is at <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/google.js" target="_blank"><code>scripts/google.js</code></a>. Google Translate and language detection are also based on a scraper, in <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/ai_lib/translate.py" target="_blank"><code>lib/py/ais/ai_lib/translate.py</code></a> and is free without quota as well. The interface script is at <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/translate.js" target="_blank"><code>scripts/translate.js</code></a>.


## <a name="watson"></a>IBM Watson


