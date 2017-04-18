---
layout:     post
title:      Troubleshooting Docker - tips for Mac users
date:       2017-04-04
summary:    If you are a heavy user of docker on Mac read on to save yourself at least a couple of hours of pain.
categories: docker
---

![whale with bandaid]({{ site.url }}/images/whale-bandaid.png){: .image-featured }

If you are a heavy user of docker on Mac read on to save yourself _at least_ a couple of hours of pain.

__Disclaimer:__ if your are not working on big projects this advice will, most likely, not be relevant to you.
If you are you might have already had all these issues :)

I am a heavy user of docker for a lot of projects and have experienced joy but also some pain derived from that.
Here are a few tricks that might save you a few hours if you are using docker on Mac.

## Increase docker memory

This might seem like a poor tip, but trust me it is crucial!
Here's the deal, you start using docker and everything works great.
Then your project grows, you have more microservices running in containers and all of a sudden your start having problems.

* First problem I experienced was that my containers started dying without any warning. I have one project making calls to services running on containers and all of a sudden the calls start failing. I check and the container had died...
* Second problem, things start getting really slow. I felt this most when running migrations on databases running inside containers. This operation would take many times longer than it should and more often than not the container would just die halfway through the process.

To fix both those issues:
Go to 'Preferences' > 'Advanced' and increase the memory that docker can use. By default it is 2 GB and I put it at 7GB.

![docker preferences]({{ site.url }}/images/docker-preferences.png){: .center-image }

![docker memory]({{ site.url }}/images/docker-memory.png){: .center-image }

__Pro tip:__ when I updated docker they changed this preference back to 2GB with no warning. This was very annoying because it put me back chasing my tale trying to understand why things were failing again, all the while thinking that the memory was as I had set it. So be sure to check out that preference after you update docker.

## Periodically remove images

At one point you might find you are unable to start database containers.

If you run the container as a daemon you don't see the problem, but if you don't you might find it complaining about lack of space - 'No space left on device' - note that this is, most likely, not related to lack of free space in the disk.

In this case you might need to remove a few docker images from your machine.
There is a limit in the space you can use for those and they can eat up quite a lot.

List images:

{% highlight js %}
docker images
{% endhighlight %}

Remove them using their image ID:

{% highlight js %}
docker rmi IMAGE_ID
{% endhighlight %}

You can also just [remove all untagged images](http://jimhoskins.com/2013/07/27/remove-untagged-docker-images.html){:target="_blank"}:

{% highlight js %}
docker rmi $(docker images -a | grep "^<none>" | awk '{print $3}')
{% endhighlight %}

## Get back your disk space

If you are on a mac the tip above is like a band aid for a broken leg. Eventually you'll see the disk space issue again. This is because there is a [bug on docker for mac that causes the Docker.qcow2 file to never shrink](https://github.com/docker/for-mac/issues/371){:target="_blank"}.
Hopefully by the time you read this the problem will have been solved. Check [github](https://github.com/docker/for-mac/issues/371){:target="_blank"} to find out the status of the issue.

In case it is not fixed, what you can do is to follow one of the many workarounds suggested on the issue thread.
I followed @wbednarski's suggestion to delete Docker.qcow2. __Notice you can only follow this suggestion if you are OK with removing all image/containers__.
If so stop docker and then run:

{% highlight js %}
docker rm $(docker ps -a -q)
docker rmi $(docker images -q)
docker volume rm $(docker volume ls |awk '{print $2}')
rm -rf ~/Library/Containers/com.docker.docker/Data/*
{% endhighlight %}

If you adopt this workaround, next time you start your docker containers all images will have to be pulled again.



