---
layout: post
title: "Angry Birds: Divide the target in four parts."
date: 2015-05-04 03:10:00
categories: [ Artificial Intelligence, Game ]
tags: [ia, angrybirds, java, random]
image: assets/images/posts/2015-05-04-angry-birds-random-agent-improved.png
excerpt: Continuing the last post I'm trying to improve the random agent to obtain a best score.

---

## Continuing the last post

If the score is less than the minimum necessary, so I keep the target object and the score in a list to after identify the better targets, otherwise will be save a text file with the level, the shot configuration and the release point for the next time that the algorithm will runs to know which screen item will be the target to obtain the same score.

The first shot was realized in all objects using with reference the center of target, but in sometimes the center isn't the best place to shot, so after execute all shots and don't get the minimum score to win, so the target is split in four parts and another shots will be execute in each part, to try another shot further up, down, left or right to get a best shot, like this:

```java
ABObject t1 = new ABObject(ab.getBounds(), ab.getType(), ab.getId());
t1.setLocation((int) t1.getMinX(), (int) t1.getMinY());
t1.setSize((int) (t1.getWidth() / 2), (int) (t1.getHeight() / 2));

ABObject t2 = new ABObject(ab.getBounds(), ab.getType(), ab.getId());
t2.setLocation((int) (t2.getMinX() + novo2.getWidth() / 2), (int) t2.getMinY());
t2.setSize((int) (t2.getWidth() / 2), (int) (t2.getHeight() / 2));

ABObject t3 = new ABObject(ab.getBounds(), ab.getType(), ab.getId());
t3.setLocation((int) t3.getMinX(), (int) (t3.getMinY() + t3.getHeight() / 2));
t3.setSize((int) (t3.getWidth() / 2), (int) (t3.getHeight() / 2));

ABObject t4 = new ABObject(ab.getBounds(), ab.getType(), ab.getId());
t4.setLocation((int) (t4.getMinX() + t4.getWidth() / 2), (int) (t4.getMinY() + t4.getHeight() / 2));
t4.setSize((int) (t4.getWidth() / 2), (int) (t4.getHeight() / 2));
```

Sometimes need more than one shot to win, because how more high level, more complex is organized the screen objects. Using the same list of scores obtained after execute the shots, the algorithm begin to combine two or more shots to obtain three stars. 

After exhausting all possibilities and don't get the three start, so is saved the best shot and go to the next level. Passing the levels, all data is saved and the knowledge is expanded so the algorithm can stop and restart in any moment and to continue at the same point. So I leave the algorithm running for some hours and it get three start until level 17.

## Comparison between the agents

To identify the learning level from proposed agent, was realized a comparison with the example agent called Naive Agent available by the AI Birds organization. The table below show the scores obtained between the agents:

| Level | Naive Agente | Random Agent |
|-|-|-|
|1|29800|32170|
|2|43290|62370|
|3|40510|41900|
|4|22280|37580|
|5|55560|66030|
|6|24400|41370|
|7|22770|45900|
|8|34300|54830|
|9|33360|50810|
|10|43360|72890|
|11|37780|55870|
|12|56030|52880|
|13|23760|49030|
|14|55640|86570|
|15|41340|55400|
|16|51040|68790|
|17|40180|56230|
|18|52420||
|19|32610||
|20|47190||
|21|60500||

Comparing both agents is possible see the difference between the scores, despite of Naive Agent be more fast, because don't need the training process, the random agent learn better in which object shot to get the higher score.

## Conclusion and future improvements

The random agent need more initial time to generate the knowledge, because the training execute shot in all objects until find a best shot to get three stars. So, after training moment and knowledge generated if running the algorithm again, it know how to get three stars in any learned level.

This is a way to provide intelligence in the algorithm, but is restricted to play only with the levels that learn, if I try to play another episode or level, need to wait training. So is good, but I think that is not the best way to learn. Another algorithms can use reinforcement learning like Q Learning, but my approach ramains the same.

Some future improvements are suggested to improve the knowledge from intelligent agent like: After define the target, have any obstacle in the middle of the trajectory preventing the collision?; After collision the bird go back in what direction, this can cause more damage?; The tap time is random, but can verify the distance to calculate the better moment to action the bird.