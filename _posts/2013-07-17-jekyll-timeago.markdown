---
layout: post
title:  "Jekyll-Timeago: Time ago date filter for Jekyll"
date:   2013-07-17
---

Custom and simple implementation of `timeago` date filter. Futures are also supported.

In fact, `jekyll-timeago` is an extension of <a href="https://github.com/Shopify/liquid" target="_blank">Liquid</a> filters, so you can use it in all your Liquid templates. Liquid is markup language extracted from the e-commerce platform <a href="http://www.shopify.com/" target="_blank">Shopify</a>.

Source code and bug tracking <a href="https://github.com/markets/jekyll-timeago" target="_blank">here</a>.

### Installation
Add this gem to your Gemfile and run bundle:

```
gem 'jekyll-timeago'
```

To enable extension into your Jekyll project add the following statement to a file in your plugin directory (_plugins/ext.rb):

```
require 'jekyll/timeago'
```

You can copy this [file](https://github.com/markets/jekyll-timeago/blob/master/lib/jekyll/timeago.rb) directly in your plugin directory (_plugins/) as well.

### Usage
Example usage:

{% highlight html %}
{% raw %}
<span>{{ page.date | timeago }}</span>
<h2>{{ page.title }}</h2>

<div class="post">
  {{ content }}
</div>
{% endraw %}
{% endhighlight %}

### Output samples
Some output examples:

{% highlight ruby %}
> timeago(Date.today)
=> "today"
> timeago(Date.today - 1.day)
=> "yesterday"
> timeago(Date.today - 10.days)
=> "1 week ago"
> timeago(Date.today - 100.days)
=> "3 months ago"
> timeago(Date.today - 400.days)
=> "1 year ago"
> timeago(Date.today + 1.days)
=> "tomorrow"
> timeago(Date.today + 10.days)
=> "in 1 week"
{% endhighlight %}

### Contributing
Any kind of idea, bug or comment are welcome.