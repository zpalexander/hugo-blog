---
title: "Debugging \"Unknown Provider\" In Minified Angular.js"
date: 2016-01-16T00:46:05-08:00
draft: false
---

For the past few months I’ve been working on an [AngularJS](https://angularjs.org/) app that makes use of [Angular Material](https://material.angularjs.org/latest/) as a Bootstrap-like front-end library. I recently had to solve one of the hardest bugs I’ve ever faced in Angular, and I’d like to share my debugging technique with anyone who might encounter the same problem down the road. While the bug was related to the use of Angular Material in my case, it could occur in vanilla Angular as well, so don’t stop reading just yet!

---

#### The Problem

Angular Material has an extremely useful directive called mdDialog. It allows developers to provide interactive modal dialog boxes to their users without all of the painful boilerplate usually associated with building your own from scratch.

![mdDialog screenshot](/images/debug-unknown-provider-minified-angularjs/md-dialog-example-1024x639.png)

Seems great, right? It honestly is, and my team and I have been using this directive throughout our app with no problems. That is, until QA discovered a bug in our staging environment that was keeping one of our dialog boxes from opening and throwing a cryptic error in the console:

![Console error screenshot](/images/debug-unknown-provider-minified-angularjs/minified-angular-error-1024x132.png)

Useless, right? While seasoned Angular developers will be able to catch what is happening here, most of them will also readily admit that this error is cryptic and unhelpful. Thankfully Angular has a feature that creates a custom docs page for you based on your error. You can see the URL for my custom error page on the first line. My page read:

![Useless error](/images/debug-unknown-provider-minified-angularjs/useless-error-1024x550.png)

I’m certain that anyone reading this who has put in their time with Angular is nodding their head right now. This is the dreaded `Error: $injector:unpr Unknown Provider error`.

For anyone who doesn’t know, this is a commonly seen Angular error that means Angular’s dependency injection can’t resolve one or more dependencies somewhere in your code. Explaining dependency injection is outside the scope of this post, but you can [read more about it here](https://docs.angularjs.org/guide/di) if you don’t fully understand what I mean.

Dependency injection is great for writing explicit and testable code, but it runs into a hiccup during minification. The quirk is as follows. Suppose your app looks like this:

```javascript
// Don't actually build apps like this, its just an example
// In real life follow this: https://github.com/johnpapa/angular-styleguide
angular.module('myModule', []); // Declare the module

angular.module('myModule')
  .service('myService', function() {
    return {
      saySomething : function() { console.log('Something'); }
    }
  });

angular.module('myModule')
  .controller('myController', function($scope, $http, myService) {
    //We included $scope, $http, and myService into the controller as dependencies
    myService.saySomething() //"Something"
  });
```

… then the minified version of `myController` will partially look like this:

```javascript
var myController = function(a, b) {
  //This will throw an error at runtime because 'a' is not a valid dependency
}
```

Angular developers know that the way to fix this is to tell Angular what your minified dependencies actually refer to like so:

```javascript
myModule
  .controller('myController',
    ['$scope', '$http', 'myService',
    function($scope, $http, myService) {
    //We included $scope, $http, and myService into the controller as dependencies
    myService.saySomething() //"Something"
  }]);
```
So I had my problem laid out. Somewhere in this app a dependency injection was going wrong.

---

#### Debugging

I spent almost an hour scouring the code related to my mdDialog box looking for a dependency that I had mis-declared or forgotten about, but I couldn’t find anything of the sort. Defeated, I started to look at the rest of the stacktrace that Angular had provided in the error, but I quickly realized that I’d need to use an un-minified version of the Angular library if I wanted to get anywhere. I swapped out the script tag and checked the stacktrace once more:

![Unminified error](/images/debug-unknown-provider-minified-angularjs/unminified-angular-error.png)

Equally useless. Feeling hopeless, I decided to step into the Angular source to see if I could find anything useful there. I clicked the first line number given by the stacktrace which brought me to the function that Angular uses to actually create the custom docs URL that gets printed in the console. Interesting… I decided to set a breakpoint on it to see what I could grok from the arguments:

![Breakpoint on error creator](/images/debug-unknown-provider-minified-angularjs/breakpoint-on-error-creator-1024x402.png)

Now I had something. Looking through the call stack on the right side of the developer tools, I noticed that `minErr()` makes a call to a function called `invoke()`. Invoking an error creator message must require some information about what error is occurring, so I stepped into it:

![After clicking invoke](/images/debug-unknown-provider-minified-angularjs/after-clicking-invoke-1-1024x402.png)

What’s that? I recognized that the arguments being passed in looked a lot like my code. I did a project wide Find for the snippet in Sublime Text and lo and behold: I had found bug.

![Bug snippet screenshot](/images/debug-unknown-provider-minified-angularjs/Screen-Shot-of-bug.png)

While what’s causing the DI problem might not be immediately clear, I knew right away. I had declared this code within a controller. As such, the $scope variables being referenced in the controller function that is passed into mdDialog on line 96 belong to the controller in which this is declared. Angular dependency injection does NOT allow developers to inject one controller into another, as per the docs:

![Screenshot of error message](/images/debug-unknown-provider-minified-angularjs/Screen-shot-of-error-msg.png)

Declaring a separate controller for the mdDialog solved the problem.

>tl;dr if you get a weird unknown provider error only when you minify your code, set a breakpoint on the line that generates the error docs URL, step into its call to invoke(), and check its arguments to find what part of your code exactly is breaking.

I hope this helps you avoid painful hours of fruitless debugging. Happy hacking!
