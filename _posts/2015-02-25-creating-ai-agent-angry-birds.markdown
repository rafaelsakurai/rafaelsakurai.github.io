---
layout: post
title: "Creating an intelligent agent to play Angry Birds in Java"
date: 2015-02-25 10:47:00
categories: [ Artificial Intelligence, Game ]
tags: [ia, angrybirds, java]
image: assets/images/posts/2015-02-25-creating-ai-agent-angry-birds-aibirds.png
excerpt: This post explain how to configure and start to create an intelligent agent to play Angry Birds.

---

[Angry Birds AI Competition](http://aibirds.org/) its an event in which we can create intelligent agent to play Angry Birds. This competition occur every year, in a different city around the World, since 2012, and the event is affiliated to the [Internation Joint Conference on Artificial Intelligence](http://ijcai-15.org/) proving to be a good place to show your knowledge.

So, in this post, I will explain how to configure the environment to play Angry Birds and how to start creating an intelligent agent using Java.

How things will work? To play Angry Birds you can simply open [http://chrome.angrybirds.com/](http://chrome.angrybirds.com/) on Google Chrome, and the Ai Birds provide a plugin from Chrome that identify the components of screen (like birds, pigs, slingshot, wood, ice, stone etc) and execute the commands send via Java.

For a fast start up, access the page [http://aibirds.org/basic-game-playing-software/getting-started.html](http://aibirds.org/basic-game-playing-software/getting-started.html) and follow the instructions. At the end, you will have the `abV1.32.zip` file that contains all files including Google Chrome plugin, a java executable `ABSoftware.jar` (that provide the comunication between Java and Chrome plugin), and an source code of a sample agent in Java that allow understand the basic of how to create your own Intelligent Agent that play Angry Birds.

So far, you can be able to execute the command `java -jar ABSoftware.jar -na 1` and see the screen of Angry Birds playing alone.

Inside of `abV1.32.zip` file you can find the structure from example of a simple agent that play Angry Birds. Look for a folder name `src` that you can find the source code in Java.

## Image segmentation

Inside of package `ab.vision` you can find two ways of image segmentation that get the image of game screen, identify and return the components of screem. It is possible to see the output just using the command with flag `-showMBR`. Minimum bounded rectangle (MBR) use rectangle to represent each component of screen, like show in next image:

<figure>
    <a href="/assets/images/posts/2015-02-25-creating-ai-agent-angry-birds-showMBR.png"><img src="/assets/images/posts/2015-02-25-creating-ai-agent-angry-birds-showMBR.png" alt="Image Segmentation MBR."></a>
</figure>

The `ab.vision.VisionMBR.java` class contains the implementation of MBR and you can see how this work.

Another option to get a better components position is using `-showReal`, is a bit slower, but you can get a more specific position of each component, like show in next image:

<figure>
    <a href="/assets/images/posts/2015-02-25-creating-ai-agent-angry-birds-showReal.png"><img src="/assets/images/posts/2015-02-25-creating-ai-agent-angry-birds-showReal.png" alt="Image Segmentation Real."></a>
</figure>

Using -showReal its possible to see a line that represent the floor and the components have a more detailed scale. The `ab.vision.VisionRealShape.java` class contains the real implementation and you can see how this work.

After all, if you don't like any of the implementations just create your own class that made the image segmentation.

## Demo agent

This source contains a demo intelligent agent that allow understand the structure of game and create your own agent.

The `ab.demo.other.ActionRobot` class will be connected to the Chrome plugin to execute the operations on real game. With this class you can go to the main game level, load a level, identify which bird (red, yellow, black, blue and white) is in sling, execute zoom in and zoom out, and execute the slingshot.

Look the `ab.demo.NaiveAgent` class, it's a Runnable, so all code will start from `run()` method, this implementation will load a level, try to solve the game (playing the bird to destroy the pigs), save the score, and understand if the agent won ou lost the game.

The `solve()` method try to understand the scene and execute some action. This method use the `ab.vision.Vision` to process the image and identify the slingshot `vision.findSlingshotMBR()`, after that identify the pigs `vision.findPigsMBR()` and made a simple calculate to estimate the trajectory of the bird in relation to pigs position.

So now the fun begins, try to change the `solve()` method and create a better intelligent agent. Next post I will show my initial progress.
