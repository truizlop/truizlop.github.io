---
layout: page
title: War against Boilerplate | Code Completions
teaser: Writing code in Java for Android has sometimes a lot of boilerplate code associated. In this series of posts I will present some features in the IDE or the language that we can use to reduce writing such boilerplate.

categories:
    - development
tags:
    - android
    - boilerplate
    - code completion
breadcrumb: true
header:
    image_fullwidth: "header_development.png"
---

I love writing code, but I am particularly lazy when it comes to writing boilerplate code. Unfortunately, working mainly using Java in Android involves writing a lot of boilerplate every now and then. When this happens multiple times repeatedly, I try to avoid it by automating it somehow. As I imagine I may not be the only one having this issue, I have decided to start these series of posts to share some tips and tricks I have found to be useful so that anyone can take advantage of them.

In this post I will cover what I think is the least powerful of the features I use to reduce writing boilerplate, but still I think is worth sharing. Let's talk about **code completions**.

### Postfix completion

**Postfix completion** is a feature of IntelliJ IDEA / Android Studio consisting of generating a little code stub by typing some keywords after an expression. In order to trigger it, you must follow this steps:

1. Type a expression ending in `.`
2. Press `Ctrl` + `Space`
3. Choose among the alternatives: `field`, `var`, `cast`, `instanceof`, `not null`, `null`, `par`, `for`, `fori`...

Each one of the will wrap your expression with a code structure. For instance, having `List<String> names`, and choosing `fori` will generate:

{% highlight java %}
for (int i = 0; i < names.size(); i++) {
            
}
{% endhighlight %}

I don't find this particularly useful and don't use it regularly either since the way it has to be triggered does not feel very natural to me. Besides, it does not allow to create your own commands or customize the existing ones (or at least I was not able to find how). Anyway, if it works for you, go ahead and use it.

### Live templates

A similar feature are **live templates**. They are small shorcuts that generate parameterized code when you write certain keywords. The nice part of it is that you can create your own templates, which has been really handy to me. Besides, there are already many of them defined by default which you can use and check to find examples to define yours.

In order to create your live templates, you must follow these steps:

1. Create your Template Group and add Live Templates to it in Preferences > Editor > Live Templates
2. Fill the abbreviation (the shortcut you want to use to trigger the template), a description of its purpose and the code it displays.
3. Include variables in your code by surrounding them with dollar signs. For instance, `$NAME$` describes a variable.
4. Click *Edit variables* to be able to assign expressions or default values to your variables.
5. Define the scope where your template is applicable (e.g. is it only available in a Java file?)
6. Use it!

As an example of how you can use them, I have created a GitHub repository with multiple [live templates for the widgets in the Android Design Support Library][1], although I plan to extend it with many others. I had troubles remembering the complete package for each widget and the available XML attributes for each one of them, since the IDE does not suggest them. If I just wanted to create, for instance, a FAB; why would I have to remember the whole package structure, when I can just type **`fab`** and get it working?

Live templates are great to complete small pieces of code, but defining too many of them may make you forget which was the exact shortcut you need to trigger them. Just as when you write code, choosing a meaningful name is extremely important.


 [1]: https://github.com/truizlop/LiveTemplates
 [2]: #
 [3]: #
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
 