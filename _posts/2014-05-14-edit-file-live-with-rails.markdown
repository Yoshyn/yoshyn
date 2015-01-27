---
layout: post
title:  "Editer un fichier en live avec rails (fr)"
date:   2014-05-12 14:03:43
categories: article
tags: rails
---

Un article un peu fourre-tout pour placer des snippets de code car :

  * Il est parfois utile de pouvoir éditer des fichiers en back-office d'un site web.
  * Il est parfois utile de faire de l'upload sans passer par un Uploadeur.

Cela peut etre par exemple :

  * Des fichiers de configurations
  * Un fichier de theme css (mon cas)
  * ...

Le truc, c'est qu'il s'agit généralement d'informations liées à un modèle. On n'a donc pas envie de toucher au controlleur pour faire cela.

Et bah en fait c'est assez simple à gérer avec ActiveRecord sans toucher aux controlleurs.

### Edition de fichiers :

Pres-requis :

  * On a un modèle (ModuleX)
  * Le fichier que l'on souhaite créer/éditer est le suivant : config/modulex.conf

L'astuce consiste à regarder l'implémentation de [update_attribute](apidock.com/rails/ActiveRecord/Base/update_attribut).

{% highlight ruby %}
def update_attribute(name, value)
  send(name.to_s + '=', value)
  save(false)
end
{% endhighlight %}

On va donc faire en sorte de répondre à cette méthode :

{% highlight ruby %}
class ModuleX < ActiveRecord::Base
  def file
    File.open(modulex_configuration_path, "rb") { |f| f.read } rescue ''
  end

  def file=(data)
    File.open(modulex_configuration_path, 'w') { |f| f.write(data) }
  end

  def file_path
     Rails.root.join('config', modulex.conf)
  end
end
{% endhighlight %}

Le getter ouvre le fichier. Le setter l'enregistre. Aussi simple que ça.

Il suffit après d'utiliser modulex_configuration comme n'importe quel champ active_record. :)

Puis, il est possible et assez simple d'utiliser [Ace Editor](github.com/ajaxorg/ace)  pour obtenir une interface sympa.

### Upload de fichiers :

Dans le cas ou l'on ne veut pas passer par un Uploader déja tout fait :

{% highlight ruby %}
# encoding: utf-8
require 'fileutils'

class ModuleX < ActiveRecord::Base

  # Constants
  FILES_PATH = Rails.root.join('public', 'files')

  # CallBacks
  before_validation :fill_directory,  unless: :persisted?
  after_validation  :build_directory, unless: :persisted?
  after_destroy     :remove_directory

  # Validations
  validates :name, :directory,        presence: true, uniqueness: true
  validate :validate_file?,          if: Proc.new { |model| model.file.present? }

  def file() @file end

  def file=(data)
    # data must be an actionDispatch class here. Something like :
    #<ActionDispatch::Http::UploadedFile:0x00000000000000 @original_filename="", @content_type="", @headers="", @tempfile=#<Tempfile:/tmp/file>>
    @file = File.join(files_directory, data.original_filename)
    if File.exist?(@file)
      @mark_file_as_existing = true
    else
      FileUtils.cp data.tempfile, @file
      FileUtils.chmod 0644, @file
    end
  end

  def files(mode = :url)
    files = files.map { |img| img.split('/public').last } if :path == mode
    files
  end

  def files_directory
    FILES_PATH.join(self.directory)
  end

private

  # CallBack to manage directory and make validation work

  # Validations
  def validate_file?
    errors.add(:file, "Le fichier #{File.basename(file)} est déja présente dans ce files.")               if @mark_file_as_existing
  end

  # Callback
  def remove_directory
    FileUtils.rm_rf(files_directory)
  end

  def fill_directory
    self.directory = name.parameterize('_')
  end

  def build_directory
    Dir.mkdir(FILES_PATH) unless File.directory?(FILES_PATH)
    Dir.mkdir files_directory unless File.directory?(files_directory)
  end
end
{% endhighlight %}























