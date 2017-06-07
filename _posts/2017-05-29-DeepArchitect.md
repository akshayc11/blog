---
layout: post
title: Deep Architect
category: synopsis, hyper-parameter optimization
customjs:
	- "http://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML"
---

This post describes my understanding of the deep architect paper as described by Negrinho et al.

# [DeepArchitect: Automatically Designing and Training Deep Architectures](https://www.cs.cmu.edu/~negrinho/assets/papers/deep_architect/deep_architect.pdf)

## Authors:
1. [Renato Negrinho](https://www.cs.cmu.edu/~negrinho)
2. [Geoff Gordon](https://www.cs.cmu.edu/~ggordon)


## My Synopsis
This paper presents a python based framework for specifying hierarchical hyper-parameters for deep neural networks. This search space structure is leveraged to use various model search algorithms:

1. Random Search
2. Monte Carlo Tree Search (MCTS)
3. Monte Carlo Tree Search with tree Restucturing
4. Sequential Model based Global Optimization (SMBO)

The authors conduct experiments of deep neural networks of a specific depth, with a large number of discrete hyper-parameters, and show that this structure allows for their SMBO and MCTS with restructuring to outperform random search for a single task.

Below, I will try to explain my rudimentary understanding of this in layman's terms as much as possible:

## Specifying the search space (Section 4):

The authors use a _Computational Module_ as a fundamental unit for the search space specification. Each computational module will take in an input of dimensionality _n_, and output a vector of dimensionality _m_.
Each module will be controlled by their own set of hyper-parameters, which are to be defined within their scope.

Currently, I do not see any module that splices the output of two layers, which is required to implement TDNN.


## The Model search Algorithms
In this section, I attempt to explain the different model search algorithms.

### Random Search:
Simply traverse the created tree from root till a node to get a fully specified model structure and hyper-parameters.
Use this to build the model, and train it on the given data.

### Monte Carlo Tree Search
This needs to have two policies:
1. A _tree_ policy that determines the path to traverse from a root node to a frontier node
2. A _rollout_ policy that determines the path to traverse from a frontier node to a leaf.

So, what is a frontier node? Simply put, a frontier node is one that has not yet been expanded (i.e. a model that is specified by it has not been used till now.

#### Tree Policy
The tree policy, as described above, determines the path to traverse from a root node to a frontier node within the already expanded sections of the graph.
How is this done? Well, at the end of each evaluation, the tree statistics are updated. All the nodes in the tree that were traversed to reach the evaluated leaf get their statistics updated by 1
Also the average score of those nodes are updated. When going down the expanded node, an upper confidence bound is determined for all the child nodes. Child nodes who have not been expanded yet will have infinite confidence. So, the system will choose to expand them first. 
let $n_1, n_2, ... n_b$ and $\hat{X}_1, \hat{X}_2, ... \hat{X}_b$ be the number of visits and the average score of the $b$ children of node $v$. The tree policy will choose the child that maximizes:

$$
\max_{i\in 1, ..., b} \hat{X}_i + 2c \sqrt{\frac{2 log n}{n_i}}
$$
$c \in \mathcal{R}_+$ is a constant that performs the tradeoff between exploration and exploitation within MCTS.

#### Rollout Policy

The rollout policy is to expand previously unexpanded nodes. Usually it is just a random sampling.


### MCTS with tree restructuring:

This basically restricts the number of children each node in the tree can have. For hyper-parameters with multiple values, it will basically perform a bisection based on the values, with higher depth performing finer bisections till a single leaf is reached. This restructuring seems to help because you can get better statistics grouping for the tree policy.

### Sequential Model based Global Optimization

This builds a surrogate function based on the features of previously completed model builds, and uses that to predict the performance of other points in the tree. It will select the model structure that optimizes the surrogate function and evaluate it. It also allows for exploration by choosing between this, and a simple random search.

In this paper, the authors use sequences of the simple modules used to create the previous models as features. I am still unclear on the details, and will update this page as I understand it better.


## Potential Drawbacks
1. The current approach does not handle continuous hyper-parameters. It chooses to discretize them.
2. No experiments have been done where the number of layers are different.
3. Currently, the expert is still needed to specify the ranges and values of the possible search space.
