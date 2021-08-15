---
title: "Discretized DreamerV2 Evaluation"
excerpt: "We fully discretize DreamerV2 and evaluate the discrete representation learned by the model. Work in progress."
collection: portfolio
---

DreamerV2 is a model-based RL agent that plays Atari games. The latent representation consists of both continuous and discrete components, where the discrete component is represented as 32 categorical variables, each with 32 categories. In this work we fully discretize DreamerV2 by only providing the discrete component to the policy, reward predictor, and image decoder.

![Latent heatmap](https://ellamorgan.ca/images/latent_heatmap.png)

Here is a heatmap representing usage of the discrete latent space over several games of Pong. This is a sum of all 32x32 latent states that occur over multiple evaluations, brighter squares indicate a frequently activated category. Many categories are black, indicating low activity. The majority of these black squares are actually 0, they were never activated over the course of several games.

Adding more to this page later.
