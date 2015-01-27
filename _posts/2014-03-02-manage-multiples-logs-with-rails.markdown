---
layout: post
title:  "Manage multiples logs with rails (en)"
date:   2014-03-02 13:53:0
categories: article
tags: rails
---

When you want to log something in rails, you probably write this :

{% highlight ruby %}
Rails.logger.info "My super log message"
{% endhighlight %}

And your log appears in your log file (for exemple developement.log). **Great**.

But, the problem is that you fill this file with a log information that 3/4 of time you don't need.

Today, you want to clearly visualize your logs in an admin panel, as follow :

  * All logs interfacing with an external
  * All logs concerning the back-office
  * All logs concerning an API

TO do this, we have to **save logs somewhere** and find a way to **distinguish differents types of logs**.

We clearly do not want to add custom code to manage this. So, what should we do?

## Log subscriber is your friend

[Log subscriber](http://api.rubyonrails.org/classes/ActiveSupport/LogSubscriber.html) is an object that consume ActiveSupport::Notifications.

The idea is to replace all our calls of:

{% highlight ruby %}
Rails.logger.info "My super log message"
{% endhighlight %}

By calls to ActiveSupport::Notifications :
{% highlight ruby %}
[:error, :info, :warn].each do |code|
   define_method code do |msg|
     ActiveSupport::Notifications.instrument("#{self.class.name.demodulize.underscore}.mon_module" kind: code, msg: msg)
     # First the name of the notification and name separated by a dot module and a hash of options
     # For instance, the 'kind' keyword refers to the type of notification (error, info or warn)
   end
end
{% endhighlight %}

The previous code overrides the 3 main logs methods and sends an ActiveSupport::Notifications when theses methodes are called :
{% highlight ruby %}
info "Mon super message de log"
warn "Mon super message de log"
error "Mon super message de log"
{% endhighlight %}


Ok, great. Now, We need to create a subscriber that will consume this notification:
{% highlight ruby %}
class LogSubscriber < ActiveSupport::LogSubscriber

  CLASS_LIST = [] # Set here a list of application classes that you want to use with the logger.

  CLASS_LIST.each do |code|
    define_method code do |event|

      payload = event.payload
      e_names = event.name.split(".")

      prefix = [e_names.last.upcase , e_names.first.classify ]
      prefix << event.time.strftime('%Y-%m-%d %H:%M:%S')

      kind = payload[:kind]
      send(kind, "[#{prefix.join(" - ")}] #{kind.upcase} : #{payload[:msg]}")
    end
  end
end
{% endhighlight %}

This log subscriber simply formats the notifications it receives. Once message is formatted, it makes a call to the base log rails through the method 'send'.
You can send the log in another place simultaneously. For instance, you can store the log in ElasticSearch.

And now, we test and.... Nothing! nitch, nada!

Indeed, there is no indication that the notification should be consumed by the subscriber. There's a missing little line :
{% highlight ruby %}
LogSubscriber.attach_to "mon_module"
{% endhighlight %}

That's it!

Now, you can send your unified logs in several places (Default rails logger, ElasticSearch, file...).
Create a new subscriber and register it.