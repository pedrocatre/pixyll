---
layout:     post
title:      Troubleshooting Docker - tips for Mac users
date:       2017-04-01
summary:    If you are a heavy user of docker on Mac read on to save yourself at least a couple of hours of pain.
categories: docker
---

![whale with bandaid]({{ site.url }}/images/whale-bandaid.png){: .image-featured }

If you are a heavy user of docker on Mac read on to save yourself _at least_ a couple of hours of pain.

__Disclaimer:__ if your are not working on big projects this advice will, most likely, not be relevant to you. If you are you might have already had all these issues :)

I am a heavy user of docker for a lot of projects and have experienced joy but also some pain derived from that.
Here are a few tricks that might save you a few hours if you are using docker on Mac.

## Increase docker memory

This might seem like a lame tip, but trust me it is crucial! Here's the deal, you start using docker and everything works great. Then your project grows, you have more microservices running in containers and all of a sudden your start having problems.

* First problem I experienced was that my containers started dying without any warning. I have one project making calls to services running on containers and all of a sudden the calls start failing. I check and the container had died...
* Second problem, things start getting really slow. I felt this most when running migrations on databases running inside of containers. This operation would take many times longer than it should and more often than not the container would just die halfway thought the process.

To fix both those issues:
Go to Preferences > Advanced and increase the memory that docker can use. By default it is 2 GB and I put it at 7GB.

![docker preferences]({{ site.url }}/images/docker-preferences.png){: .center-image }

![docker memory]({{ site.url }}/images/docker-memory.png){: .center-image }

__Pro tip:__ when I updated docker they changed this preference back to 2GB with no warning. This was very annoying because it put me back chasing my tale trying to understand why things were failing again, all the while thinking that the memory was as I had set it. So be sure to check out that preference after you update docker.

## Periodically remove images

At one point when I tried starting database containers they would simply not start.

If you run the container as a daemon you don't see the problem, but if you don't you might find it complaining of lack of space - note that it was not related to lack of free space in the disk.

In this case you might need to remove a few docker images from your laptop.
There is a limit in the space you can use for those and they can eat up quite a lot.

List images:

{% highlight js %}
docker images
{% endhighlight %}

Remove them using their image ID:

{% highlight js %}
docker rmi IMAGE_ID
{% endhighlight %}


