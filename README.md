# Translate

The main idea of this plugin is to keep all field translations on the same
table. I've worked with other Rails plugins but all of them have some caveats,
and I needed something really simple.

## Quick explanation

We have a page model with the following attributes.

    Page: title_en, title_es

By default Rails defines the current locale as `:en` and the default locale
to `:en`. If we add `translate :title` to our model we can get the content of
the attribute without defining the locale, and we'll get the localized field.

    @page_title = Page.first.title

We can change the locale with `I18n.locale`.

    I18n.locale = :es
    @page_title = Page.first.title

And we'll get the `title` attribute of the defined locale.

Sample migration

    class CreatePages < ActiveRecord::Migration

      def self.up
        create_table :pages do |t|
          t.string :title_en, :null => false
          t.string :title_es
          t.text :content_en, :null => false
          t.text :content_es
          t.boolean :status, :null => false, :default => false
        end
      end

      def self.down
        drop_table :pages
      end

    end

Model definition

    # app/models/page.rb
    class Page < ActiveRecord::Base
      translate :title, :body
    end

## Usage from the console

    $ script/console
    Loading development environment (Rails 2.2.2)
    >> I18n.default_locale
    => :en
    >> Page.first.title
    => "Hello World"
    >> I18n.locale = :es
    => :es
    >> Page.first.title
    => "Hola"
    >> Page.first.update_attributes :title => "Hola Mundo"
    => true
    >> Page.first.title
    => "Hola Mundo"
    >> I18n.locale = :undefined
    => :undefined
    >> Page.first.title
    => "Hello World"    <== Because is the default locale

Copyright (c) 2008-2010 Francesc Esplugas Marti, released under the MIT license
