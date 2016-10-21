---
layout: post
title:  "Make a wrapper like (almost) simple_form (en)"
date:   2016-10-22 13:53:0
categories: article
tags: rails
---

Have you ever ask you how to implement a wrapper like simple form ?

What is the purpose ?
  * Challenge of course
  * But also make your helper lighter
  * Or simply prepare to implement a simple_form like system

Here is an exemple of utilisation :

{% highlight ruby %}

    # Defined one wrapper (into an initializer for exemple)
    Formatters.setup do |config|
      config.wrappers :simple_wrapper do |dw|
        dw.tag     :div
        dw.classes 'simple_wrapper_column'
      end
    end

{% endhighlight %}

Use this wrapper over a string :

{% highlight ruby %}

    Formatters.find_wrapper(:simple_wrapper).wrap('This string must be wrapped!')

{% endhighlight %}

Which render

{% highlight html %}

    <div class="simple_wrapper_column">This string must be wrapped!</div>

{% endhighlight %}

Of course, we can imagine something more complicated :

{% highlight ruby %}

    Formatters.setup do |config|
      config.wrappers :awesome_wrapper do |dw|
        dw.tag     :span
        dw.classes 'awesome_wrapper_column'

        dw.wrapper tag: 'div', class: 'awesome_wrapper' do |wavc|
          wavc.prepend(tag: 'div', class: 'awesome_wrapper_prepend') do |prepend|
            prepend.content('PREPEND')
          end
          wavc.append(tag: 'div', class: 'awesome_wrapper_append') do |append|
            append.tag('span')
            append.classes('awesome_wrapper_append')
            append.content('APPEND')
          end
        end
      end
    end

    # Use this wrapper over a string :
    Formatters.find_wrapper(:simple_wrapper).wrap('This string must be wrapped!')

{% endhighlight %}

Which render :

{% highlight html %}

    <div class="awesome_wrapper">
      <div class="awesome_wrapper_prepend">PREPEND</div>
      <span class="awesome_wrapper_column">This string must be wrapped!</span>
      <span class="awesome_wrapper_append">APPEND</span>
    </div>

{% endhighlight %}

The code that make this work is commented and tested into this gist :

https://gist.github.com/Yoshyn/6f2bd8b67d366bf55de8677ab67b7736
