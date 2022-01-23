---
title: "Binary Bomb Lab :: Phase 5"
date: 2015-01-09T12:09:45-08:00
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

Ok, let’s get right to it and dig into the `<phase_5>` code:

![Phase 5 overview](/images/binary-bomb/phase-5-overview.png)

So, what have we got here? First things first, we can see from the call to `<string_length>` at `<phase_5+23>` and subsequent jump equal statement our string should be six characters long.

Next, as we scan through each operation, we see that a register is being incremented at `<phase_5+57>`, followed by a jump-less-than statement right afterwards that takes us back up to `<phase_5+43>`. Remember this structure from Phase 2? A loop is occurring. And, as you can see at `<phase_5+58>` structure, the loop iterates 6 times. Given that our string is 6 characters long, it makes sense to assume that the function is iterating over each character in the loop and presumably doing something to them.

Finally, we can see down at the bottom of the function that `<strings_not_equal>` is being called after the contents of `%eax` and the fixed address `0x804980b` have been pushed onto the stack. This looks just like phase 1. The code is comparing the string (presumably our input) stored in `%eax` to a fixed string stored at `0x804980b`.

So, what do we know about phase 5 so far?

- our input has to be a string of 6 characters
- the function accepts this 6 character string and loops over each character in it
- the result of the loop is compared to a fixed string, and if they’re equal, the bomb doesn’t explode


As a next step, let’s input the test string “abcdef” and take a look at what the loop does to it. Then, we can take a look at the fixed value we’re supposed to match and go from there:

![Phase 5 compared strings](/images/binary-bomb/phase-5-compared-strings.png)

Woah. “srveaw” is pretty far off from “abcdef”. On the bright side, at least now we know that our string should come out of the loop as “giants”. Given this info, it looks as though the loop is implementing a cypher. From here, we have two ways to solve this phase, a dumb way and a smart way. The dumb way is to simply input all characters from a-z into the cypher and create a mapping table. From this mapping table, we can figure out the un-cyphered version of “giants”. This works just fine, and I invite you to try it.

The smart way of solving this phase is by actually figuring out the cypher. Once we understand how it works, we can reverse engineer “giants” into its pre-cypher form without having to waste time doing trial and error.

I don’t want to go through either solution all the way here, since the first one is a no-brainer and the second one is a little complicated. In order to solve the cypher, take a look at `%esi` and you’ll find an array of characters stored there, where each character has an index. Essentially what is happening is, each character from our string is ANDed with 0xf, and the result is used to get the character with the corresponding index from the array. If you solve the phase this way, you’ll actually notice that there is more than one correct solution.

Either way, eventually you’ll find that the pre-cyphered version of “giants” is actually “opekmq”.

![Phase 5 complete](/images/binary-bomb/phase-5-complete.png)

On to the last phase! [Phase 6]({{< ref "/binary-bomb-lab-phase-6" >}}).
