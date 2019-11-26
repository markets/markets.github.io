---
layout: post
title:  Introducing Unscoped Associations
date:   2013-06-04
---

Ruby on Rails is a great framework with a lot of built-in options and customizations, specially regarding the data/models layer (Active Record). One of them is the ability to collect instances of a particular class using a `default_scope`. Very useful to collect "scoped" data by default, but pulling objects via associations is also affected, leading to weird behavior (think in "soft deletes").

This library allows you to easily skip the `default_scope` when navigating through your associations:

```
belongs_to :user, unscoped: true
```

Supported associations:

* `:belongs_to`
* `:has_one`
* `:has_many`

More information, source and follow up [here](https://github.com/markets/unscoped_associations).

### Installation

Add this line to your Gemfile:

```
gem 'unscoped_associations'
```

### Scenario usage

From now on you are able to use `unscoped` option in your association definitions. Basic usage example:

```ruby
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
@comment.user # => return user w/o taking account 'active' flag
```

### Status

Tested on Rails 3.x series and Rails 4.0.0. Originally thought and built for Rails 3, Rails 4 supported.

NOTE: Rails 4 introduces some updates (and more planned for upcoming releases) related to this part. For example, in Rails 4, you are able to customize associations using a scope block, so you can skip `default_scope`:

```ruby
class User < ActiveRecord::Base
  has_many :all_comments, -> { where public: [true, flase] }, class_name: 'Comment'
end
```

Anyway, you can use `unscoped` option, if you prefer.

### Contributing

Feel free to fork, send ideas, [bugs](https://github.com/markets/unscoped_associations/issues) or any comment.
