# <a name="features"></a>Features

This gives a top level overview of the features. See [Modules](#modules) to find usable functions for developers.


| Feature | Implemented? |
|:---|---|
| [fast setup](#setup2) | yes |
| [cross-platform](#cross-platform) | implementing |
| [graph brain](#graph-brain) | improving |
| [multi-language](#multi-language) | yes |
| [built-in AI tools](#builtin-ai) | yes |
| [theoretical power](#theoretical) | yes |


## <a name="setup2"></a>fast setup

[3 steps](#setup) to get a bot installed and running. It once took me just 15 seconds to deploy a bot for an unexpected demo. 

*P/S "fast" implies the least number of steps; dependency installation can be heavy and long.*


## <a name="cross-platform"></a>cross-platform

The Facebook bot paradigm is "why use multiple apps when you can access them from one bot?". I dare to ask "why talk to the bot from one platform instead of everywhere?"

### Omnipresence

This inspires the *omnipresence* feature: a bot that recognizes who you are, and can continue conversation in any supported platforms. We talk to our friends everywhere, and they don't forget us when we switch apps. Bots should do the same too.

In short: *one brain, multiple interfaces*. AIVA's hubot base allows for generality, and she can tap into multiple platforms by using different [adapters](#adapters). Unlike the original hubot though, she can exist simultaneously on multiple platforms by having several interface instances plugged into the respective adapters, and all share the same brain, which serves as the central memory.

For the list of supported adapters see [adapters](#adapters).


## <a name="graph-brain"></a>graph brain

The brain of AIVA is a graph database; it is the central memory that coordinates with different adapters, allowing it to be consistent across the platforms; for example, it can remember who you are or your todo list, even as you switch apps.

Graph is a very generic and natural way of encoding information, especially for information that doesn't always follow a fixed schema. A knowledge base is often implemented as a graph (wordNet, conceptNet, google graph, facebook graph, etc.) due to the adhoc and connected nature of generic knowledge. Thus, we think it's the natural choice for AIVA.

Although graph and non-graph databases can both be Turing-complete, graphs tend to have a lower working complexity in practice, and it's data units can be schema-free. Turing completeness ensures that the database can do everything a computer supposedly can; lower complexity makes development easier, schema-free allows knowledge creation on-the-fly.

AIVA uses [`neo4jKB`](https://github.com/kengz/neo4jKB) as the brain - a knowledge base implemented graph with [`neo4j`](http://neo4j.com). Specifically, `neo4jKB` imposes a [KB standard](https://github.com/kengz/neo4jKB#kb-standard-basic) on the graph for proper usage as a knowledge base.

<aside class="notice">
Neo4jKB is a completed project. Currently it is used for user-recognition and user todo list. AIVA doesn't fully utilize Neo4jKB yet as the AI tools are under development. When done, AIVA will be able to automatically form and extract knowledge from the graph brain.
</aside>


## <a name="multi-language"></a>multi-language

**Unite we stand**. When different languages work together, we can access a much larger set of development tools. Also, we wish that a developer can use the AIVA regardless of his/her favorite programming language. 

For more, see [Polyglot environment](#polyglot) and [Socket.io clients](#clients) for how it's done. We now support `node.js, python, ruby`.


## <a name="builtin-ai"></a>built-in AI tools

AIVA comes with a set of well-rounded set AI/machine learning tools - each is the most advanced of its type. We have packaged these tools nicely, so you can get right into work with them. This saves you the tideous hunt, installation, and setup. 

`lib/<lang>/ais/` contains the specific implementations of the AI modules/models, and `lib/<lang>/ai.<lang>` is the high-level script that imports the functions from `lib/<lang>/ais/` for usage.

Thanks to the multi-language feature, we can easily access the machine learning universe of Python. For example, <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/" target="_blank"><code>lib/py/ais/</code></a> contains a sample Tensorflow training script <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/dnn_titanic_train.py" target="_blank"><code>dnn_titanic_train.py</code></a>, and a script to deploy that DNN for usage <a href="https://github.com/kengz/aiva/tree/aiva-v3/lib/py/ais/dnn_titanic_deploy.py" target="_blank"><code>dnn_titanic_deploy.py</code></a>. It is interfaced through <a href="https://github.com/kengz/aiva/tree/aiva-v3/scripts/dnn_titanic.js" target="_blank"><code>scripts/dnn_titanic.js</code></a> that allows you to use run predictions.

The list of AI modules and their respective language:

| AI/ML | type | lang |
|:---|---|---|
| [Tensorflow](https://www.tensorflow.org) | Neural Nets | Python |
| [scikit-learn](http://scikit-learn.org/stable/) | Generic ML algorithms | Python |
| [SkFlow](https://github.com/tensorflow/skflow) | Tensorflow + Scikit-learn | Python |
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


## <a name="theoretical"></a>theoretical power

AIVA is based on a theoretical interface [HTMI](https://github.com/kengz/aiva/tree/aiva-v3/docs/HTMI.md) and a brain [CGKB](https://github.com/kengz/aiva/tree/aiva-v3/docs/CGKB.md) that is *human-bounded Turing complete*. The theorem establishes that HTMI can be used by a human to solve any problems or perform any functions she enumerates that are solvable by a Turing Machine. Complete implementation is still underway.

Why is this important? Because for far too many times people have tried to solve problems that are unsolvable. Many of the bots out there are actually very restrictive. To allow developers to solve problems with full power, and with easy, we have built a general-purpose tool and made it theoretically complete, so you can focus on solving your problem.

