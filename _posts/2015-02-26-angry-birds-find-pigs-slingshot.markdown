---
layout: post
title: "Angry Birds: Find the pigs and execute a shot."
date: 2015-02-26 11:07:00
tags: ia angrybirds java
published: true
summary: This post explain how to identify in Angry Birds the pigs and execute a shot.
image: 2015-02-26-angry-birds-find-pigs-slingshot.png
---

<div class="span2">
  <a href="{{ post.url }}" >
    <img border="0" width="100%" height="100%" src="/img/posts/2015-02-26-angry-birds-find-pigs-slingshot.png" alt="Angry Birds">
  </a>
</div>

<br/>

<h2>Identify the slingshot and pigs</h2>

After configure the environment is time to programming, so let's start to understand how to identify the slingshot and pigs. To do this, we need a `Vision` object, like this:

{% highlight java %}
BufferedImage screenshot = ActionRobot.doScreenShot();
Vision vision = new Vision(screenshot);
{% endhighlight %}

The `ActionRobot` can communicate with Chrome plugin and make a screenshot from Angry Birds game, with this screenshot we create a `Vision` object that will be used to analyze the screenshot and find the slingshot:

{% highlight java %}
Rectangle sling = vision.findSlingshotMBR();
{% endhighlight %}

So, the slingshot is a rectangle like this `java.awt.Rectangle[x=185,y=317,width=17,height=66]` with your position on screen.

And we also can find the pigs:

{% highlight java %}
List<ABObject> pigs = vision.findPigsMBR();
{% endhighlight %}

Each pig is returned in a list of `ab.vision.ABObject`, a class that extends `java.awt.Rectangle` and contains the type of component (Ground, Hill, Sling, Bird, Pig, Ice, Wood, Stone, etc). When you print a ABObject you have some like this `ab.vision.ABObject[x=537,y=291,width=11,height=10]` if you are using `findPigsMBR()`, just the rectangle of the component; or you can see some like this `Circ: id:1 type:Pig r:  7,250 at x:543,5 y:294,0`, if you are using `findPigsRealShape()` with the information of type, angle, etc.

<h2>Shot</h2>

With these informations we know the position of slingshot and the position of pig, so we can try to estimate the trajectory of shoot the bird in direction of the pig.

<div class="span2">
  <a href="{{ post.url }}" >
    <img border="0" width="100%" height="100%" src="/img/posts/2015-02-26-angry-birds-find-pigs-slingshot2.png" alt="Pull the sling.">
  </a>
</div>

<br/>

To execute the shot, we will use `ab.demo.other.Shot` class, the constructor receive as parameter the position x and y from slingshot, the positions x and y from release point, a value zero (wait time before launch the bird), and a time that execute the tap in case when the bird have same effect like split into three or increase speed.

For example:

{% highlight java %}
Shot shot = new Shot(193, 328, -100, 50, 0, 0);

ActionRobot aRobot = new ActionRobot();
aRobot.cshoot(shot);
{% endhighlight %}

Create a Shot object that inform to start the pull in position `(193, 328)`, that correspond of slingshot position, and the value `-100` say that need pull back in `x axis` and the value `50` say that need to pull down in `y axis`. To execute the shot just need to call `cshoot(shot)` method from `ActionRobot` class.

<div class="span2">
  <a href="{{ post.url }}" >
    <img border="0" width="100%" height="100%" src="/img/posts/2015-02-26-angry-birds-find-pigs-slingshot3.png" alt="Slingshot.">
  </a>
</div>

<br/>

But, how do I know how many I need to pull back and pull down the slingshot?