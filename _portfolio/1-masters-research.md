---
title: "Aligning Planning Models with Real-World Observations"
excerpt: "This work aims to bridge the gap between noise and structure by aligning visualizations of planning states to the underlying state space structure. Work completed as part of my Master's at Queen's University."
collection: portfolio
---

## Problem setting

In this work, we aim to improve the predictions of a computer vision model through aligning a sequence of images and their predictions with a provided graph structure of the problem. This problem is highlighted below.

<img src="https://ellamorgan.ca/images/task.jpg" width=700>

In the four images at the top of the figure we highlight the kind of data we're looking at: sequences of images showing actions taking place in an environment. These are examples of the images the computer vision model is predicting the underlying state of. In this environment, the actions are the agent picking up articles of clothing and moving between rooms. In the first state, the agent is holding a shirt, as shown by the shirt being in the bottom left corner. In the next state the agent places the shirt in the room, then moves between rooms in the next two states. Throughout the sequence of images the appearance of the shirt keeps changing. The idea here is that the model should learn to recognize concepts such as "there is a shirt in the top left room" (as shown in images 2-4), so we want to model to be robust towards learning which visual differences result in a different state (such as which room the shirt is located in), versus which differences are irrelevant (such as the appearance of the shirt - all that matters is that it is a shirt).

The bottom graphs show the state space - this is how we know the environment to behave. The actual states of the above images are highlighted. There is a directed edge from one state to another in the state space if the second state is reachable from the first through a single action. For example, the second image shown is a state reachable from the first image because the single action taken was "place the shirt in the room", we don't expect that the agent should be able to both place the shirt and move at the same time, as in our example this goes against our knowledge of how the environment should work. The general idea is we have a graph of how we can transition between states, and we expect the sequence of images to align with this understanding of the world. We highlight the states in the state space graph that correspond with the states of the images, showing the path that this sequence of images takes through the graph.

<img src="https://ellamorgan.ca/images/state_graph.gif" width=700>

<img src="https://ellamorgan.ca/images/state_space.jpg" width=700>


## The data

Below are examples of the generated data.

<img src="https://ellamorgan.ca/images/grid.png" width=700>

<img src="https://ellamorgan.ca/images/blocksworld.png" width=700>

<img src="https://ellamorgan.ca/images/elevator.png" width=700>

## The model

<img src="https://ellamorgan.ca/images/contrastive_model.png" width=700>

## Aligning algorithms

<img src="https://ellamorgan.ca/images/hmm_trace.png" width=700>


This research is still a work in progress. Some initial research and results can be seen from [this poster](https://ellamorgan.ca/files/research-poster.pdf) I recently presented at Vector's Research Symposium.
