---
layout: post
title:  Making DB backups with Ruby
date:   2014-03-14
---

The past week I mounted a DB backup system, for a couple of Ruby web apps (Rails and Sinatra), using the [Backup](https://github.com/backup/backup) gem. I found the process pretty straightforward, so I decided to share my experience.

The `backup` gem provides a very nice set of features:

- Databases support: MySQL, MongoDB, PostgreSQL, Redis ...
- Compression
- Encryption
- Storages: Amazon S3, Local, RSync, Dropbox ...
- Notifiers: Email, Twitter, Hipchat ...
- Friendly DSL
- The plugin is totally independent to Rails, so you can use it for other applications.

Backup model example (MySQL, Amazon, Gzip and email notifications):

{% highlight ruby %}
Model.new(:my_backup, 'My backup description') do
  database MySQL do |db|
    db.name     = "database_name"
    db.username = "username"
    db.password = "pass"
    db.host     = "localhost"
    db.port     = 3306
  end

  store_with S3 do |s3|
    s3.access_key_id     = "access_key_id"
    s3.secret_access_key = "secret_access_key"
    s3.bucket            = "bucket_name"
    s3.path              = "path/to/your/backups"
  end

  compress_with Gzip

  notify_by Mail do |mail|
    mail.on_success     = true
    mail.on_warning     = true
    mail.on_failure     = true

    mail.from           = "sender@email.com"
    mail.to             = "receiver@email.com"
    mail.address        = "smtp.gmail.com"
    mail.port           = 587
    mail.domain         = "your.host.name"
    mail.user_name      = "sender@email.com"
    mail.password       = "pass"
    mail.authentication = "plain"
  end
end
{% endhighlight %}

Perform the backup with the following command:

{% highlight bash %}
> backup perform --trigger my_backup
{% endhighlight %}

You can also schedule your backups with a cron job (for example using the `whenever` gem) and you'll achieve a simple and effective solution:

{% highlight ruby %}
every 1.day, :at => '1:00 am' do
  command "backup perform --trigger my_backup"
end
{% endhighlight %}

Hope this can help you!
