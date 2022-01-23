---
title: "Binary Bomb Lab :: Phase 4"
date: 2015-01-08T12:09:45-08:00
draft: false
featured_image: "/images/binary-bomb/bob-omb-featured-image.jpg"
aliases:
    - /binary-bomb-lab-phase-4/
---

A note to the reader: For explanation on how to set up the lab environment see the ["Introduction"]({{< ref "/binary-bomb-lab-set-up" >}}) section of the post.

If you're looking for a specific phase:

* [Here is Phase 1]({{< ref "/binary-bomb-lab-phase-1" >}})
* [Here is Phase 2]({{< ref "/binary-bomb-lab-phase-2" >}})
* [Here is Phase 3]({{< ref "/binary-bomb-lab-phase-3" >}})
* [Here is Phase 4]({{< ref "/binary-bomb-lab-phase-4" >}})
* [Here is Phase 5]({{< ref "/binary-bomb-lab-phase-5" >}})
* [Here is Phase 6]({{< ref "/binary-bomb-lab-phase-6" >}})

---

#### Phase 4

In my opinion, this is where things start to get tricky. In this phase, it is not enough to simply understand the assembly. Some pattern-recognition will be required. As usual, let's start by taking a look at the code for this phase's function to see if we can find a vector through which to understand what's happening. I'm going to do so by running gdb with a test string and using disas:

![Phase 4 function](/images/binary-bomb/phase-4-function.png)

On line `<phase_4+16>`, the `<phase_4>` function is pushing a fixed value stored at memory address `0x8049808` onto the stack right before a call to `scanf` is made. As we have learned from the past phases, fixed values are almost always important. Lo and behold, when we dump the contents of the memory address we get `“%d”`, which tells us that the answer to this phase should be a single integer

The second important feature of this code occurs on line `<phase_4+53>`. Our input value, which is stored in `%eax`, is getting input into this other function called `<func4>`. Right now `<func4>` is a black box, so we will need to dig into it using si to figure out what it is doing to our integer

The last thing we need to consider is that, after `<func4>` gets called and returns our potentially altered input back to `%eax`, `%eax` then gets compared to the hex value `37`, which in decimal is 55. This means that, whatever our input is to begin with, it needs to be turned into 55 by `<func4>`.

So, now that we know what is generally happening in `<phase_4>`, let’s dig into `<func4>` to figure out what we need to input in order to generate 55.

![Phase 4 func 4](/images/binary-bomb/phase-4-func4.png)

Ok, before we do anything else, let’s recognize that this function is calling itself, which makes it recursive. All recursive functions have a “base case”, which terminates the loop, and then a series of instructions to follow in order to continue the loop if the base case is not met.

Let’s first identify our base case. It should occur early in the function as a conditional check. In `<func4>`, our base case occurs at `<func4+11>`. The function compares its input to 1. If the input is less than or equal to 1, it jumps down to `<func4+48>`, where the return value is set to one. Finally, it returns this value.

Now that we understand that the base case occurs when input=1, let’s identify what happens when the base case is not met. The first thing to note is that the function calls itself twice, once at `<func4+23>` and once at `<func4+37>`. The first time it calls itself, it feeds the new function call its input-1. Similarly, the second time it calls itself, it feeds the new function call its input-2. Once the two recursivelly called functions return, it sums their return values at `<func4+42>` and returns the result.

Here’s what the corresponding C code would look like:

```c
int test(int n) {
  if (n<2)
    return n;
  else
    return test(n-1) + test(n-2);
}
```

Now comes the part where we have to do some abstracted, big-picture thinking. We’ve got recursion, we’ve got a pattern, and we’ve got a function where `F(x) = F(x-1) + F(x-2)`. What famous pattern fits these three parameters? [The Fibbonaci sequence](http://en.wikipedia.org/wiki/Fibonacci_number)!

![Fibonacci spiral](/images/binary-bomb/fibonnaci-spiral.png)

We should note that `<func4>` does not exactly return the Fibonacci number of the input. `<func4>` returns 1 if the input is 1 or less. For example, if our input is x=2, `<func4>` will return 1 for both x-1 = 1 and x-2 = 0.

So, now that we know all this stuff, how do we solve the stage? Remember that, in order to avoid the bomb blowing itself up, whatever gets spit out has to equal hex 37, which is 55 in decimal. Keeping in mind that func4(0) = func(1) = 1, func4(2) = 2, we will actually need to input the Fibonacci number for 55 with 1 subtracted from it. I find that taking a look at a table of the sequence makes this a little less confusing:

![Fibonacci table](/images/binary-bomb/fibbonaci-table.png)

As we can see in the table above, the Fibonacci number for 55 is 10. So given our logic, 10-1= 9, so 9 should be the solution for the fourth phase.

![Phase 4 complete message](/images/binary-bomb/phase-4-complete.png)

Rock and roll. Get your bad self down onto [Phase 5]({{< ref "/binary-bomb-lab-phase-5" >}}).
