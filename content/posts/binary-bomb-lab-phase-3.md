---
title: "Binary Bomb Lab :: Phase 3"
date: 2015-01-07T12:09:45-08:00
draft: false
featured_image: "/images/binary-bomb/bob-omb-featured-image.jpg"
aliases:
    - /binary-bomb-lab-phase-3/
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

By now you know the drill. Off to our objdump file!

![Phase 3 object dump](/images/binary-bomb/phase-3-objdump.gif)

Woah. There are a lot of conditionals here. Maybe you’re an IA32 warrior who can take one look at this code and immediately tell what is going on, but for the rest of us, an iterative approach might work better here.

The first interesting thing we see here is the call to `scanf` which occurs at memory address `8048bb7` in my objdump output file. There are two values that are pushed onto the stack right before `scanf` is called. After the call to `scanf` is made, the two pushed values are compared, and the bomb explodes if they’re not equal. Let’s use gdb to figure out what they are.

As usual, we will set a breakpoint at our phase, phase_3, and then run the bomb using answers.txt as an argument. After that, we can input a test string and when our breakpoint hits, we can use the gdb command `x/s [memory-address]` to print whatever string we find at that memory address:

![Phase 3 arg params](/images/binary-bomb/phase-3-arg-params.png)

From this we see that our test string is stored in register `%eax`, while the format being asked for follows the format `%d %c %d`. In case your C is rusty:

![Documentation of scanf imports](/images/binary-bomb/scanf-inputs.png)

From this, we know that our input will have to be an int, followed by a char, followed by an int again. This is a good first step. Let’s update our test input to `1 a 2` and run our bomb through gdb again.

When we do this, you’ll notice that after passing the format check, the next call to `<explode_bomb>` gets passed over as well. We could dig into [why 1 is correct as the first integer](https://code.google.com/p/binary-bomb/source/browse/phase3.txt?spec=svnb2dca04ee85e83c4693ac2cf58339bbfd34bd804&r=b2dca04ee85e83c4693ac2cf58339bbfd34bd804), but let’s take the freebie and use our time to continue on.

![Phase 3 second number](/images/binary-bomb/phase-3-second-number.png)

Once we arrive at the next test, we see that the value `$0xd6` is being compared to the value stored at `-0x4(%ebp)`.

As you should certainly know, `d6` in hexidecimal is equal to 214 in decimal. When we check the value stored in `-0x4(%ebp)`, it should be apparent that the program has jumped past our char input for now (its using a case statement for all of you A+ students), and is checking our second integer here. Since the jump statement to miss the next `<explode_bomb>` call requires equality, this means that our second integer must be 214.

Finally we come to our character. This one is just as simple as the last:

![Last character](/images/binary-bomb/phase-3-character.png)

From the move statement on line `<+240>` its clear that our char is stored at `-0x5(%ebp)` and the value to compare against is coming from `%bl`. When we print out the value to be compared against, we get a hexidecimal value of `62`, which in decimal is 98:

![ASCII character chart](/images/binary-bomb/Screen-Shot-2014-12-19-at-18-04-05.png)

In other words, our character should be a lowercase ‘b’. Lo and behold, when we put our three answers together:

![Phase 3 correct answer: 1 b 214](/images/binary-bomb/phase-3-correct-answer.png)

Looks like the bomb squad is in town! Time to take on [Phase 4]({{< ref "/binary-bomb-lab-phase-4" >}}).
