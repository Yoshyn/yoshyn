---
layout: post
title:  "Class attribute VS constant (fr)"
date:   2014-03-11 18:47:43
categories: article
tags: ruby
---

J'ai récemment eu deux problèmes avec les constantes définies dans une classe :

  * Problème de définitions multiples à cause de l'utilisation de concerns et de gems (plusieurs engines définis dans des gems utilisaient le meme concern où une contante était défnie).
  * Problème lors de l'héritage.

Exemple :

{% highlight ruby %}
class Base
  DEFAULT_VALUES = { key: 'value1', key2: 'value2' }
end

class Extended < Base

  # WRONG : change the value of Base::DEFAULT_VALUES
  # DEFAULT_VALUES[:key2] = 'Extended value2'

  # GOOD : work but does not fix the first issue
  DEFAULT_VALUES = Base::DEFAULT_VALUES.merge(key2: 'Extended value2')
end
{% endhighlight %}

J'ai donc opté pour l'utilisation de class_attribute présent depuis rails 3.

**Avantages :**

  * On est sûr que le class_attribute ne sera initialisé qu'une seule fois.
  * On peut surdéfinir le class_attribute sans problème après en avoir hérité.

> **Note :** Attention au array et au hash en cas de redéfinition.

{% highlight ruby %}
class Base
  class_attribute :default_values
  self.default_values = { key: 'value1', key2: 'value2' }
end

class Extended < Base
  self.default_values = default_values.merge(key2: 'value2 custom')
end

Base.default_values  # {:key=>"value1", :key2=>"value2"}
Extended.default_values  # {:key=>"value1", :key2=>"value2 custom"}
{% endhighlight %}