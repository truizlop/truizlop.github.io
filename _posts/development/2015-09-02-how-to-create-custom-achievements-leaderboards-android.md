---
layout: page
title: HOW TO - Create your custom Achievements list and Leaderboards with Google Play Games in Android
teaser: Google Play Games is a good library to include gamification elements into your app, such as Achievements and Leaderboards. In this tutorial, I will show how to implement your custom visualization for Achievements and Leaderboards.

categories:
    - development
tags:
    - google play games
    - tutorial
    - achievements
    - leaderboards
breadcrumb: true
header:
    image_fullwidth: "header_development.png"
---

I have been using **Google Play Games Services** for Android in a project that I am currently working on, and I have encountered that the API lacks some important things that I would like to do.

For instance, in order to present an **Achievement** list or a **Leaderboard**, the API provides an `Intent` that shows that for you. It is really nice to have that fallback, but usually it will not go well with the look and feel of your app/game. Besides, you don't want your app to look like everyone else's!

According to the documentation, it is possible to create your own visualization of Achievements and Leaderboards, but there is no direction on how to proceed with that (or at least, I was not able to find it).

After digging a little bit into the documentation, I found there isn't a convenient method call that returns a list of all achievements or parts of a leaderboard. Thus, I have created a couple of gists to show you how to do it.

To be able to retrieve the achievements list, you have to proceed as follows:

<script src="https://gist.github.com/truizlop/fb4bf585e242306370bb.js"></script>

Note that this is an asynchronous call. Now that you have a list of Achievements, you can display them in whatever way you prefer. Take a look at the [documentation][1] for the `Achievement` class to know what information is available.

To be able to retrieve the leaderboards, you have to provide a little bit of additional info, such as the leaderboard ID you want to retrieve, the time span you are looking for (daily, weekly or all time), and the collection (public or social). Check the [documentation][2] for `LeaderboardVariant` to see the possible values that these parameters can take.

<script src="https://gist.github.com/truizlop/22b7241714844f489f7c.js"></script>

With this gist, we are able to load either top scores or user-centered scores. Note that this piece of code is only retrieving at most 25 scores for the leaderboard you are requesting. Loading more result pages is left as an exercise for the reader. Once you have your score list, you can display it however you'd like. Take a look at the [documentation][3] for `LeaderboardScore` to know what information is available for you to display.

I hope you find this useful and if you have any comments or questions, please, leave them below!

 [1]: https://developers.google.com/android/reference/com/google/android/gms/games/achievement/Achievement
 [2]: https://developers.google.com/android/reference/com/google/android/gms/games/leaderboard/LeaderboardVariant
 [3]: https://developers.google.com/android/reference/com/google/android/gms/games/leaderboard/LeaderboardScore
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
 