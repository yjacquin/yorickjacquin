---
title: "Understanding the Ruby Method Lookup Chain"
author: "Yorick Jacquin"
date: 2020-03-30T06:34:05.657Z
lastmod: 2020-04-26T16:59:55+02:00

description: ""

subtitle: "Unveiling a bit of Ruby’s magic under the hood"




aliases:
    - "/understanding-the-ruby-method-lookup-chain-d6f9d7997849"
---

#### Unveiling a bit of Ruby’s magic under the hood




![image](https://cdn-images-1.medium.com/max/800/0*30mrS9RLd45EZ54N)

Photo by [Agence Olloweb](https://unsplash.com/@olloweb?utm_source=medium&amp;utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&amp;utm_medium=referral)



When you call a method in Ruby, a lot of things happen behind the scenes. The Ruby interpreter has to know where this method is declared to know what behavior it has to interpret.

This process is called _the method lookup._

It goes through the ancestor chain of an object to see where this method is declared.

My good friend Mehdi Farsi wrote an [article explaining the ancestor chain](https://medium.com/rubycademy/ruby-object-model-part-1-4d06fa486bec). I can’t explain it better than him, so if you’re not familiar with Ruby’s ancestor chain, I’d recommend you check it out.### So How Does It Behave?

Let’s say you have the following classes:




The `Dog` class inherits from the `Animal` class, which defines a `#speak` method.

So we can do the following:
`dog = Dog.new  
=&gt; #&lt;Dog:0x00007ff5b78e9270&gt;``dog.speak  
&#34;I am from the Animal class&#34;  
=&gt; &#34;I am from the Animal class&#34;`

By simple inheritance, our instance of the `Dog` class has access to its parent’s `#speak` method.

But how exactly does Ruby know where to find this method?

Well, it’s quite simple.

It checks the object on which the method is called (in this case, an instance of the `Dog` class) using an approach similar to the following:

The `#instance_methods` returns, as the name suggests, the methods of an instance as an array. In this case, `Dog.instance_methods` returns a bunch of methods inherited from its ancestors, including our `#speak` method:
`Dog.instance_methods``=&gt; [**:speak**, :instance_variable_defined?, :remove_instance_variable, :instance_of?, :kind_of?, :is_a?, ...]`

But this isn’t what we want, as we only want the methods defined within the `Dog` class, without the methods of its ancestors.

To do that, we pass `false` to `instance_methods` to only get the methods defined within the `Dog` class:
`Dog.instance_methods(false)  
=&gt; []`

We can conclude that Ruby uses something similar to:
`Dog.instance_methods(false).include?(:speak)  
=&gt; false`

Now that we know the `Dog` class doesn’t define the `#speak` method, we can go one class up in the ancestor chain to the `Animal` class.
`Dog.ancestors  
=&gt; [Dog, Animal, Object, Kernel, BasicObject]`

It proceeds to the `Animal` class, where it finds the holy grail.
`Animal.instance_methods(false)  
=&gt; [:speak]`

The algorithm is straightforward:

1.  Examine the current object to see if it defines the method — if not, move to step 2.
2.  As long as it’s not defined, it invokes its superclass and repeats step 1.### Conclusion

This algorithm is what makes the lookup chain hermetic without method overlap and what makes overriding methods possible.

Here’s a quick example:




We can see both the `Dog` class and the `Animal` class define the `#speak` method. So upon invoking this method on a `Dog` instance …
`Dog.new.speak  
&#34;I am from the Dog class&#34;  
=&gt; &#34;I am from the Dog class&#34;`

… the algorithm checks if the method is defined in the object it is invoked on. In this case, it’s an instance of `Dog`:
`Dog.instance_methods(false)  
=&gt; [:speak]``Dog.instance_methods(false).include?(:speak)  
=&gt; true`

Then, it stops — as we’ve found our winner.
