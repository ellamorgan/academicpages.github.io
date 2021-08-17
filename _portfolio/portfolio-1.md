---
title: "Discretized DreamerV2 Evaluation"
excerpt: "We fully discretize DreamerV2 and evaluate the discrete representation learned by the model. Work in progress."
collection: portfolio
---

In progress of updating this.

What is DreamerV2?
-----

DreamerV2 is a model-based reinforcement learning agent trained on the Atari domain. The latent representation consists of both continuous and discrete components, where the discrete component is represented as 32 categorical variables, each with 32 categories. The preceeding diagram represents the original architecture.

![DreamerV2 Architecture](https://ellamorgan.ca/images/dreamerv2.png)

In the diagram above, the purple dots along the hidden state represent the concatenation of continuous and discrete components. In is work we fully discretize DreamerV2 by only providing the discrete component to the policy, reward predictor, and image decoder. This discrete state is what is being represented at the right of the diagram, it represents information obtained from the encoded input image as well as the continuous hidden memory state.

Latent Representation
-----

![Latent heatmap](https://ellamorgan.ca/images/latent_heatmap.png)

Here is a heatmap representing usage of the discrete latent space over several games of Pong. This is a sum of all 32x32 latent states that occur over multiple evaluations, brighter squares indicate a frequently activated category. Many categories are black, indicating low activity. The majority of these black squares are actually 0, they were never activated over the course of several games. This demonstrates that the latent space is quite sparse.

Interpolation Experiments
-----

The first things tested were interpolations between states, we found interpolations by hamming distance to not be meaningful. Since each categorical variable represents a single category, we simply interpolate from latent A to latent B by changing the categorial variables of latent A to match the categories of latent B one variable at a time. We see that this has no meaningful result in the gif below.

![Interpolation](https://ellamorgan.ca/images/interpolation.gif)

It can be seen that 'in-between' states don't have much meaning. By the heatmaps we saw that the usage of the latent space is quite sparse. It appears that meaningful states are sparsely distributed throughout the latent space, with seemingly no meaningful interpolations between them.
