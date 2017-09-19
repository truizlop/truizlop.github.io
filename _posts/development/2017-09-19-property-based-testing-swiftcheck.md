---
layout: page
title: Property-based Testing in Swift - Using SwiftCheck
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

In the [previous post][1] we described an approach to testing known as property-based testing. Instead of writing examples to test our code, we write properties that the output must have. We can keep writing properties that constraint this output until we have an accurate specification of what we need our software to do.

Although it is possible to perform this type of testing without any kind of library, there are tools that will ease these tasks for us. In Swift, we can use [**SwiftCheck**][2], an open-source library in the spirit of Haskell's [**QuickCheck**][3].

Including SwiftCheck in your project is as easy as using **CocoaPods**. Just add `pod 'SwiftCheck', '~> <version>'`, replacing `<version>` with the latest release of the library. Install your pods and you are ready to go!

## Writing Property-based Tests with SwiftCheck

In the [previous post][1] we used a String concatenation example. We can rewrite the three property-based tests using SwiftCheck:

{% highlight swift %}
func testConcatLengthIsEqualToSumOfLengths() {
    property("Concatenation length is equal to sum of input lengths") <- forAll { (a : String, b : String) in
        let output = concat(first: a, second: b)
        return output.characters.count == a.characters.count + b.characters.count
    }
}

func testConcatIsAssociative() {
    property("Concatenation is associative") <- forAll { (a : String, b : String, c : String) in
        let output1 = concat(first: concat(first: a, second: b), second: c)
        let output2 = concat(first: a, second: concat(first: b, second: c))
        return output1 == output2
    }
}

func testConcatHasEmptyStringAsNeutralElement() {
    property("Concatenation has empty String as neutral element") <- forAll { (a : String) in
        let output = concat(first: a, second: "")
        return output == a
    }
}
{% endhighlight %}

Let's break down the structure of these tests:

1. We have to create a regular unit test by extending XCTestCase and adding methods prefixed with `test`.
2. Inside each unit test, we create a `property` with a `String` describing what we want to test.
3. With operator `<-`, we invoke `forAll`, which receives a closure. This closure accepts a number of arguments whose types implement the `Arbitrary` protocol. Don't worry about this for now, the library extends the basic Swift types for us. The arguments to this closure will be random Strings for our property-based test (the ones that we were creating with `randomString()` in the previous post).
4. In the body of the closure, we execute the methods or functions we want to test.
5. Finally, we don't need to write an assertion, but to return a `Testable` expression. In our case, we return a `Bool` from the comparison we are performing.

Running these tests will execute our closures multiple times. By default, each property is run 100 times, although this number can be increased or decreased.

As you can see, it is very easy to write property-based tests using SwiftCheck. In the final post of these series, we will show how the input to these tests can be constrained (sometimes we may be interested in a certain subset of the input type) and how to provide input for our custom types.

 [1]: https://truizlop.github.io/development/property-based-testing-introduction/
 [2]: https://github.com/typelift/SwiftCheck
 [3]: https://hackage.haskell.org/package/QuickCheck
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
 