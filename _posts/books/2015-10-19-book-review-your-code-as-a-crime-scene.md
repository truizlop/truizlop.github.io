---
layout: page
title: Book Review Your Code as a Crime Scene
teaser: Achieving perfection in code is nearly impossible. That's why we have refactoring; to help us improve our code when we determine something *smells bad* in our design. But sometimes things might not be obvious. For those cases, **Your Code as a Crime Scene** is a great book to provide you with tools to find places in your code where something is screaming for a refactoring.

categories:
    - books
tags:
    - book review
    - refactoring
    - architecture
    - forensics
    - social psychology
breadcrumb: true
header:
    image_fullwidth: "header_books.png"
---

I have previously mentioned this book when wrote about [the important role of psychology in software engineering][1]; by that time I didn't even have the book, but now that I bought it and read [Your Code as a Crime Scene][2], by Adam Tornhill, I believe it deserves a separate post to discuss it.

<p align="center">
<img width="300" src="https://imagery.pragprog.com/products/426/atcrime.jpg?1426617929"/>
</p>

I have to say that I really enjoyed reading the book. It was really easy to follow and full of illustrative and cool examples that serve very well to make the point Adam wants to transmit. I am very impressed how he relates his knowledge on the field of Psychology to Software Engineering and how things fit so smoothly. The book is divided into three parts that I will briefly summarize here.

### Evolving Software

This part of the book covers how to use several tools (coded by the author) to analyze version control systems (VCS) in order to find potential candidates for improvement; those *bad smells* that we refer to when we speak about **refactoring**. I had never thought about it previously, but it's amazing the amount of information that we can extract from a CVS, even though they were not conceived for this purpose. Combining this information with simple heuristics like analyzing class/module namings or indentation levels, we can find *hotspots* in our code where we should further investigate in order to determine if we should undertake extra action to improve our design.

### Dissect Your Architecture

Once we have found potential hotspots in our code, in this part of the book the author analyzes their relationship with the big picture: the architecture of your software.

By analyzing commits made along time, we can detect *temporal coupling* between modules that apparently have no relationship. This is also a tool to discriminate places in our design where we should invest longer time to make improvements.

### Master the Social Aspects of Code

Finally, Software Engineering is a social discipline and it is reflected as well in the CVS. Performing various types of analyses, we can extract information about, for instance, who should be able to fix a problem, or if there are too many people working at the same time in a module, flag it as a candidate to split it into different ones with a defined responsibility.

### Conclusion

Refactoring is a kind of optimization, and as such, it has to be performed with educated decisions based on metrics. **Your Code as a Crime Scene** provides a nice set of tools to obtain such metrics and guide your improvements to your design. It is a great handbook if you have to deal with legacy systems, where you usually have little clue about where to focus your efforts. Even if you don't deal with them, I recommend you to try the tools (they are open source, by the way) in your own repositories - for sure you are going to learn things about your coding style.


[1]: https://truizlop.github.io/development/the-important-role-of-psychology-in-software-engineering/
[2]: https://pragprog.com/book/atcrime/your-code-as-a-crime-scene

