---
title: "Discretized DreamerV2 Evaluation"
excerpt: "We fully discretize DreamerV2 and evaluate the discrete representation learned by the model. Work in progress."
collection: portfolio
---

In progress of updating this.

What is DreamerV2?
-----

DreamerV2 is a model-based reinforcement learning agent trained on the Atari domain. The latent representation consists of both continuous and discrete components, where the discrete component is represented as 32 categorical variables, each with 32 categories. The preceeding diagram represents the original architecture.

<img src="https://ellamorgan.ca/images/dreamerv2.png" height="300">

In the diagram above, the purple dots along the hidden state represent the concatenation of continuous and discrete components. In is work we fully discretize DreamerV2 by only providing the discrete component to the policy, reward predictor, and image decoder. This discrete state is what is being represented at the right of the diagram, it represents information obtained from the encoded input image as well as the continuous hidden memory state.

Latent Representation
-----

<img src="https://ellamorgan.ca/images/latent_heatmap.png" height="200">

Here is a heatmap representing usage of the discrete latent space over several games of Pong. This is a sum of all 32x32 latent states that occur over multiple evaluations, brighter squares indicate a frequently activated category. Many categories are black, indicating low activity. The majority of these black squares are actually 0, they were never activated over the course of several games. This demonstrates that the latent space is quite sparse.

Interpolation Experiments
-----

The first things tested were interpolations between states, we found interpolations by hamming distance to not be meaningful. Since each categorical variable represents a single category, we simply interpolate from latent A to latent B by changing the categorial variables of latent A to match the categories of latent B one variable at a time. We see that this has no meaningful result in the gif below.

<img src="https://ellamorgan.ca/images/interpolation.gif" height="200">

It can be seen that 'in-between' states don't have much meaning. By the heatmaps we saw that the usage of the latent space is quite sparse. It appears that meaningful states are sparsely distributed throughout the latent space, with seemingly no meaningful interpolations between them.

Score Representation
-----

Next, we investigate how the score information is embedded in the latent state. Although score shouldn't be relevant for the policy, this information is required by the decoder for reconstructing the input image. First, we group latents based on the left and right scores. Next, we sum all latents in each group, so that we have sums corresponding to each score on each side. We also sum over all groups, and all latent sums are normalized so that the sum over each categorical variable is 1. Then we subtract the average over all latents from each score, so that we may compare how each score differs from the average.

<img src="https://ellamorgan.ca/images/score_differences.png" height="200">

This doesn't really convey when the categories activated are 'rare', any better way to explore this?

Probing for Information
-----

The next experiment performed probes the latent representation for insight on how information is represented. We assume that the most relevant factor for the policy is the location of the ball and it's velocity. We generate data that consists of latent states and information on the ball location and horizontal and vertical velocity. We then train neural networks with varying layers on this data. The results show that a linear probe performs quite poorly, and performance increases as the number of layers in the network are increased. This demonstrates that the information is present in the latent representation, but is not linearly separable and likely embedded in a highly complex manner.

I think a better experiemnt would be predicting where the ball will meet the paddle, I should probe for that information instead.
