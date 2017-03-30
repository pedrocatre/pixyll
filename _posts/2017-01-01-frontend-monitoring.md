---
layout:     post
title:      Frontend Error Tracking
date:       2017-01-01
summary:    You can save hours when debugging users' issues by taking 10 minutes now to setup monitoring for your frontend!
categories: jekyll pixyll
---


> You can save hours when debugging users' issues by taking 10 minutes now to setup monitoring for your frontend! If you delay this you'll curse yourself later :)

Tl;dr:

* Monitor your frontend. It will make you aware of problems you did not known existed and it will save you time debugging and fixing issues. Seriously, the benefits of this are completely disproportional to the investment!
* Monitoring the frontend can be super easy and free to setup, so why not just do it? You won't regret it.

## Problem

Nowadays it is quite common for services to use monitoring/log tools (such as graylog, pingdom, and so on).
It is also common for engineers to be woken up by alerting systems (such as OpsGenie) to nurture their services.
However, most of that love and attention is usually focused on the backend, on core business functions.


_![meanwhile on the frontend]({{ site.url }}/pixyll/images/meanwhile-on-the-front-end.jpg){: .image-featured }_


This is better than nothing but not having monitoring for the frontend will cost you dearly in the long run.

When you are missing monitoring on the frontend you are completely blind there. You experience the hassle of the standard user feedback loop.

![user feedback loop]({{ site.url }}/pixyll/images/user-feedback-loop.png)

<blockquote>
  <footer><cite title="Pedro Catré">Missing information when clients complain and wasted time & effort gathering information.</cite></footer>
</blockquote>


When clients don't complain problems usually fly under the radar.

These issues result in a lot of wasted time and effort (not only for you but for your clients as well). When you are not proactive and fast solving problems your image is affected. Sometimes this is the first impression users have of you and it can stop them from becoming clients.

## Solution

![For I was blind, but now I see]({{ site.url }}/pixyll/images/for_i_was_blind.jpg){: .center-image }

Let's say the priority for your business is the backend and you can't commit a lot of resources for monitoring the frontend. That it is completely fine.
You can just choose a monitoring solution that is easy to setup, cheap or free if you are just getting started.
I usually go with [sentry](https://sentry.io){:target="_blank"}. It is a real-time event logging and aggregation platform  that can fix all the above issues for less than 1 hour of work. Sentry can also be used for the backend but we are focusing on the frontend here. If you don't know what to choose go with sentry, the setup effort is so low you have nothing to lose.

1. First step is to create a free account. They give you 5000 events per month for free which is enough to start, and if you require more [sentry is cheaper than hosting it yourself](https://blog.sentry.io/2017/01/05/a-new-pricing-model.html){:target="_blank"}.
2. Get a [plugin for the webapp you're using](https://docs.sentry.io/clients/javascript/integrations/){:target="_blank"}, spend 5 minutes setting it up and deploy.

Me: That's it!

You: Really?

Me: Yup!

With time you'll fine tune sentry to better adapt to your circumstances, but you are now a lot better off then when you started. Frontend monitoring is one of those things that even if you are doing wrong you still get a lot of benefits out of it (similar to usability tests, go do those too after you're done here [^1]).

Happy monitoring.

---

[^1]:  Check out "Usability testing on 10 cents a day" chapter in [Don't make me think](https://www.amazon.com/Dont-Make-Think-Revisited-Usability/dp/0321965515/ref=pd_sbs_14_t_0?_encoding=UTF8&psc=1&refRID=JEKF04MZ8ADP8S7CFBKP){:target="_blank"}.
