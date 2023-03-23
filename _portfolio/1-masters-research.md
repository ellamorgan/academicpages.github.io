---
title: "Aligning Planning Models with Real-World Observations"
excerpt: "This work aims to bridge the gap between noise and structure by aligning visualizations of planning states to the underlying state space structure. Work completed as part of my Master's at Queen's University."
collection: portfolio
---


## Problem setting

In this work, we aim to improve the predictions of a computer vision model through aligning a sequence of images and their predictions with a provided graph structure of the problem. This problem is highlighted below.

<img src="https://ellamorgan.ca/images/state_graph.gif" width=700>

On the left is an example of the kind of data we're looking at: sequences of images showing actions taking place in an environment. These are examples of the images the computer vision model is predicting the underlying state of. In this environment, the actions are the agent picking up objects (represented by the number 0) and moving between rooms. The robot can pick up the object (represented by the object moving to the bottom left corner outside the rooms) and place it in the robot's current room. The idea is that the state should be recognized as being the same no matter where in the room the robot or the object is located - all that matters is the room they are in. Furthermore, the appearance of the object changes between states - we sample this from the MNIST dataset to obtain variations.

The graph on the right shows the state space - this is essentially a model of how we know the environment to behave. The state of the image on the left is highlighted in grey. In this graph, there is an edge from one state to another if there is a valid transition between those states, in this case if there is an action in the corresponding planning domain of the problem that transitions from one state to the other. Given the sequence of images such as those shown in the gif, we expect that the corresponding states form a path in the graph, that is, there is a valid transition occuring between each sequential pair of states in the sequence.


## Goal

Given a sequence of images representing states in a state-space, we want to train a model to predict the underlying state for each image, while aligning the sequence of predictions with a given state-space graph which dictates which transitions are valid in the environment.


## The data

First, we generate some data using planning domains as a testbed.

<img src="https://ellamorgan.ca/images/grid.png" width=700>

Here, both rows show a sequence of the same 3 states. In these examples the objects are sampled from the Fashion MNIST dataset, each object represented as a different item of clothing. Each image on the top is the same state as the one below it, but visually they appear different due to sampling different items of clothing and the position of objects. The challenge here is for the model to learn concepts like "t-shirt" and "pants" to differentiate the objects, as well as understanding which room an object is in when the objects position in the room can change between states.

<img src="https://ellamorgan.ca/images/blocksworld.png" width=700>

Here, labeled blocks are moved around on a table. The model needs to differentiate between the blocks, and understand what state the specific arrangement of blocks corresponds to. Above, the labels are numbers sampled from MNIST. Below, they are represented by different articles of clothing sampled from Fashion MNIST for increased difficulty.

<img src="https://ellamorgan.ca/images/elevator.png" width=700>

This domain models people taking an elevator, where they are shown waiting at different floors on the left half, and the elevator is shown on the right half. Above, we highlight the mechanics using stick figures, showing a person on the 2nd floor getting on the elevator and getting off at the first floor. Below shows what the equlivalent states look like in our training data: representing different people as different clothing items, and assigning them locations within the waiting area or elevator randomly to add difficulty disambiguating between states.


## The model

<img src="https://ellamorgan.ca/images/contrastive_model.png" width=700>


## Aligning algorithms

<img src="https://ellamorgan.ca/images/hmm_trace.png" width=700>


This research is still a work in progress. Some initial research and results can be seen from [this poster](https://ellamorgan.ca/files/research-poster.pdf) I recently presented at Vector's Research Symposium.
