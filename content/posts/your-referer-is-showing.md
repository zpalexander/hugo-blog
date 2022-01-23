---
title: "Your Referer Is Showing"
date: 2016-06-08T00:18:28-08:00
draft: false
aliases:
    - /your-referer-is-showing/
---

Did you know that every time you click through to a new webpage, you're voluntarily telling that new webpage where you came from?

This isn't a trick by the NSA, its just the way the web works. As most people know, whenever you click a hyperlink, the browser sends a request to the resource on the other end of the link. What most people don't realize is that by default the request contains the referrer field, which indicates the page where the user clicked the link.

![Surveillance camera](/images/your-referer-is-showing/surveillance.jpg)

Visible referer headers can lead to all kinds of privacy problems. The information contained in a referrer can be abused to track website visitors across the internet, especially when combined with cookies and other tracking techniques. Search engines in particular are especially bad when it comes to giving away information through referer headers since they often include the search terms used to find a web page:

{{< rawhtml >}}
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Remember kids, when you embed other people’s stuff in your page, they see the referer header.</p>&mdash; Nick Craver (@Nick_Craver) <a href="https://twitter.com/Nick_Craver/status/593724330338611201">April 30, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{{< /rawhtml >}}

{{< rawhtml >}}
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Oookay so <a href="http://t.co/Cp0u2ghdIf">http://t.co/Cp0u2ghdIf</a> sends your personal info to third-party sites via the Referer header... <a href="https://t.co/ixy1nZjB2v">https://t.co/ixy1nZjB2v</a></p>&mdash; Chris (@leftside) <a href="https://twitter.com/leftside/status/558072479798013952">January 22, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{{< /rawhtml >}}

In addition to privacy concerns, leaky referer headers can also lead to all kinds of nasty security exploits:

{{< rawhtml >}}
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">&quot;Referer Header Based Blind SQL Injection Explained With Example&quot;<br>Haider Mahmood.<a href="https://t.co/pICnVm0TZr">https://t.co/pICnVm0TZr</a></p>&mdash; KS7000 (@ks7000) <a href="https://twitter.com/ks7000/status/713154427088011264">March 25, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{{< /rawhtml >}}

{{< rawhtml >}}
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">WP Slimstat = 4.1.5.2 - Referer Header Cross-Site Scripting (XSS) <a href="https://twitter.com/hashtag/wordpress?src=hash">#wordpress</a> <a href="https://twitter.com/hashtag/websecurity?src=hash">#websecurity</a> <a href="http://t.co/1B1I6aMiAB">http://t.co/1B1I6aMiAB</a></p>&mdash; eXploit.By (@exploitby) <a href="https://twitter.com/exploitby/status/625410275303231489">July 26, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{{< /rawhtml >}}

As a side note, before you flip out about my misspelling of the word "referer", be aware that I'm not spelling it wrong by accident - [this is how browser implementations spell it](https://en.wikipedia.org/wiki/HTTP_referer):

{{< rawhtml >}}
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">4 hours of debugging later: The HTTP spec INTENTIONALLY misspells the &quot;referer&quot; header with 3 R&#39;s instead of 4.  <a href="https://t.co/qT1M27yXOb">https://t.co/qT1M27yXOb</a></p>&mdash; Chris Johnson (@CJcodes) <a href="https://twitter.com/CJcodes/status/726553497739599872">April 30, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{{< /rawhtml >}}

Now that we've got linguistics out of the way, let's talk about how to solve this problem.

---

### Disabling Referer Headers in Firefox

Firefox provides the easiest method for disabling referer headers:

1. Type `about:config` in the URL bar and hit enter. You may need to click through the "I'll be careful, I promise!" warning if you haven't visited this config page before.

2. In the search bar, type `network.http.sendRefererHeader`

3. Double click on the preference's "Value" text box when it appears and set the value to either 0, 1 or 2.

![Firefox about:config](/images/your-referer-is-showing/firefox-about-config.png)

The value you enter determines how Firefox will handle your Referer Headers in the future:

- ***Value 0*** - completely disables the Referer Header. This provides the most privacy, but may break some websites (more on this later)
- ***Value 1*** - Sends a Referer Header when clicking on a link, but not when loading images on a page. This will prevent
- ***Value 2*** - Default setting which sends all Referer Headers


---

### Disabling Referer Headers in Chrome

Chrome unfortunately doesn't provide the same easy configuration as Firefox, but that's not to say that disabling referer headers on Chrome can't be done.

The easiest way to disable referer headers in Chrome is to head over to the Chrome Store and grab the [Referer Control](https://chrome.google.com/webstore/detail/referer-control/hnkcfpcejkafcihlgbojoidoihckciin) browser extension.

![Referer Control screenshot](/images/your-referer-is-showing/referercontrol.png)

If you don't want to bloat your browser with additional extensions, you can also launch the Chrome app with the `--no-referrers` flag. If you're on Windows, you can do this by editing the Browser's shortcut icon execution path, e.g. `"C:\Users\Username\AppData\Local\Google\Chrome\Application\chrome.exe" --no-referrers`.

---

### Other Browsers

I was unable to find information on how to block referer headers in Opera, Safari and Internet Explorer. As such, I feel that it is not sensible for a privacy-conscious user to perform personal browsing via any of these web browsers.

If anyone has information on how to disable referer headers on any browsers aside from Mozilla Firefox and Google Chrome I'd be very interested to know about it. Drop me a line on Twitter at [@zpalexander](https://twitter.com/zpalexander).

---

### Side Effects

Disabling sending of header referers can break some sites. Some web developers use header referers to prevent [Cross Site Request Forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery):

{{< rawhtml >}}
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">If you want to disable the Referer header, do it in a way that still sends it within the domain. Otherwise you break many CSRF protections.</p>&mdash; Paul McMillan (@PaulM) <a href="https://twitter.com/PaulM/status/606573592076091392">June 4, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{{< /rawhtml >}}

In my opinion, [those developers are wrong](https://gispunt.wordpress.com/2012/01/10/why-using-referer-header-as-a-security-mechanism-is-a-bad-idea/) to rely on an easy to forge header in such a way that potentionally breaks accessibility. Nevertheless, its important to remember that disabling your browser's referer headers can cause problems such as these.

In conclusion, remember that referer headers are only passed on when you click through a link. Cutting and pasting a web address into a browser’s URL bar, or typing it out manually, will also prevent any referer headers being sent to the visited website. For more information, check out [this comprehensive article](https://blog.fastmail.com/2016/06/20/everything-you-could-ever-want-to-know-and-more-about-controlling-the-referer-header/) on the subject.
