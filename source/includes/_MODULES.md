# <a name="modules"></a>Modules

| Module | Implemented? |
|:---|---|
| [general NLP](#gen_nlp) | yes |
| [tensorflow](#tensorflow) | yes |
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
| time | Parsed time if any, as ISO time string. |

