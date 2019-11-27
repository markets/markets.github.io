---
layout: post
title:  "Rails Notifications: Subscribe to internal events"
date:   2013-08-02
---

We're all agree that it is very useful (and necessary) to know how our application is performing in production (detect bottlenecks, generate statistics, measure metrics, etc).

Rails 3 introduced a very easy-to-use mechanism to allow access for all internal notifications: `ActiveSupport::Notifications`. In fact, it's the system that Rails itself uses internally to log `ActiveRecord` and `ActionView` events.

### In action

To take a first look, we're going to print all notifications into the log file. We just need to subscribe to all notifications. For do this, add this piece of code to an initializer (/config/initializers/notifications.rb):

```ruby
ActiveSupport::Notifications.subscribe do |name, start, finish, id, payload|
  Rails.logger.debug("[NOTIFICATION] #{name} #{start} #{finish} #{payload}")
end
```

Restart the server and make a request. We'll see those lines in the application log:

```
[NOTIFICATION] start_processing.action_controller
  2013-08-01 20:10:45 +0200 2013-08-01 20:10:45 +0200
  {
    :controller=>"TopicsController", :action=>"index",
    :params=>{"action"=>"index", "controller"=>"topics"},
    :format=>:html, :method=>"GET", :path=>"/topics"
  }
[NOTIFICATION] sql.active_record
  2013-08-01 20:10:45 +0200 2013-08-01 20:10:45 +0200
  {
    :sql=>"SELECT  `topics`.* FROM `topics` LIMIT 10 OFFSET 0",
    :name=>"Topic Load", :connection_id=>92001860, :binds=>[]
  }
[NOTIFICATION] render_template.action_view
  2013-08-01 20:10:45 +0200 2013-08-01 20:10:45 +0200
  {
    :virtual_path=>"topics/index"
  }

...

[NOTIFICATION] process_action.action_controller
  2013-08-01 20:10:45 +0200 2013-08-01 20:10:45 +0200
  {
    :controller=>"TopicsController", :action=>"index",
    :params=>{"action"=>"index", "controller"=>"topics"},
    :format=>:html, :method=>"GET", :path=>"/topics", :status=>200,
    :view_runtime=>121.30655800000001, :db_runtime=>1.2731360000000003
  }
```

We can observe different kinds of notifications:

* `start_processing.action_controller`
* `sql.active_record`
* `render_template.action_view`
* `process_action.action_controller`

Very useful the last one. That notification includes information such as: the params, the path, the view_runtime, the db_runtime ...

We can subscribe to only one specific type of notifications by filtering them. To accomplish this, we'll pass a parameter to `subscribe` method:

```ruby
ActiveSupport::Notifications.subscribe(/action_controller/) do |*args|
  ...
end
```

Another interesting idea would be to store notifications into your database in order to manage information easily.

```ruby
# assuming Notification is an existing ActiveRecord class
ActiveSupport::Notifications.subscribe do |name, start, finish, id, payload|
  notification_params = {
    name: name,
    start: start,
    finish: finish,
    payload: payload
  }
  Notification.create(notification_params)
end
```

We can even implement more complex solutions. For example: store data to Memcached or Redis, and bulk it into a persisted storage periodically. Any good idea across your mind!

### Create custom notifications

We can create our own notifications and subscribe to them as well. For example:

```ruby
# create custom notification
ActiveSupport::Notifications.instrument('render') do
  ...
end

# subscribe to it
ActiveSupport::Notifications.subscribe('render') do |*args|
  ...
end
```

### Final words

There are some professional and well known tools to monitor our applications in production environments, like [RPM New Relic](https://newrelic.com), [Librato Metrics](https://metrics.librato.com/), [Riemann](https://riemann.io/). Totally right, full solutions with lots of options, but this API helps us to build for example: a custom case study, a custom logging, a self-made usage statistics, alerting, etc. Cool!

Be sure to check out the official [documentation](https://api.rubyonrails.org/classes/ActiveSupport/Notifications.html) for further information.
