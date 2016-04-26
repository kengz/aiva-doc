# <a name="modules"></a>Modules

| Module | Implemented? |
|:---|---|
| [general NLP](#gen_nlp) | yes |
| [TensorFlow](#tensorflow) | yes |
| [Indico.io](#indico) | yes |
| [Knowledge base](#kbase) | yes |
| [google APIs](#google-api) | yes |
| [general customMsg](#custom-msg) | pending |
| [neo4jKB brain](#neo4jKB-brain) | pending |

misc list of all functions and modules in all lang that come in the box. Need programmatic way to generate such a list, ohh maybe during client start?

## <a name="gen_nlp"></a>general NLP

```javascript
// js: scripts/hello_nlp.js
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

>The example in <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/hello_nlp.js#L5" target="_blank"><code>scripts/hello_nlp.js</code></a> shows how the generic NLP module in <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/js/gen_nlp.js" target="_blank"><code>lib/js/gen_nlp.js</code></a> returns the result pulled from spaCy in Python, and Date.js in Node.js.


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


Yes, *you did just train a Deep Neural Net and deploy it on Slack for usage*. The mythical creative of deep learning - now at your finger tip - is going to help you unleash a whole world of possibilities. And that's not it; remember, there is an entire package of nearly every type of machine learning tools, all included in AIVA. Next, we will go over them quickly next, and leave you to explore them more deeply.



