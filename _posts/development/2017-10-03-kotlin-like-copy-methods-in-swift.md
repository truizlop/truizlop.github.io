---
layout: page
title: Kotlin-like copy methods in Swift
teaser: Creating copies of objects with small modifications is easy in other languages like Kotlin; however, it requires some boilerplate in Swift. But that can change.

categories:
    - development
tags:
    - swift
    - iOS
    - Kotlin
    - boilerplate
    - Xcode Source Extension
breadcrumb: true
header:
    image_fullwidth: "header_development.png"
---

Immutability is a very desirable property of our data structures. It means that the internal state of our instances does not change after their creation. It has multiple benefits, ranging from thread-safety to being easier to reason about them.

Swift even encourages developers to make data structures immutable. For instance, `structs` are, by default, immutable, and methods that alter the internal state of a `struct` must be marked with the `mutating` keyword.

However, sometimes we need to make changes to the state of an object. In such case, the recommendation is to create a new instance of an object, with the appropriate modifications. Languages like Kotlin or F\# provide methods to do that. In Kotlin, for instance, you have a `copy` method where you can pass named arguments matching the fields you want to modify, and it will return a copied instance with the same fields as the original one, and the changes you set in the call arguments.

Unfortunately, Swift does not have this feature by default, but luckily for us, it can easily be added.

## Adding a copy method

Let's assume we have the following data structure:

{% highlight swift %}
struct Person {
  let name : String
  let surname : String
  let address : String
  let age : Int
}
{% endhighlight %}

We can create a method to return a copy of this object, with some modifications, as follows:

{% highlight swift %}
func copy(name : String? = nil,
          surname : String? = nil,
          address : String? = nil,
          age : Int? = nil) -> Person {
    return Person(name : name ?? self.name,
                  surname : surname ?? self.surname,
                  address : address ?? self.address,
                  age : age ?? self.age)
}
{% endhighlight %}

The recipe is straightforward:

1. Create a `copy` method that returns a new instance of its type.
2. Add an argument to the `copy` method for each field in the class/struct.
3. Make the type of each argument optional.
4. Add `nil` as the default value of each parameter.
5. In the body of the method, just return a new instance, choosing the arguments that were sent to the `copy` method, or the existing values if they were not set (using the ?? operator).

## Removing the boilerplate

As you can see, the procedure is really simple but involves writing some boilerplate. However, we can get rid of it. If writing the `copy` method is so automatic, let's automatize it with an Xcode Source Extension!

I have created and open-sourced such an extension, which will allow you to select the fields of a class or struct, and generate both a constructor (`init` method) and a copy method, automatically. You can find it in my [Github account][1].

Working with the extension still has some limitations; for instance, if you add new fields to your data structure, you will have to delete and recreate the generated code. Nevertheless, I still find it useful to save me from writing some boilerplate - and I hope you can find it useful as well.

 [1]: https://github.com/truizlop/CopyGenerator
 [2]: #
 [3]: #
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
