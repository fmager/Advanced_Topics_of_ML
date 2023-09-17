---
theme: default
paginate: true
created: 2023-08-25-11-14
updated: 2023-09-17-22-31
---

# Stability of Relative Presentations
by Fabian Mager

## About Relative Representations

In the paper [RELATIVE REPRESENTATIONS ENABLE ZERO-SHOT LATENT SPACE COMMUNICATION](https://arxiv.org/pdf/2209.15430.pdf), Luca Moschella et al. showed that the latent space not only depends on the data, the loss and the task, but also on weight initialization and other hyperparameters:

> The shape of the latent space depends _ideally_ on data distribution, the task, and additional constraints. _In practice_ it also depends on parameter initialization, data shuffling, training seed, etc.

In their work they present a method called _relative representations_, which instead of expressing the latent space as absolute values of a given hidden layer, each value is given relative to the coordinates of a given set of anchors. Their methods shows invariance to latent isometries and rescaling, which enables for example zero-shot model stitching (See figures below, [(souce)][https://github.com/fmager/Self_Supervised_Learning_Summer_School_2023/blob/main/Slides/03_RelativeRepresentations_02.pdf]).

![bg contain invert](absolute_spaces.png)
![bg contain invert](relative_spaces.png)


In their work, the authors choose the set of anchors at random. However, on can imagine that some choice of anchors may be better then another. For example, when choosing a set of anchor points which lie very close in the chosen distance space, one might introduce scaling artefacts.

Other possible methods to define anchors might be useful, such as archetypal analysis. In contrast to cluster centers, archetypal points are extremal points in the multidimensional data space, such that all other data points can be expressed as a convex combination of archetypes [\[1\]](https://en.wikipedia.org/wiki/Archetypal_analysis#:~:text=Archetypal%20analysis%20in%20statistics%20is,data%2C%20the%20%22archetypes%22.).

In this report I want to see how the choice of anchors affects the relative representation. I hypothesize that a more robust choice of anchors, e.g. archetypal data points, give lower variance in the relative representations.
## Method

Similar to the exercise, I choose to train a Variational Autoencoder with two hidden units on MNIST. I chose to split the training set, and use each split to train a model, denoted as $M_1$ and $M_2$. Both models are trained for 100 epochs. Both models achieved a similar reconstruction loss. I use the same test set on both models to investigate relative representations. 

In order to approximate archetypes, I choose the farthest-first algorithm. This algorithm works as follows:

1. Let $S$ be an empty and $\mathbb{X}$ be the full set of points.
2. Choose an arbitrary data point in $D$ and include it in $S$.
3. Find the point in $D$ that is the farthest from all point in $S$ and include it in $S$.
4. Repeat the previous step $N$ times.

The algorithm above will produce $N+1$ data points, which can be used as archetypes. As only two anchor points are needed to transform a two-dimensional absolute latent space into a relative space, I set $N=2$. The first anchor, which is chosen at random, is omitted from the set.

The relative representations of $\mathbb{X}$ are then computed using the final set of anchors $\mathbb{A}$ as follows:

1. Select a subset $\mathbb{A}$ of the set $\mathbb{X}$
2.  Let $E$ be the encoder
3. Let $SIM$ be a similarity function
4. The relative representations are the computed as:
$$ r_x(i) = \left(S(e_{x^{i}},a_{x^{1}}), S(e_{x^{i}},a_{x^{2}}), ...\right) $$
All the experiments conducted use _Cosine Similarity_ as similarity measure.
# Experiment 1: Random Anchors

The following animation shows the relative representation over 100 randomly chosen anchors for $M_1$ and $M_2$ respectively, as well as the difference between corresponding points in $M_1$ and $M_2$.
# Experiment 2: Farthest-First Anchors

Similar to the animations above, this animations shows the relative representations using farthest-first anchors of 100 randomly initialiyed points.

The difference between corresponding points in $M_1$ and $M_2$ is shown below.