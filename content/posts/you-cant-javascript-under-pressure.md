---
title: "You Cant Javascript Under Pressure"
date: 2014-06-20T16:53:11-08:00
draft: false
---

A fun little Javascript game was forwarded around my office recently, and I had a great time revisiting my elementary programming skills while defeating each of its levels. It’s called [“You Can’t Javascript Under Pressure”](http://games.usvsth3m.com/javascript-under-pressure/). I would recommend that everyone take the next 10-30 minutes out of their life to check it out.

[![Game screenshot](/images/you-cant-javascript-under-pressure/Screen-Shot-2014-07-19-at-19-25-24.png)](http://games.usvsth3m.com/javascript-under-pressure/)

While I found the game relatively easy, I figured it might be nice to share my solutions with the web. This game is great practice for the elementary-level software developer, and working through tough problems with a cheat sheet can be an excellent way to learn new topics. Even if you don’t need any help solving these problems, it can always be fun to see solutions that are different than your own. So, without any further ado, here are my solutions to each of the 5 levels:

---

#### Level 1

Ok, this one is a gimmie. All you have to do is return twice the integer. If you understand basic JavaScript syntax and made it through 3rd grade, you should be able to get this one no problem.

```javascript
function doubleInteger(i) {
    // i will be an integer. Double it and return it.
    return i*2;
}
```

Too easy.

---

#### Level 2

The plot thickens! This time we are asked to check if the number is even or odd. Time to dust off our old trusty friend, the modulus (`%`) operator.

{{< hp5 "https://www.youtube.com/embed/dWSJqvFE7Cg" >}}

In case you were too lazy to watch the video above, the modulus operator finds the remainder of division of one number by another. Remember remainders from 6th grade? I’m a little fuzzy on the details as well, but we don’t need to understand them completely in order to solve this level. The key here is that when a division operation does not return a remainder, that means that the number was divided evenly. So, what are all even numbers evenly divisible by? Well, 2 of course.

```javascript
function isNumberEven(i) {
    // i will be an integer. Return true if it's even, and false if it isn't.
    if (i%2 === 0) {
        return true;
    }
    else {
        return false;
    }
}
```

Et voila!

---

#### Level 3

In the third level, we have ourselves a little string manipulation puzzle. The real meat of this problem is in simply knowing the JavaScript functions necessary to chop strings up and put them back together. We’re given a string that may or may not be a filename with an extension. So the first thing we need to do is check to see whether this condition is true or false. What do all filenames have in common? Well, they contain a period that separates the name from the extension, of course!

Next, once we have identified a filename with an extension, we will need to split it on the period and return the last part. This is really just a case of knowing the necessary JavaScript.

```javascript
function getFileExtension(i) {

    // i will be a string, but it may not have a file extension.
    // return the file extension (with no period) if it has one, otherwise false

    // If there is no period in the string, it's not a filename so we can return false
    if (i.indexOf(".") < 0) {
        return false;
    }

    // Otherwise, we need to grab and return the extension
    var filenameParts = i.split(".");
    return filenameParts[filenameParts.length-1];

}
```

Three up and three down!

---

#### Level 4

Ok, now it’s time to move up to the big leagues. Level 4 asks us to look through an array for the longest string. While this problem in itself is relatively straightforward, the tricky part is handling the edge cases. It might seem obvious to just run `string.length` on each array index and pop out the one that has the largest value, but this is sure to break in certain cases. Read the problem carefully: it tells us that we’ll be receiving an array and we have to find the longest string in it, but doesn’t say anything about what *else* the array might contain. Before we do anything, we need to make sure what we’re dealing with is a string.

```javascript
function longestString(i) {

    // i will be an array.
    // return the longest string in the array

    var longestLength = 0;
    var returnString = "";

    i.forEach(function(item) {
        if ((typeof item === 'string') && (item.length > longestLength)) {
            longestLength = item.length;
            returnString = item;
        }
    });

    return returnString;
}
```

Boom.

---

#### Level 5

This level is by far the hardest, and actually might require a little bit of thought if you don’t regularly implement recursive logic. Yep, that’s right: the best solution to this problem is a recursive one. I personally think recursion is super fun. If you like solving this level, check out the [Tower of Hanoi](https://en.wikipedia.org/wiki/Tower_of_Hanoi) problem for more recursive puzzle-solving goodness. In case you’re rusty, here is a quick refresher on recursion:

{{< hp5 "https://www.youtube.com/embed/ozmE8G6YKww" >}}

So, how can we use recursion to solve this problem? Let’s image we have an array that looks like this:

`[0,1,a,3]`

Summing and returning the integers in this array is pretty simple. We take a look at each index, check to see that it’s an integer, and add it to the count. At the end, we’re left with 0 + 1 + 3 = 4. No problem.

But, what happens if our array looks like this instead?

`[0,1,[3,2],3]`

If we follow our previous method again, we’ll be humming along for the first two indices. 0 + 1 = 1, right? The problem arises when we get to our third value. We actually have a nested array within our array. The description of the problem asks us to “sum all integers you find, anywhere”. Well, this nested array has integers in it, doesn’t it? How do we add these to the count without borking up our iteration over each array index?

The answer is recursion. If we can write a function that returns the sum of all integers in our array, why can’t we use it to find the sum of integers in the array in our array? A little bit like Inception? Maybe.

![Recursion Inception](/images/you-cant-javascript-under-pressure/Inception-confused1.jpg)

```javascript
function arraySum(i) {

    // i will be an array, containing integers, strings and/or arrays like itself.
    // Sum all the integers you find, anywhere in the nest of arrays.

    var sum = 0;
    function sumArray(i) {
        i.forEach(function(item) {
            if (typeof item === 'number') {
                sum = sum + item;
            }
            // Remember that everything is an object in JavaScript!!!
            if (item instanceof Array) {
                sum = sum + sumArray(item);
            }
        });
    return sum;
    }

    return sumArray(i);
}
```

Bam! You’re a JavaScript master!
