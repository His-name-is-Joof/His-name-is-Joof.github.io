---
layout: post
title:  "Space Colonization"
date:   2015-02-03 19:09:17
---

Space Colonization \[[2007 paper\]]( http://algorithmicbotany.org/papers/colonization.egwnp2007.html) is an algorithm that is used to generate 3D models of trees (the nature kind; think speedtree) and is an improvement over L-systems for more natural looking trees. 

The basic premise is that a point cloud of endpoints exists with thousands of distributed points and a tree is grown from a 'seed' point towards those points. The natural feel exists because all those growing branch points begin to compete for space as opposed to being pre-set like an L-system.

It's an insteresting project to build for several reasons:

    \- It looks cool

    \- It's highly optimizable

    \- It can be highly parallel

    \- I wanted to make it real-time

I personally like it for another reason; I over-engineered it and never finished it. It serves as a lesson as to how many things it's feasible to fit into a single project. I hope to finish it one day, but for now it's left incomplete. C++14, boost, templating, CUDA and numerous optimizations exist partly used or not quite accomplished within this project. It was an excellent way to learn, but I think my future projects will be more iterative in what I learn and how I build them.


Optimizing:

    The algorithm first calls for every end-point to search iteratively through all the existing branch-points to find which is closest and then average the vectors of all the closest points to choose which direction the next branch will grow in. This is of order O(end-points * branch-points) so I needed to find a way to reduce the search space. Using R-trees, a form of space partitioning, I was able to guesstimate which end-points were going to be closer to which branch-points. I then drew a rectangular prism around the branch point as a way to remove more of the end-points. Lastly, I did a more costly distance comparison from the branch to the endpoints and emplaced them in a table \[endpoint -> closest branch\] if the distance was shorter than what already existed. The table allows the algorithm to run in parallel for each branch. Then each table entry is moved to another table of \[branch-point -> growth-vector\] where the vectors towards each en\-point from the branch are averaged together. It may be useful to have an intermediary table where \[branch-point -> vector\<end-points\>\] so that all the vectors can be averaged in CUDA more efficiently.

    This is where I left off; however, the algorithm is setup to be highly parallel if continued. Given infinite time I would first implement a series of consumer/producer style queues then go back and replace as many of the operations as possible with CUDA code (depending on the size of the point-cloud and hardware). I would also remove most of the templating simply because it reduces the readability of the code and removes the guarentees that I get from using specific containers. 

Where I failed:

    It never got finished. In comparison, it shouldn't take much time to go back and finish the implementation (a few days work maybe), but I got caught up playing with C++14 features and templating which while cool is ultimately useless for such a specialized project. On the other hand, boost provided me with an implementation for R-trees which would have taken me a good while to figure out and the code in boost is top-notch. I would still need to tie it in with a renderer and find a good measure for the width of each branch and how to deal with seams, but I dream of an entire tree made of tiny models of Nicholas Cage's head. 
