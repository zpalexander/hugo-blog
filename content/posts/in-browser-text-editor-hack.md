---
title: "In Browser Text Editor Hack"
date: 2014-06-07T15:30:34-08:00
draft: false
featured_image: "images/in-browser-text-editor-hack/Writing.jpg"
aliases:
    - /in-browser-text-editor-hack/
---

Here’s a neat little hack.

Have you ever needed a place to write something down, only to realize you don’t have a piece of paper around? Have you ever fumbled to open your native text editor, only to forget the great idea or the telephone number you just had in your head in the process? This hack will create a simple text editor in your browser that you can bookmark and pop open whenever you need. Set it on your bookmarks toolbar and you’ll find yourself using it all the time.

To set this up, simply paste the following code into the URL bar of your browser:

```html
data:text/html,
<title>Text Editor</title>
<body contenteditable style="font-size:2rem; font-family:Helvetica; line-height:1.4; max-width:60rem; margin:0 auto; padding:4rem;">
```

Bookmark this page and you’ll never need a notepad again!
