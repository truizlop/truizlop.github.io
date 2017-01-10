---
layout: page
title: Property-based Testing in Swift - Introduction
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

This is the first instalment of a series on **Property-based Testing** (PBT) with Swift. In this first post, we will introduce the topic of PBT; the examples will be in Swift, but the underlying concepts are equally applicable to other languages. Later, we will make use of a library to ease the writing and execution of the tests; in such case, you will need to look for a similar tool in your preferred language. I have taken inspiration from [this series of posts][2] in F# when writing this. Let's get started!

## Motivation

You may be familiar with **Example-based Testing**. These are the tests that most of us start writing. For instance, let's assume that we are pairing in a TDD session to build a function to concatenate two strings. We can write the following test:

{% highlight swift %}
func testStringConcat() {
	let a = "Hello, "
	let b = "World!"
	let result = "Hello, World!"
	XCTAssertEqual(result, concat(first: a, second: b))
}
{% endhighlight %}

This is a test written following **Example-based Testing** since we are providing examples of how the function should behave. After writing this test, we hand our keyboard to our pair and she writes code to make it pass:

{% highlight swift %}
func concat(first : String, second : String) -> String {
    return "Hello, World!"
}
{% endhighlight %}

You may think this implementation is wrong, but actually it isn't. According to TDD rules, we should write the *simplest thing that can possibly work*, and later refactor if necessary. Our pair returns the keyboard; it seems we will need to write something more to force her write the right implementation:

{% highlight swift %}
func testStringConcat() {
	let a = "Hello, "
	let b = "World!"
	let result = "Hello, World!"
	XCTAssertEqual(result, concat(first: a, second: b))
        
	let c = "Another "
	let d = "example"
	let result2 = "Another example"
	XCTAssertEqual(result2, concat(first: c, second: d))
}
{% endhighlight %}

In response, our partner writes this implementation:

{% highlight swift %}
func concat(first : String, second : String) -> String {
    if first == "Another " && second == "example" {
        return "Another example"
    } else {
        return "Hello, World!"
    }
}
{% endhighlight %}

Again, you may think this implementation is wrong, but it is not; this is the result of applying the [**Transformation Priority Premise**][1]. It seems we will have to work harder on the tests. Let's create a bunch of examples and run the test on all of them:

{% highlight swift %}
func testStringConcat() {
    let examples = [ ("Hello, ", "World!", "Hello, World!"),
                     ("Another ", "example", "Another example"),
                     ("Property-based ", "Testing", "Property-based Testing"),
                     ("Swift ", "rocks", "Swift rocks"),
                     ("One more ", "thing", "One more thing")]
    
    examples.forEach{
        XCTAssertEqual($0.2, concat(first: $0.0, second: $0.1))
    }
}
{% endhighlight %}

We have created triples of values where the third component is the expected result of concatenating the first two. Then, we hand the keyboard to our partner and...

{% highlight swift %}
func concat(first : String, second : String) -> String {
    switch (first, second) {
    case ("Another ", "example"): return "Another example"
    case ("Property-based ", "Testing"): return "Property-based Testing"
    case ("Swift ", "rocks"): return "Swift rocks"
    case ("One more ", "thing"): return "One more thing"
    default: return "Hello, World!"
    }
}
{% endhighlight %}

At this point I can agree with you that the implementation doesn't seem to be the most correct one. This case seems obvious to us given its simplicity, but perhaps our partner is not able to see the repeating pattern in all these examples. Moreover, to the best of her knowledge, this implementation is valid for the whole universe of cases provided by the tests, so it must be correct!

Can you spot the problem? There is a relationship between the examples provided in the tests and the implementation. If we could hide the actual examples, then our partner would be forced to write a correct implementation. Assuming we have a function to generate random strings, we can write the following test:

{% highlight swift %}
func testStringConcat() {
    for _ in 0 ..< 100 {
        let a = randomString()
        let b = randomString()
        let expected = a + b
        XCTAssertEqual(expected, concat(first: a, second: b))
    }
}
{% endhighlight %}

And we repeat it multiple times to make sure that, by chance, our partner doesn't hardcode the solution. However, we are leaking an implementation detail by using + in our test, so this test is wrong. Can we try a different approach?

## Property-based Testing

Instead of using examples, we can use something that holds true for every two strings that we concatenate. For instance, if we concatenate two strings, its length is equal to the sum of the length of the two arguments. This is what we call a **property**. We can write a test like this:

{% highlight swift %}
func testConcatLengthIsEqualToSumOfLengths() {
    for _ in 0 ..< 100 {
        let a = randomString()
        let b = randomString()
        let result = concat(first: a, second: b)
        XCTAssertEqual(result.length, a.length + b.length)
    }
}
{% endhighlight %}

In this case, our partner cannot hardcode any data and we are not leaking implementation details into the test (note that I have added a extension field to `String` to get its length). What other properties does string concatenation have? For instance, if I concatenate three strings `a`, `b` and `c`, I can do `(concat(concat(a, b), c)` or `concat(a, concat(b, c))` and the result is the same. This property is called the **associative property** and can be tested like this:

{% highlight swift %}
func testConcatIsAssociative() {
    for _ in 0 ..< 100 {
        let a = randomString()
        let b = randomString()
        let c = randomString()
        let result1 = concat(first: concat(first: a, second: b), second: c)
        let result2 = concat(first: a, second: concat(first: b, second: c))
        XCTAssertEqual(result1, result2)
    }
}
{% endhighlight %}

And also, if we concatenate the empty string to any other string, we get the same string as a result; this is the **neutral element** property:

{% highlight swift %}
func testConcatHasEmptyStringAsNeutralElement() {
    for _ in 0 ..< 100 {
        let a = randomString()
        let result = concat(first: a, second: "")
        XCTAssertEqual(result, a)
    }
}
{% endhighlight %}

## Conclusion

The tests written above have some common structure that fits in the following logical proposition: *for all `x` in a domain, `p(x)` is true*, being `p` a property about `x`. Since our tests need to be finite, we have simulated the universal quantifier (for all) by generating 100 random instances, assuming that, if the implementation is wrong, some of them will eventually fail.

This type of testing forces us to focus on the specification, rather than on concrete examples. It makes you think in a more abstract way about what characterizes a solution, instead of how it is implemented. I don't mean you should drop Example-based Testing, since it is a very good tool for exploration and checking specific corner cases.

I hope you enjoyed the post and feel free to contribute on the comments if you have any question or feedback!


 [1]: https://8thlight.com/blog/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html
 [2]: http://fsharpforfunandprofit.com/pbt/
 [3]: #
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
 