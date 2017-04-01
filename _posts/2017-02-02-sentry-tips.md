---
layout:     post
title:      Sentry Pro Tips
date:       2017-02-02
summary:    Fine tune Sentry
categories: frontend
---

Here are some quick tips to get the most out of [Sentry](https://sentry.io){:target="_blank"} when using it to monitor your frontend.

## Log server errors

If you just setup Sentry with a library specific to the framework you are using you get warned of all exceptions that happen in your application.
What you might like to do is to also log server errors because it gives you better visibility of problems affecting the frontend.
You could get information on these server errors from the backend log manager, but it requires less context switching to just do it all in Sentry if it relates to the frontend.

Here is how you could do it with angular (you can do something similar with other frameworks).

Register an interceptor for HTTP requests:

{% highlight js %}
(function () {
    'use strict';

    angular.module('myNiceWebApp', [
        'ngRaven',
        // ...
    ])
        // ...
        .config(['$httpProvider', function ($httpProvider) {
            // Add an HTTP interceptor
            $httpProvider.interceptors.push('interceptHttpRequestsService');
        }])
        // ...
        .run([
            'runAppService',
            function (runAppService) {
                runAppService.runApp();
            }]);
}());
{% endhighlight %}

Manually capture a message on error responses:

{% highlight js %}
(function () {
    'use strict';
    angular
        .module('myNiceWebApp.interceptHttpRequestsService', [])
        .factory('interceptHttpRequestsService', [
            '$q',
            interceptHttpRequestsService]);

    function interceptHttpRequestsService($q) {

        var service = {
            responseError: responseError
        };

        return service;

        function responseError(response) {
            // we usually don't want to log UNAUTHORIZED requests otherwise we get an alert every time someone's
            // session expires.
            if(response.status !== 401) {
                // This example assumes the server is returning an error code in the message `response.data.errorCode`
                // the details of this will be different depending on the server response.
                Raven.captureMessage('Server error ' + response.status + ' ' + response.data.errorCode, {
                    level: 'error',
                    extra: response
                });
            }
            
            return $q.reject(response);
        }
    }
}());
{% endhighlight %}

## Filter sensitive information

Out of the box Sentry filters out sensitive information (e.g. if you send a password to Sentry it will filter it).

When you look at the data in the Sentry dashboard it will be stripped of sensitive information. For example:

{% highlight js %}
{
    // ...
    withCredentials: [Filtered]
}
{% endhighlight %}

Yet [raven](https://github.com/getsentry/raven-js){:target="_blank"} - the frontend library that sends the logs to Sentry - is still sending this information to Sentry.
If you are not comfortable with that you can just filter it also on the client side.
It could look something like this:


{% highlight js %}

// Example of sensitive data you might want to filter out
var propertiesToMask = ['password', 'secret', 'credentials'];

function isSensitiveProperty(key) {
    return _.find(propertiesToMask, function (propertyToMask) {
        return _.includes(key.toUpperCase(), propertyToMask.toUpperCase());
    });
}

function maskSensitiveData(obj)
{
    var key;
    if (obj instanceof Object) {
        for (key in obj){
            if (obj.hasOwnProperty(key)){
                if(isSensitiveProperty(key)) {
                    obj[k] = '[masked]'
                } else {
                    // Recursive call to scan property
                    maskSensitiveData( obj[key] );
                }
            }
        }
    }
}

Raven
.config('https://xxxx@sentry.io/xxx', {
    // A function that allows mutation of the data payload
    // right before being sent to Sentry.
    dataCallback: function(data) {
        return maskSensitiveData(data);
    }
})
.addPlugin(Raven.Plugins.Angular)
.install();
{% endhighlight %}

## Add client-side context to your logs

When a user logs in set this information for all requests.

{% highlight js %}
Raven.setUserContext({
    email: 'user@example.org'
});
{% endhighlight %}

You can also save the webapp version they are using and which environment it is running on.

{% highlight js %}
Raven.config('https://xxx@sentry.io/xxx', {
    release: 8.15.0.dev0_example_version,
    environment: 'LIVE'
})
.addPlugin(Raven.Plugins.Angular)
.install();
{% endhighlight %}

## Filter localhost logs

Usually you won't want to get emails informing you of exceptions when developing the application or running automated tests.
You can configure Sentry to ignore localhost logs on the Sentry dashboard.

![filter logs on graylog]({{ site.url }}/pixyll/images/sentry-filter-localhost.png){: .center-image }

Or on the client side config, that would look something like this:


{% highlight js %}
Raven.config('https://xxx@sentry.io/xxx', {
    ignoreUrls: [/.*0\.0\.0\.*/, /.*localhost.*/]
})
.addPlugin(Raven.Plugins.Angular)
.install();
{% endhighlight %}

A _hacky_ alternative is to check window.location.hostname and, if it is running locally, initialize raven with an invalid key.
This way raven is initialized and available to the rest of the application but the logs do not reach Sentry.
You should avoid this option if you can.

## Turn on source maps

You should be combining and minifying your JavaScript before deploying your webapp.
However, when you do this your client-side code is no longer readable or debbugable in the production environment.
 This is a really big set back when using an error tracking tool like Sentry because you won't be able to know exactly where an error happens... _unless you use source maps :)_

<blockquote>
  <p>
    Source Maps - Basically it's a way to map a combined/minified file back to an unbuilt state. When you build for production, along with minifying and combining your JavaScript files, you generate a source map which holds information about your original files. When you query a certain line and column number in your generated JavaScript you can do a lookup in the source map which returns the original location.
  </p>
  <footer><cite title="Ryan Seddon" target="_blank"><a href="https://www.html5rocks.com/en/tutorials/developertools/sourcemaps/">Ryan Seddon, Introduction to JavaScript Source Maps, html5rocks.</a></cite></footer>
</blockquote>

 You'll need to setup your build system to produce source maps. There is no next step, since Sentry picks up source maps automatically when they are available.
 As soon as you have source maps Sentry will be able to tell you the exact line in your code where an error occurred.
 
 ![filter logs on graylog]({{ site.url }}/pixyll/images/sentry-source-maps.png){: .center-image }

## Set alerts

Sentry provides a way to set alerts based on rules.
The example bellow is the simplest, get an email for all events.
The rules you set will depend on the kind of application you have and on the number of users.

![]({{ site.url }}/pixyll/images/sentry-alert-rules.png){: .center-image }

## Extra tip - slack integration

If you are using slack there is an [integration for Sentry](https://slack.com/apps/A0F814BEV-sentry){:target="_blank"} that improves the visibility of the frontend
errors for the entire team.

![]({{ site.url }}/pixyll/images/sentry-slack-integration.png)
