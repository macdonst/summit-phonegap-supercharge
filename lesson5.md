---
layout: module
title: Module 5&#58; Adding Analytics
---

### Overview
When you’re building apps it’s important to have an understanding of how your users are behaving. This will help you identify areas of friction and come up with ideas and tests for improvement. It’s a good idea to answer questions like:

* Which features of your app do people use most?
* When are your users most active?
* What does the conversion funnel look like from signup to paying customer?
* Where are people dropping off?

In this module, I’ll explain how you can track what your users are doing in your PhoneGap Application using [Keen IO](http://keen.io/).

## Requirements
Before you can code this feature, you'll first need to add the [Cordova Device Plugin](https://github.com/apache/cordova-plugin-device) to your project since it is not yet used in the Star Track base app template.

1. Open your terminal and use the PhoneGap CLI to add it now (the `--save` parameter will save the plugin to your `config.xml` file):

       phonegap plugin add cordova-plugin-device --save

   >Be sure to visit the [Cordova Device Plugin Docs](https://github.com/apache/cordova-plugin-device) to learn about any platform quirks and more things you can do with this plugin.

## Steps

1. [Download](https://raw.githubusercontent.com/keen/keen-js/master/dist/keen.min.js) the latest version of the Keen IO JavaScript library and move it into the `www/js` folder of your PhoneGap project folder.

2. Include the Keen IO library as part of the project. Open up `www/index.html` and add the following line just after the index.js file gets loaded:

            <script type="text/javascript" src="cordova.js"></script>
            <script type="text/javascript" src="lib/MSOpenTech/winstore-jscompat.js"></script>
            <script type="text/javascript" src="lib/framework7/js/framework7.min.js"></script>
            <script type="text/javascript" src="js/init-styles.js"></script>
            <script type="text/javascript" src="js/my-app.js"></script>
            <script type="text/javascript" src="js/keen.min.js"></script>

3. You will also need to include a number of urls in the Content Security Policy. In `www/index.html` find the tag that starts with:

{%raw%}
            <meta http-equiv="Content-Security-Policy"
            content="default-src 'self' data: gap: https://ssl.gstatic.com https://api.spotify.com 'unsafe-eval' 'unsafe-inline' ws://localhost:3000; style-src 'self' 'unsafe-inline'; media-src *; img-src * data:">
{%endraw%}

   and replace it with this one:

{%raw%}
           <meta http-equiv="Content-Security-Policy"
           content="default-src 'self' data: gap: https://ssl.gstatic.com https://api.spotify.com https://www.google.com/jsapi https://www.google.com https://api.keen.io 'unsafe-eval' 'unsafe-inline' ws://localhost:3000; style-src 'self' 'unsafe-inline' https://www.google.com; media-src *; img-src * data:">
{%endraw%}

4. Now open the `www/js/my-app.js` file and add the following variable declaration to the top of the file under the `isIos` and `isMaterial` handling. This will allow us to register event listeners that report back to the analytics service.

                var keenClient = null;

5. Initialize the Keen.io client by adding the following code to the `deviceready()` function :

                keenClient = new Keen({
                  projectId: "Your_ProjectID",
                  readKey: "Your_ReadKey",
                  writeKey: "Your_WriteKey"
                });

   The Keen API keys will be provided for you in the workshop but you can grab your Keen API keys from your Keen IO project page after you create an account. After adding in your keys, you should have a fancy new client object ready to send and read data from Keen IO.

6. Now let's track the search event in our app. To accomplish this we'll add some code into the `searchSubmit(e)` function.

                var eventData = {
                  query: formData.q,
                  device: device
                };
                keenClient.client.addEvent("clicks", eventData, function(err, res) {
                  if (err) {
                    console.log("Error: " + err);
                  }
                  else {
                    console.log("Event sent.");
                  }
                });

   Now we are collecting all the clicks of the search button. Whenever someone does a search, you will see the new event appear in the “button clicks” collection inside the Keen IO project along with it’s associated properties.

   Right now, we are just logging the search query and the device information on every click event. The event data looks something like this:

                {
                  "query": "Foo Fighters",
                  "device": {
                    "available": true,
                    "uuid": "99195ABC-C79B-4DF3-9620-C38CD087617A",
                    "platform": "iOS",
                    "version": "8.4",
                    "model": "iPhone7,2",
                    "cordova": "3.6.3"
                  },
                  "keen": {
                    "timestamp": "2015-08-07T00:15:43.023Z",
                    "created_at": "2015-08-07T00:15:43.023Z",
                    "id": "55c3f8af6f31a205660b5172"
                  }
                }

7. Now let's analyze the data you’re sending. In this example, we can display the number of searches today by running a simple queries. We are not going to modify our app in this case but we can inspect the app and run Javascript using it's context.

   Right mouse click in your web browser window and select `Console`. Paste in the following code to setup our query:

                var count = new Keen.Query("count", {
                       eventCollection: "clicks",
                       timeframe: "this_1_days",
                       interval: "daily"
                   });

   Then run the query by pasting in this code:

                 keenClient.run(count, function(err, response) {
                        if (err) {
                            console.log(err);
                        }
                        else {
                            console.log(response.result[0].value);
                        }
                    });

   The `run` function will return an object that represents the state of the query but the next line will be the total amounts of time that the Search button has been used today.

This was a very simple example of how you can use Keen’s JavaScript library to easily gather data on how your users are interacting with your PhoneGap app and gain some great insights on where you can improve. Once you’re collecting your own important events, you can analyze all of it via [Keen IO’s data explorer](https://keen.io/blog/114588771746/introducing-data-explorer) or you can programmatically run queries and embed the results and charts anywhere using [Keen’s JavaScript library](https://keen.io/docs/api/?javascript#events).

### Dependencies

   [Apache Cordova Dialog Device](https://github.com/apache/cordova-plugin-device)

    $ phonegap plugin add cordova-plugin-device

   >You won't need to specifically add it for this workshop as it is already included in the **PhoneGap Developer App**. If you are creating the project from scratch and using the CLI locally then use the command above.

<div class="row" style="margin-top:40px;">
<div class="col-sm-12">
<a href="lesson4.html" class="btn btn-default"><i class="glyphicon glyphicon-chevron-left"></i> Previous</a>
<a href="lesson6.html" class="btn btn-default pull-right">Next <i class="glyphicon
glyphicon-chevron-right"></i></a>
</div>
</div>
