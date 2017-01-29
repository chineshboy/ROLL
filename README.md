ROLL: Fast In-Memory Generation of Gigantic Scale-free Networks.
=====
[ROLL \[1\]][1] is a tool for fast generation of gigantic [Barabasi-Albert](https://en.wikipedia.org/wiki/Barab%C3%A1si%E2%80%93Albert_model) graphs. Barabasi-Albert is a model for generating synthetic graphs that exhibit the properties of large real-world networks, such as the [World-Wide-Web](https://en.wikipedia.org/wiki/World_Wide_Web), [Citation networks](https://en.wikipedia.org/wiki/Citation_analysis) and [social networks](https://en.wikipedia.org/wiki/Social_network). Networks generated by the Barabasi-Albert model exhibit the [scale-free](https://en.wikipedia.org/wiki/Scale-free_network) property, i.e. their [degree distribution](https://en.wikipedia.org/wiki/Degree_distribution) follows a [power-law](https://en.wikipedia.org/wiki/Power_law).

The following image illustrates how a BA-graph is incrementally generated. The BA model starts with an initial network with size m_0. Each new node is added to m existing nodes with probabilities proportional to the degree of each existing node. In other words, the more connections a node has, the more frequently the node will receive new connection.

![sample BA process](https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Barabasi_Albert_model.gif/300px-Barabasi_Albert_model.gif)

This package contains various implementations for the  Barabasi-Albert model, as discussed [[1]]. The difference between these implementations is in their algorithmic approach for roulette wheel selection from a dynamic distribution. These algorithms are listed below:

 - [Simple roulette wheel selection](https://en.wikipedia.org/wiki/Fitness_proportionate_selection)
 - Stochastic Acceptance [[2]]
 - Roll-Bucket [[1]]
 - Roll-Tree [[1]]

The [ROLL][1] roulette wheel methods, specifically the ROLL-tree algorithm, are specifically designed to enable fast generation of scale-free networks with the Barabasi-Albert model. Details are explained in the [ROLL paper \[1\]][1]
 
# Usage
------------------------------
 - Clone ROLL:
``
git clone https://github.com/alihadian/ROLL.git
``

 - ROLL is using Maven for building the package, ensure maven is installed on the system. To build the package, type:
``
cd ROLL
mvn package
``

 - Execute the jar file:
``
java -jar target/ROLL-0.3-SNAPSHOT-jar-with-dependencies.jar -n 1000 [other params] 
``

Parameters
-----------
 - **-n** number of nodes

 - **-m** number of edges per node, i.e. the average degree (default = 2)

 - **-o** output file name (default: no output) 

 - **-s** sampling mode (simple, roll-bucket, roll-tree, SA)
 
 - **-m0** number of initial nodes. Note that m0 >= m (default: =m)

 - **-d** only for ROLL-tree: path to save the degree distribution file.

Output
------
The saves resulting graph is saved in a text file. Also, the performance measures (execution speed and time taken by various components) is printed in the standard output.

### Output format:
You should explicitly define the output file (using -o switch), or otherwise no output file is created. The output file contains the edge list of the graph, where each line contains a single edge: ``source_node_id target_node_id`` (tab-delimtited). for example:
``
0	1
0	2
1	2
...
``

### Performance results:
Since this package is primarily used for performance comparison, the performance measures are printed in the standard output:
```
SamplingMode   NumNodes  m  NumEdges  NumComparisons  \\
TotalTime  SamplingTime  MaintenanceTime Num_buckets \\
Tree-code-word-length Tree-optimal-huffman-code-word-length  \\
Total-buckets-inserted* Total-buckets-removed*
```

**Notes:** 
 
 - ``SamplingTime`` is the time required for "selecting the next item", excluding the time of writing edge list to file and the time of maintaining the data structure after sampling
 
 - ``MaintenanceTime`` is the time for maintaining the data structure. For example, in ROLL-tree, the MaintenanceTime is the time taken to add new buckets to the tree (considering the balancing procedure) or remove a bucket from the tree. 
 
 - ``NumComparisons`` illustrates how many comparisons are performed by each of the algorithms to select an item from the roulette wheel. For example, in the simple roulette wheel algorithm, NumComparisons is the number of items traversed to find the target node corresponding to the random number. See [[1]] for detailed explanation. 
 
 - ``Num_buckets`` is the number of buckets in ROLL-tree and ROLL-bucket.

 - ``Tree-code-word-length`` and  ``Tree-optimal-huffman-code-word-length`` are the actual and [optimal](https://en.wikipedia.org/wiki/Huffman_coding) code word lengths of ROLL-tree. 
 - *``Total-buckets-inserted`` and ``Total-buckets-removed`` illustrate how many times a bucket is inserted/deleted into the ROLL-tree. These two measures are only printed for ROLL-tree and its variant ROll-tree-sorted.
	
Source code structure
---------------------
The source code contains three main folders:

 1. `org.hadian.bagraph.generators.BAGraphGenerator.java`: ُThis is the main class that generates Barabasi-Albert graphs. It can use various roulette wheel algorithms, that is chosen by user as a program argument.
 
 1. `org.hadian.bagraph.roulettes.*`: This package contains various roulette wheel data structures that can be used for generating BA graphs. All data structures in this package implement `org.hadian.bagraph.roulettes.NodesList`, which offers two simple operations (functions): "initialize graph", and "add one node to the graph".
	
 1. `org.hadian.bagraph.util.XOrShiftRandomGenerator.java`: An implementation of [XOrShift](https://en.wikipedia.org/wiki/Xorshift), a fast random number generator. An interesting fact about ROLL-tree (and to some extent, ROLL-bucket) is that the selection probabilities of items (buckets, or right/left subtrees) is not very skew, so that a less-accurate random generator performs well for these methods.
	
References
-----------
[[1]] A. Hadian, S. Nobari, B. Minaei-Bidgoli, and Q. Qu, ROLL: Fast in-memory generation of gigantic scale-free networks. In Proceedings of the ACM 	SIGMOD International Conference on Management of Data, 2016.

[[2]] A. Lipowski and D. Lipowska. Roulette-wheel selection via stochastic acceptance. Physica A, 391(6):2193–2196, 2012.


License
-------
Licensed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).


Citing the paper
----------------
If you find this package useful, we kindly ask you to cite [the paper \[1\]][1]. Here is the BibTex record:

```
@inproceedings{hadian2016roll,
  title = {ROLL: Fast in-memory generation of gigantic scale-free networks}, 
  author = {Hadian, Ali and Nobari, Sadegh and Minaei-Bidgoli, Behrouz and Qu, Qiang},
  booktitle = {Proceedings of the 2016 ACM SIGMOD International Conference on Management of Data (To appear)},
  publisher = "ACM",
  year = {2016},
  doi={10.1145/2882903.2882964}
}
```

For further inquiries, feel free to contact [me](http://hadian.org).

[1]: http://dl.acm.org/citation.cfm?doid=2882903.2882964
[2]: http://dx.doi.org/10.1016/j.physa.2011.12.004
