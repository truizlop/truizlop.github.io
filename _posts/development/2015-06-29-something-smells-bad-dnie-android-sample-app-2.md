---
layout: page
title: Something smells bad in the DNIe sample Android app (Part 2)
teaser: A sample Android app has been published by the Spanish Homeland Ministry and the National Police to explain how to interact with the new version of the DNIe 3.0 (the Spanish ID card). However, the published code is not what we can consider "best practices" in software development.
categories:
    - development
tags:
    - app clinic
    - bad design
    - code smells
    - UI / UX
breadcrumb: true
header:
    image_fullwidth: "header_development.png"
---

In my previous article, I made an extensive, though not exhaustive, review of the code released as part of the sample app for the new DNIe 3.0 in order to analyze several aspects regarding the quality of the design and code. If you missed it, you can read it [here](https://truizlop.github.io/development/something-smells-bad-dnie-android-sample-app-1/ "Something smells bad in the DNIe sample Android app (Part 1)").

In this post, I will review Android-specific stuff that I considered worth mentioning. Although in the previous post I already pointed out several of them, I'll go into a more detailed description here. Let's get started.


## User Interface and User Experience

### Material Design

If you are reading this, it's likely that you have heard a little bit about [Material Design](http://www.google.es/design/spec/material-design/introduction.html "Material Design"). According to Google's definition, it is a visual language that synthesizes the classic principles of good design.

In its specification, it has plenty of details regarding aspects such as layouts, typography, color, metrics or patterns, among others. Although following these guidelines is not mandatory, it is highly advisable to follow them in order to be consistent with the look and feel of the platform. This ultimately leads to a better user experience and enhances user engagement. I also recommend to try to be a little creative to add some special detail to your UI to make you app distinguishable from what others have been doing.

The DNIe app, however, doesn't apply Material Design. Of course, this is not a commercial app and following the guidelines is optional, but there is also a trend in the design of UIs for Android apps that the development team could have followed. Moreover, making a nice UI even for a sample app reveals that you put an effort on to what you are doing. In this regard, Google recently released the Design Support Library with a bunch of new widgets to ease the implementation of Material Design. If you are interested in reading more about it, Antonio Leiva has [several very useful tutorials explaining it](http://antonioleiva.com/navigation-view/ "Antonio Leiva - Desing Support Library").

Nevertheless, aesthetics is something personal so I will not make any judgement about it.

### Content first

In the DNIe sample app, the main goal is to illustrate how to sign PDF documents. Since I don't have the actual DNIe 3.0, I could not test the whole screen flow, but the main three screens that we can see are the following:

<img class="t60" src="{{ site.url }}/images/dnie_screenshots.png">

As we can see, the home screen has a menu with two different options: pick a PDF to sign it, and see all the signed PDF documents in our device. I had several PDF documents in my device, but for some reason, none of them appears on the list and I am not able to guess if I have to put them into some special folder.

One of the main principles behind User Interface design for mobile applications is "Content first", meaning that we should focus on the information that we are going to provide, and provide it as easily as possible to the user.

In our case study, the core element is the PDF document. Therefore, the UI should be design around this concept. Following this principle, and given that there are two possible actions both involving listing PDFs which can be in mutually exclusive states (signed and not signed), it seems that an appropriate way to design the home screen would be a `ViewPager` with two tabs, the first one with the unsigned documents and the second one with the signed documents. This way, both listings can look consistent with each other, unlike they were designed in the sample app. I will present a mockup later to illustrate my proposal.

### Empty state

Another important aspect is how to handle the empty state when showing a collection of items. Taking a look at the image above, we can see several things worth mentioning:

1. The unsigned PDFs view has a non-standard widget to show a collection of items, with what seems to be on-screen control arrows. This is something very unusual in mobile development and wrongly adopted from UI design for desktop applications.
2. Both screens have instructions about how to proceed in that screen. Such instructions doesn't seem to disappear when there are items on the list.
3. Their look and feel doesn't look consistent, even though they are semantically related by the type of items presented.

Having instructions on screen is taking an important part of the screen that can be used to display relevant content. Possible approaches to solve this issue are:

1. If using a `ListView`, you can set the instructions view to its `setEmptyView` method, so that they are only displayed when the collection is empty.
2. Although `ListView` can still be used, `RecyclerView` is a better choice. However, it lacks a way of setting an empty view to handle the empty state. Nevertheless, this shouldn't be a problem; you only need to register an `AdapterDataObserver` to the `RecyclerView` and hide/show the empty view upon convenience.
3. If you still want the instructions appear all the time, you can set them in the header of a `RecyclerView` so that they scroll when you have larger lists of documents. To this purpose, I recommend you to use `HeaderRecyclerView` by my colleagues at [Karumi](https://github.com/Karumi/HeaderRecyclerView "HeaderRecyclerView by Karumi").

### Navigation

Navigation is something particularly wrong and confusing in this app. First, they have decided to remove the `Toolbar` from the top of every screen. The `Toolbar`/`ActionBar` has become a standard way to navigate in Android apps. It usually provides up navigation, side drawer, tabs or frequent actions, depending on how you decide to organize your UI. Instead, the developers of this app decided to remove it and place an image with credits of the public organisms who funded this project, which is present in every screen of the app. Again, this is not a commercial app, but it is far from what [Google recommends for branding](http://www.google.com/design/articles/expressing-brand-in-material/ "Expressing brand in Material").

However, there is something that annoys me specially: the on-screen back button. It reminds me of this video by Reto Meier where he explains why [having an on-menu exit button is really bad](https://www.youtube.com/watch?v=631T7B8HOv4 "Reto Meier - Table flip"). Android phones have a back button either physical or on screen by default. Including an extra one at the bottom of the screen is redundant and takes a valuable space that could have been used to show relevant information.

Moreover, one could expect that the action performed by this event is to finish the current `Activity` and go back. However, that is not the case:

{% highlight java %}
Button btnPrKBack = (Button)findViewById(R.id.Btn_BACK);
		
btnPrKBack.setOnClickListener(new OnClickListener() {
    public void onClick(View v) {
        // Volvemos al Activity de lectura
        Intent intent = new Intent(pdfload.this, DNIeMcu.class);
        startActivity(intent);
    }
});	
{% endhighlight %}

It launches a new `Activity` to display the previous screen. Thus, when you go back and forth several times between these activities using the on-screen back button, and then you press the device back, navigation becomes very confusing, specially when you are at the home screen and you expect to get out of the app, but suddenly it seems that you are moving forward. Something that could have easily fixed just by doing:

{% highlight java %}
Button btnPrKBack = (Button)findViewById(R.id.Btn_BACK);
		
btnPrKBack.setOnClickListener(new OnClickListener() {
    public void onClick(View v) {
        finish();
    }
});	
{% endhighlight %}

### Proposal

Considering all these comments, I decided to create a little mockup of my approach to a simple app like this. Of course, it can be refined much more, but I just wanted to somehow refactor the existing UI into something that fits better in the Android system. If you have some feedback, I would love to hear about it in the comments.

<img class="t60" src="{{ site.url }}/images/dnie_mockup.png">

## Assets

### Resources

There is not too much to discuss here, but something dragged my attention. I found many assets that include the text to be displayed in buttons. This has several problems. First, if you want to change the text to be displayed, you have to modify the assets and generate versions for every density, which is a time consuming task. Besides, it makes your app difficult to translate to other languages.

### I18N / L10N

Regarding internationalization / localization, maybe it is not something very important because the DNIe is something to be used just in Spain, but still, managing this issues carefully reveals a special dedication to your job. I have found that most of the strings in the app are included into the `strings.xml` file, as they were supposed to be, but much others have been hardcoded, making it impossible to easily translate the app. Moreover, plurals are handled with conditional statements, but a [more suitable mechanism is provided to handle them](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals "Quantity Strings (Plurals)").

---

I hope you found this post useful and if you want to share anything or discuss some of the topics addressed here, feel free to comment below.


 [1]: #
 [2]: #
 [3]: #
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
 