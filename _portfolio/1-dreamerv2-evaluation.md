---
title: "Discretized DreamerV2 Evaluation"
excerpt: "We fully discretize DreamerV2 and evaluate the discrete representation learned by the model. Still in the process of updating this page."
collection: portfolio
---

## The DreamerV2 model

DreamerV2 is a reinforcement learning agent that builds a world model of it's environment, trained separately from the policy. The agent attains human-level performance on the Atari benchmark, and serves as the first model-based approach to do so. An interesting aspect of this world model is that it's partially discretized. The latent representation of the world model consists of a continuous memory portion that's passed on between states, and a discretized portion represented by 32 categorical variables, each of which has 32 categories. The world model architecture is demonstrated in the figure below, taken from the DreamerV2 paper.

<img src="https://ellamorgan.ca/images/dreamerv2.png" width=700>

The continuous latent $h_i$ is represented as the purple line, and the discrete latent $z_i$ is shown in green. In this figure, the purple dot connecting $h_i$ and $z_i$ is a concatenation operator, thus the latent state is not fully discretized as the paper might imply.

## Our objective
Our objective in this exploration study is evaluating the learned discrete latents. The goal is an evaluation of whether the discrete prior encourages 'disentanglement' in the learned representation. 

## Our modification
 
As the model is not actually fully discrete, we perform this study by first modifying the agent to have a fully discrete latent representation. This is done simply by changing the concatenation operation. Instead of concatenating continuous and discrete components and passing the concatenation on to the reward, decoder, and policy predictors, we only pass on the discrete component. The continuous hidden state $h_i$ is still passed along as a memory state, but it is no longer seen outside of the world model. The updated architecture is demonstrated in the diagram below.

<img src="https://ellamorgan.ca/images/dreamerv2_changes.jpg" width=700>

This modification inhibits performance of the model. Due to compute constraints a proper analysis was not performed, but after 25M world steps a perfect policy for Pong has not been learned with the agent not winning every point. The agent is capable of scoring against the AI in Pong for most points, so we continue on with exploring the learned representation even if imperfect, this is mainly an early analysis into whether anything promising can be obtained from this approach. 

## Experimental setup
 
We trained the discretized model to around 25M world steps on the domain Pong. No thorough study of the performance differences between the continuous + discrete vs. solely the discrete representation has been performed, but it's clear that simply removing the continuous part causes issues with training. Nonetheless, our objective is the study of what it is learning, and as it is capable of scoring against the game's AI we expect that there should be some useful information to be obtained from the learned representation.

To evaluate, we examine the discrete latents along with the input images provided to the model, thus we have frames of the gameplay along with the discrete internal states of the agent. The aim is then to find whatever insight we can from these latent states.

## Evaluating the latent space
The first question is to find the distribution of usage of the latent space. For this we take all latent states that occured over the course of ~160 games of Pong and we sum them, obtaining a count of how many times each variable category was activated over all observed games. The result is a surprisingly sparse image.

<img src="https://ellamorgan.ca/images/latent_heatmap.png">

Here each row represents a categorical variable, then columns correspond to the categories of each variable. Bright squares represent a frequently activated category, while dark squares indicate low activity. We note that the majority of squares appear black. Not only are they infrequently used, the actual counts tend to be 0. Thus instead of refering to rare and important events in the game, they are simply never utilized by the model. We note that each variable tends to have a smaller number of categories that it goes through.

## Changing single variables

When starting this experiment we were hopeful that the discrete categorical variables provided a 'disentanglement' of game concepts. To test this, we evaluated how the decoded image changes as a single categorical variable is varied. We take on categorical variable, iterate through all categories one at a time, and save the resulting decoded images as a gif to evaluate how it changes.

<img src="https://ellamorgan.ca/images/single_variable.gif">

We see that this has little meaning, hinting that concepts are encoded in the representation in a complex manner.

## Interpolation experiments

Next we evaluate the latent representation for its ability to interpolate between two distant states. A desirable property may be a smooth transition between two latents as we start from one and change the categorical variables over one by one to match the second latent, slowly transitioning to the latter state as we do so. We perform the experiment by doing just that, changing a single categorical variable of the first latent at a time to match the variables of the second latent. The results are demonstrated below.

<img src="https://ellamorgan.ca/images/interpolation.gif">

We find that there is little meaning in the interpolation.

## Score evaluation

The previous experiments hint at a complex embedding of game concepts. We explore this deeper by evaluting how game score information is stored in the representation. While this information isn't necessary for the policy, it is required to reconstruct the input image, thus must be encoded in the representation in some way. First we examine statistical differences in the average categorical activations for each score compared to the average utilization across all scores.

<img src="https://ellamorgan.ca/images/score_differences.png">

While it is not exactly clear what these differences mean, it's clear that the patterns displayed appear to differ between scores. Thus scores may be represented in the representation in a quite complex manner.

## Probing

Next, we probe the learned representation by training neural networks to predict a given concept given the latent state as input. Here we test whether we can predict the X and Y coordinates of the ball, and its horizontal and vertical velocity. Our hypothesis is that this information should be vital for the policy to accurately predict where to move the paddle, as the agent should be able to find the ball and compute its trajectory towards the paddle.

To gather the data we collect latent states that occur as the ball is moving towards the agent's paddle. We track the ball as it moves across the screen to determine its position and velocity, and only take those with a positive horizontal velocity. We then train several neural networks, starting with a linear layer that consists only of an input and an output layer, and increasingly add hidden layers up to 5 additional hidden layers. We see from the following results that performance scales with the number of layers, significantly increasing after a single hidden layer is added.

| Hidden Layers | X coord MAE | Y coord MAE | Horizontal velocity MAE | Vertical velocity MAE |
|:---|:---:|:---:|:---:|:---:|
| 0 | 4.35 | 8.41 | 1.40 | 2.08 |
| 1 | 1.19 | 3.01 | 0.74 | 1.47 |
| 2 | 0.93 | 2.62 | 0.62 | 1.42 |
| 3 | 0.74 | 1.94 | 0.56 | 1.37 |
| 4 | 0.70 | 1.67 | 0.56 | 1.30 |
| 5 | 0.62 | 1.40 | 0.53 | 1.27 |

## Conclusion and next steps

This exploration demonstrates that the discrete prior doesn't seem to provide human interpretable disentanglement. Although, only one domain is examined, it may be a limitation of the training, perhaps there is a better method or hyperparameters that would have led to improved training of the model. Perhaps there is some level of disentanglement that has no human percievable meaning.

This serves as an initial exploration of DreamerV2 that will be expanded upon in future work. We will explore inductive biases that can lead to better 'disentanglement' or interpolations between states, then perform similar experiments on those representations to see if there's any improvement.
