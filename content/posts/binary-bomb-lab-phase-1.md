---
title: "Binary Bomb Lab :: Phase 1"
date: 2015-01-05T12:09:45-08:00
draft: false
featured_image: "/images/binary-bomb/bob-omb-featured-image.jpg"
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

Phase 1 is sort of the “Hello World” of the Bomb Lab. You will have to run through the reverse engineering process, but there won’t be much in the way of complicated assembly to decipher or tricky mental hoops to jump through. To begin, let’s take a look at the `<phase_1>` function in our objdump file:

![Phase 1 object dump](/images/binary-bomb/phase-1-objdump.png)

The `<phase_1>` function is rather simple. It moves some things around, and then pushes two values onto the stack before calling the function `<strings_not_equal>` on them. One of these values is stored in the register `%eax` and the other appears to be stored in the code at `$0x80497c0`. If we really wanted to be pedantic, we could go check out what <strings_not_equal> does, but I think it’s pretty safe to assume that it checks equality of the strings.

Moving on: if the strings are equal, the function runs `<je>` to jump over the `<explode_bomb>` function. So, what does `<phase_1>` do in a nutshell? It takes your standard input, stores it in `%eax`, and then compares it to the string at `$0x80497c0`. If they’re equal, you get to go to the next phase.

Given that the string we’re looking for is stored at a fixed memory address, we know that it must have printed out somewhere within our `strings` output text file. Unfortunately, it could be any of those strings for all we know. We will have to use gdb to find out a little more.

First things first, let’s fire up gdb and set a breakpoint on the `<phase_1>` function. This will ensure that the bomb doesn’t blow up when we run the program with the compiler. Set the breakpoint with the command `break phase_1`. Next, run the program with the `run` command.

![Phase 1 GDB breakpoint demonstration](/images/binary-bomb/phase-1-gdb.png)

As you can see in the screenshot above, once you run the program, “Dr. Evil” (stdin) asks you for the first password. Let’s enter the string “test string”, since we know our breakpoint will save us from bomb detonation. As soon as we enter our string and press enter, the breakpoint stops execution of the program. Now we can take a look at the values in our two memory addresses of interest, `$eax` and the fixed address `0x80497c0`. We will use the gdb command `x`, and ask for the 25 characters that follow the memory address. Since the register `$eax` is not a fixed memory address, we’ll have to first use `p/x` to find its address. `p/x $eax` quickly lets us know that the currently allocated address for the register is `0x804b680`.

At this point, all we have to do is check the contents of each memory address. As expected, running `x /25c 0x804b680` gives us our inputted string `“test string”`. When we run the command on the fixed address, `x /25c 0x80497c0`, we get something exciting! The 25 characters that follow the memory address are `“Public speaking is very e”`. Let’s head to our `strings` output to check for a string that starts this way:

![Strings output Phase 1 solution](/images/binary-bomb/3-strings-2.png)

There it is, plain as day! The answer to Phase 1 is the string “Public speaking is very easy.” Let’s input it into our bomb to make sure:

![Phase 1 success](/images/binary-bomb/phase-1-success.png)

Hooray! On to [Phase 2]({{< ref "/binary-bomb-lab-phase-2" >}}).
