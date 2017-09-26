---
layout: page
title: Property-based Testing in Swift - Generators
teaser: Did you know you can write hundreds of tests with very little effort? If you are as lazy as I am, keep reading to learn how!

categories:
    - development
tags:
    - swift
    - iOS
    - testing
    - property based testing
    - functional programming
breadcrumb: true
header:
    image_fullwidth: "header_development.png"
---

We have seen how easy it is to [create property-based tests with SwiftCheck][1] in previous posts. However, so far we have written properties that are universally valid (i.e. they are true for every input from a given type) and properties that work with primitive types (i.e. we have not created properties for custom types as input). In this post we are going to review how we can constraint the inputs and create property-based tests for our own types. It is all about **Generators**.

## Constraining input with Generators

In the [last post][1] we mentioned that the closure we pass to the `forAll` function in our tests receives a number of arguments whose type implements the `Arbitrary` protocol. This protocol requires the implementing type to provide, among other things, a static variable which generates values of such type. This is what we call a **Generator**. Generators are passed to the forAll function in order to provide random values for input.

But we didn't pass a generator in our previous tests, did we? That is because SwiftCheck uses the default Generator for a type if we do not specify otherwise. For example:

{% highlight swift %}
property("Whatever property") <- forAll { (a : String) in ...
{% endhighlight %}

Is equivalent to:

{% highlight swift %}
property("Whatever property") <- forAll(String.arbitrary) { (a : String) in ...
{% endhighlight %}

Where `String.arbitrary` is the default random String generator provided by the library. Therefore, we can pass a custom generator if we want to control what kind of input our code under test is going to receive.

For instance, if we have a function that only operates on positive integers, we can customize our generator with:

{% highlight swift %}
let positiveIntsGen = Int.arbitrary.suchThat{ n in n > 0 }
{% endhighlight %}

Or if we are testing a function that needs an uppercase character as an input, we can create a generator like:

{% highlight swift %}
let uppercaseCharacterGen = Gen<Character>.fromElementsIn(Character("A")...Character("Z"))
{% endhighlight %}

And then we just need to feed it to `forAll` in order to use it instead of the default one. `Gen` has multiple methods that ease the creation of your custom generators.

## Creating Generators for your Custom Types

So far we have only used primitive types, but in real life scenarios you will have to deal with more complex domains with custom types. In such cases, you will have to provide generators for them.

Let's use [this repository][2] for the Tennis Kata as an example. You can read a full description about it in [this series][3] of posts by Mark Freeman.

For instance, in this Kata we have an enum to represent a `Player`, which can be `Player.one` or `Player.two`. To be able to write a property based test that uses `Player` as an input, I have to extend it:

{% highlight swift %}
extension Player : Arbitrary {
    public static var arbitrary : Gen<Player> {
        return Gen<Player>.fromElementsOf([Player.one, Player.two])
    }
}
{% endhighlight %}

Using `fromElementsOf` we can pass a collection of possible values and it will randomly pick one of them for each execution of the test.

Similarly, `Point` is another enum that represents the possible points that a player can have (0, 15, 30). Based on that, there is a state of the game where both players should have some points, which is modeled in the `PointData` struct. In this case, we can create a generator for `PointData` by composing the one that we have por `Point`:

{% highlight swift %}
extension PointData : Arbitrary {
    public static var arbitrary : Gen<PointData> {
        return Gen<(Point, Point)>.zip(Point.arbitrary, Point.arbitrary).map(PointData.init)
    }
}
{% endhighlight %}

We zip the generators for `Point`, which will create pairs of points (e.g. (15, 30)) and then we tell the generator to map this pairs to the `PointData` constructor, creating an instance with both elements of the pair corresponding to the first and second players, respectively. As you can see, SwiftCheck allows us to create more complex generators by composing smaller, simpler ones.

Finally, at some points you will need to generate arrays of objects. To do so, SwiftCheck provides `ArrayOf<T>`. Thus, if we need to generate a list of players with length smaller than some number, we can write the following generator:

{% highlight swift %}
func generateSequenceOf(lengthSmallerThan n : Int) -> Gen<ArrayOf<Player>> {
        return ArrayOf<Player>.arbitrary.suchThat{ $0.getArray.count < n }
}
{% endhighlight %}

`ArrayOf<Player>.arbitrary` would give as arrays of arbitrary length, which we are constraining with `suchThat` method, just as we would do with primitive types.

## Conclusion

Writing property-based tests is very easy with SwiftCheck. The library provides several utilities to ease the creation of this type of tests as well as generators of random input. Although there are some topics that I didn't cover on these posts (like shrinkers, for instance), I think these notions are enough to get you started. Go on and apply it to your Swift codebase!

 [1]: https://truizlop.github.io/development/property-based-testing-swiftcheck/
 [2]: https://github.com/truizlop/TennisKataSwift
 [3]: http://blog.ploeh.dk/2016/02/10/types-properties-software/
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
 