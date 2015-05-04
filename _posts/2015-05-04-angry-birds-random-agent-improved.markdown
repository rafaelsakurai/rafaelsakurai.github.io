---
layout: post
title: "Angry Birds: Divide the target in four parts."
date: 2015-05-04 03:10:00
tags: ia angrybirds java random
published: true
summary: Continuing the last post I'm trying to improve the random agent to obtain a best score.
image: 2015-05-04-angry-birds-random-agent-improved.png
---

<h3>Continuing the last post</h3>

If the score is less than the minimum necessary, so I keep the target object and the score in a list to after identify the better targets, otherwise will be save a text file with the level, the shot configuration and the release point for the next time that the algorithm will runs to know which screen item will be the target to obtain the same score.

The first shot was realized in all objects using with reference the center of target, but in sometimes the center isn't the best place to shot, so after execute all shots and don't get the minimum score to win, so the target is split in four parts and another shots will be execute in each part, to try another shot further up, down, left or right to get a best shot, like this:

{% highlight java %}
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
{% endhighlight %}

Sometimes need more than one shot to win, because how more high level, more complex is organized the screen objects. Using the same list of scores obtained after execute the shots, the algorithm begin to combine two or more shots to obtain three stars. 

After exhausting all possibilities and don't get the three start, so is saved the best shot and go to the next level. Passing the levels, all data is saved and the knowledge is expanded so the algorithm can stop and restart in any moment and to continue at the same point. So I leave the algorithm running for some hours and it get three start until level 17, like show in next image:

<div class="row">
  <div class="span12" align="center">
    <a href="{{ post.url }}" >
      <img border="0" width="80%" height="100%" src="/img/posts/2015-05-04-angry-birds-random-agent-improved.png" alt="Angry Birds">
    </a>
  </div>
</div>

<br/>

<h3>Comparison between the agents</h3>

To identify the learning level from proposed agent, was realized a comparison with the example agent called Naive Agent available by the AI Birds organization. The table below show the scores obtained between the agents:

<table class="table table-striped">
  <tr>
    <th>Level</th><th>Naive Agent</th><th>Random Agent</th>
  </tr>
  <tr><td>1</td><td>29800</td><td>32170</td></tr>
  <tr><td>2</td><td>43290</td><td>62370</td></tr>
  <tr><td>3</td><td>40510</td><td>41900</td></tr>
  <tr><td>4</td><td>22280</td><td>37580</td></tr>
  <tr><td>5</td><td>55560</td><td>66030</td></tr>
  <tr><td>6</td><td>24400</td><td>41370</td></tr>
  <tr><td>7</td><td>22770</td><td>45900</td></tr>
  <tr><td>8</td><td>34300</td><td>54830</td></tr>
  <tr><td>9</td><td>33360</td><td>50810</td></tr>
  <tr><td>10</td><td>43360</td><td>72890</td></tr>
  <tr><td>11</td><td>37780</td><td>55870</td></tr>
  <tr><td>12</td><td>56030</td><td>52880</td></tr>
  <tr><td>13</td><td>23760</td><td>49030</td></tr>
  <tr><td>14</td><td>55640</td><td>86570</td></tr>
  <tr><td>15</td><td>41340</td><td>55400</td></tr>
  <tr><td>16</td><td>51040</td><td>68790</td></tr>
  <tr><td>17</td><td>40180</td><td>56230</td></tr>
  <tr><td>18</td><td>52420</td><td></td></tr>
  <tr><td>19</td><td>32610</td><td></td></tr>
  <tr><td>20</td><td>47190</td><td></td></tr>
  <tr><td>21</td><td>60500</td><td></td></tr>
</table>

Comparing both agents is possible see the difference between the scores, despite of Naive Agent be more fast, because don't need the training process, the random agent learn better in which object shot to get the higher score.

<h3>Conclusion and future improvements</h3>

The random agent need more initial time to generate the knowledge, because the training execute shot in all objects until find a best shot to get three stars. So, after training moment and knowledge generated if running the algorithm again, it know how to get three stars in any learned level.

This is a way to provide intelligence in the algorithm, but is restricted to play only with the levels that learn, if I try to play another episode or level, need to wait training. So is good, but I think that is not the best way to learn. Another algorithms can use reinforcement learning like Q Learning, but my approach ramains the same.

Some future improvements are suggested to improve the knowledge from intelligent agent like: After define the target, have any obstacle in the middle of the trajectory preventing the collision?; After collision the bird go back in what direction, this can cause more damage?; The tap time is random, but can verify the distance to calculate the better moment to action the bird.

<div class="row"> 
  <div class="span12 column">
      <p class="pull-right">{% if page.previous.url %} <a href="{{page.previous.url}}" title="Previous Post: {{page.previous.title}}"><i class="icon-chevron-left"></i></a>   {% endif %}   {% if page.next.url %}  <a href="{{page.next.url}}" title="Next Post: {{page.next.title}}"><i class="icon-chevron-right"></i></a>   {% endif %} </p>  
  </div>
</div>

<div class="row"> 
    <div class="span12 columns">    
    <h2>Comments Section</h2>
      <p>Feel free to comment on the post but keep it clean and on topic.</p> 
      <div id="disqus_thread"></div>
    <script type="text/javascript">
        /* * * CONFIGURATION VARIABLES * * */
        var disqus_shortname = 'rafaelsakurai';
        
        /* * * DON'T EDIT BELOW THIS LINE * * */
        (function() {
            var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
            dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
            (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
        })();
    </script>
    <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
    <a href="http://disqus.com" class="dsq-brlink">blog comments powered by <span class="logo-disqus">Disqus</span></a>
  </div>
</div>

<!-- Twitter -->
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src="//platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","twitter-wjs");</script>

<!-- Google + -->
<script type="text/javascript">
  (function() {
    var po = document.createElement('script'); po.type = 'text/javascript'; po.async = true;
    po.src = 'https://apis.google.com/js/plusone.js';
    var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(po, s);
  })();
</script>