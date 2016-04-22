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
var genNLP = require('../lib/js/gen_nlp')

s = 'find me flights from New York to London at 9pm tomorrow.'
genNLP.parse(s).then(function(res) {
  console.log(JSON.stringify(res))
})
```

We provide a fast NLP module in `lib/js/gen_nlp` for generic usage. It serves as a convenient method to get all the vital NLP parsed results. This is especially useful in the `js` **interface** scripts for *parsing user inputs, normalizing texts, and extracting information*.

>This will yield the result pulled from spaCy in Python, and Date.js in Node.js.

```javascript
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

| NLP property | Details |
|:---|---|
| text | The original input text. |
| len | Length: number of tokens in text. |
| tokens | Text broken into array of tokens. |
| lemmas | The [base form](https://en.wikipedia.org/wiki/Lemmatisation) of each token. |
| NER | [Named Entity Recognition](https://en.wikipedia.org/wiki/Named-entity_recognition) |
| noun_phrases | grouping of meaningful nouns |
| pos_coarse | [Coarse](https://spacy.io/docs#token-postags) Part-of-speech tagging. |
| pos_fine | [Finer](https://spacy.io/docs#token-postags) Part-of-speech tagging.  |
| NER_POS_tree | The syntactic and dependency parse tree of text, seen in [DisplaCy](https://spacy.io/demos/displacy) |
| NER_POS_tag | The flatten array of NER_POS_tree, without the dependencies. |
| time | Parsed time if any, as ISO time string. |



Example app that uses this.
