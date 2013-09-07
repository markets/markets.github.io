---
layout: post
title:  Introducing Unscoped Associations
date:   2013-06-04
---

Ruby on Rails is a great framework with a lot of interesting options. One of them is the ability to collect instances of a particular class using `default_scope`. Very useful to collect "scoped" data by default, but pulling objects via associations is also affected.

It may be that you've ever needed to skip the `default_scope` (for some strange reasons). This library allows you to do it easily.

Supported associations:
* `:belongs_to`
* `:has_one`
* `:has_many`

More information and source [here](https://github.com/markets/unscoped_associations).

### Installation
Add this line to your Gemfile:

```
gem 'unscoped_associations'
```

### Scenario usage
From now on you are able to use `:unscoped` option for your association definitions. Basic usage example:

{% highlight ruby %}
class User < ActiveRecord::Base
  has_many :comments # or , unscoped: false
  has_many :all_comments, class_name: 'Comment',
                          unscoped: true
  has_one  :last_comment, class_name: 'Comment',
                          order: 'created_at DESC',
                          unscoped: true

  default_scope where( active: true )
end

class Comment < ActiveRecord::Base
  belongs_to :user, unscoped: true

  default_scope where( public: true )
end

@user.comments # => return public comments
@user.all_comments # => return all comments skipping default_scope
@user.last_comment # => return last comment skipping default_scope

@topic.user # => return user w/o taking account 'active' flag
{% endhighlight %}

### Status
Tested on Rails 3.x series. In Rails 4 you can customize data retrieving via a scope block, so you can skip default_scope using a block.

### Contributing
Feel free to fork, send ideas, [bugs](https://github.com/markets/unscoped_associations/issues) or any comment.
