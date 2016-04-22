# <a name="features"></a>Features

This gives a top level overview of the features. See [Modules](#modules) to find usable functions for developers.


| Feature | Implemented? |
|:---|---|
| [fast setup](#setup2) | yes |
| [cross-platform](#cross-platform) | implementing |
| [multi-language](#multi-language) | yes |
| [built-in AI tools](#builtin-ai) | yes |


## <a name="setup2"></a>fast setup

[3 steps](#setup) to get a bot installed and running. It once took me just 15 seconds to deploy a bot for an unexpected demo. 

*P/S "fast" implies the least number of steps; dependency installation can be heavy and long.*


## <a name="cross-platform"></a>cross-platform

The Facebook bot paradigm is "why use multiple apps when you can access them from one bot?". I dare to ask "why talk to the bot from one platform instead of everywhere?"

This inspires the *omnipresence* feature: a bot that recognizes who you are, and can continue conversation in any supported platforms. We talk to our friends everywhere, and they don't forget us when we switch apps. Bots should do the same too.

In short: *one brain, multiple interfaces*. AIVA's hubot base allows for generality, and she can tap into multiple platforms by using different [adapters](#adapters). Unlike the original hubot though, she can exist simultaneously on multiple platforms by having several interface instances plugged into the respective adapters, and all share the same brain, which serves as the central memory.

For the list of supported adapters see [adapters](#adapters).


## <a name="multi-language"></a>multi-language

**Unite we stand**. When different languages work together, we can access a much larger set of development tools. Also, we wish that a developer can use the AIVA regardless of his/her favorite programming language. 

For more, see [Polyglot environment](#polyglot) and [Socket.io clients](#clients) for how it's done. We now support `node.js, python, ruby`.


## <a name="builtin-ai"></a>built-in AI tools

AIVA comes with a well-rounded set of AI/machine learning tools. We have packaged these tools nicely, so you can get right into work with them. This saves you the tideous hunt, installation, and setup. 

`lib/<lang>/ais/` contains the specific implementations of the AI modules/models, and `lib/<lang>/ai.<lang>` is the high-level script that imports the functions from `lib/<lang>/ais/` for usage.

Thanks to the multi-language feature, we can easily access the machine learning universe of Python. For example, <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/" target="_blank"><code>lib/py/ais/</code></a> contains a sample Tensorflow training script <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/dnn_titanic_train.py" target="_blank"><code>dnn_titanic_train.py</code></a>, and a script to deploy that DNN for usage <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/dnn_titanic_deploy.py" target="_blank"><code>dnn_titanic_deploy.py</code></a>. It is interfaced through <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/dnn_titanic.js" target="_blank"><code>scripts/dnn_titanic.js</code></a> that allows you to use run predictions.

The list of AI modules and their respective language:

| AI/ML | type | lang |
|:---|---|---|
| [Tensorflow](https://www.tensorflow.org) | Neural Nets | Python |
| [skflow (scikit-learn)](http://scikit-learn.org/stable/) | Generic ML algorithms | Python |
| [spaCy](https://spacy.io) | NLP | Python |
| [Indico.io](https://indico.io) | text+image analysis | (REST API) Node.js client |
| [IBM Watson](http://www.ibm.com/cloud-computing/bluemix/watson/) | cognitive computing | Node.js client |
| [Google API](https://console.developers.google.com/apis) | multiple | Node.js client |
| [ConceptNet](https://github.com/Planeshifter/node-concept-net) | semantic network | (REST API) Node.js interface |
| [NodeNatural](https://github.com/NaturalNode/natural) | NLP | Node.js |
| [wordpos](https://github.com/moos/wordpos) | WordNet POS | Node.js |
| [date.js](https://github.com/matthewmueller/date) | time-parsing | Node.js |

These tools are put close together in a [polyglot environment](#polyglot), so you can start combining them in brand new ways. An example is the generic NLP parser <a href="https://github.com/kengz/aiva/blob/aiva-v3/lib/js/gen_nlp.js" target="_blank"><code>lib/js/gen_nlp.js</code></a> for parsing user input into intent and functional arguments - it uses `date.js` and `spaCy`.

For more in-depth details, see [modules](#modules). Also if find a great ML tool we should include, submit a Pull Request!
