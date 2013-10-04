---
layout: post
title:  Calling super in Ruby
date:   2013-10-04
---

Ruby, like most programming languages, has a set of reserved identifiers, keywords: `if`, `else`, `return`, `do`, `while`, `self`, `yield`, `true`, `false`, etc. 

Next we'll talk about `super`. We should use it when we want to take some behavior of an already defined method. For example, when implementing inheritance or overriding methods from a plugin. 

In Ruby context, this means "call the method with the same name along the classes and modules available to the current object".

When using `super`, be careful with syntax. Called with omitted arguments (without parentheses), it acts differently than passing an empty argument list:

* `super`: calls with the same arguments as those used to call the current method.

* `super()`: calls with empty arguments list.

### Scenario
Example how `super` performs in different cases:

{% highlight ruby %}
class A
  def initialize(*args)
    puts args.inspect
  end
end

class B < A
  def initialize(args)
    super()
    super # same as super(args)
  end
end

B.new('foo')
# => []
# => ["foo"]
{% endhighlight %}