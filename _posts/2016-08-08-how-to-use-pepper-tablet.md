---
layout: post
published: false
date: 2016-08-08 09:00
category: development
meta: ""
---

The whole project is on Github -> link
this sample works on Pepper but not Nao. Nao uses an older version of JS SDK. Nao apps will still work on Pepper (deprecation)

## Setting up your projects

Create app. Basic Manifest settings.

Create HTML folder. Put index.html in html folder.

Download and put needed libraries in project folder. As much and possible, don't rely on Tablet wifi, which is not always available for some obscure reason. (see troubleshooting). For example I often include jquery in my projects in the js folder.

Also, add your javascript file as js/script.js, and stylesheet as css/style.css.

In your html, use relative paths for files that are part of your application project:

``` html
<script src="/libs/qimessaging/2/qimessaging.js"></script>
<script src="js/jquery-3.1.0.min.js"></script>
<script type="text/javascript" src="js/script.js"></script>
```

On the other hand, note how I included the javascript SDK as an absolute path. This file is served by the robot and doesn't need to be in your project.

## Display the web page on the tablet

There are 2 ways to display the project page on the tablet. But before that, don't forget to add something on your page. This will do:

``` html
<h1>Hello World!</h1>
```

Then from Python, for example in a Choregraphe box, do:

```Python
# method 1: use loadApplication
uid = self.getPackageUid()
self.session().service('ALTabletService').loadApplication(uid)

# method2: use loadUrl
#ip = self.session().service('ALTabletService').getRobotIp()
#url = 'http://' + ip + '/apps/' + uid + '/index.html'
#self.session().service('ALTabletService').loadUrl(url)

# at this point, the page is still hidden by default tablet screen
self.session().service('ALTabletService).showWebview()
```

Note that `self.getPackageUid()` is a method accessible only from Choregraphe boxes.

Method 1 will simply load the index.html file from your html folder. Method 2 allows you to choose exactly which file to display. This can be useful if the file to show by default depends on application context (language, time, user preference, etc...).

Start your application with Choregraphe and see "Hello World!" appear on your tablet.

## Control your robot from the tablet

In this part, we will add a button to the tablet, and use it to make the robot talkself.

```javascript
var session;

// connecting to robot
try {
  QiSession( function (s) {
    console.log('connected!');
    session = s;
    // now that we are connected, we can use the buttons on the page
    $('button').prop('disabled', false);
  });
} catch (err) {
  console.log("Error when initializing QiSession: " + err.message);
  console.log("Make sure you load this page from the robots server.")
}

// event callback
$(function () {
  $('#say').click(sayHelloWorld);
});

// example of calling a Naoqi API method
function sayHelloWorld() {
  session.service('ALTextToSpeech').then(function (tts) {
    tts.say('Hello World!');
  }, function (error) {
    console.log(error);
  })
}
```

## Troubleshooting

Dynamic info taken from the web will work better if you download them through robot and then serve them via robot to tablet.
