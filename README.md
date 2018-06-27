PicoTags
========

<!--
@author Brice Boucard
@link https://github.com/bricebou/PicoTags/
@license http://bricebou.mit-license.org/
-->

Adds page tagging functionality to [Pico CMS](http://picocms.org/).

_Originally made by [Dan Reeves](https://github.com/danreeves/picotags), based on [Pico Tags by Szymon Kaliski](https://github.com/szymonkaliski/Pico-Tags-Plugin), but only uses the provided hooks and leaves the Pico core alone._

**/!\ This plugin is for the version 2 of [Pico CMS](http://picocms.org/). For previous versions, you have to use [this release](https://github.com/bricebou/PicoTags/releases/tag/v1.0).**


PicoTags plugin gives access to:
* The current page `meta["Tags"]` array
* The `tag_list` array of all used tags
* If on a `/tag/` URL, the `current_tag` variable

## Installation

__/!\ No matter the way you install the plugin, it's mandatory you name the plugin folder `PicoTags`.__ 

### Using Git

Just move to your Pico CMS `plugins` directory and run:

```
git clone https://github.com/bricebou/PicoTags
```

### Otherwise

Download the [latest zip archive from Github](https://github.com/bricebou/PicoTags/archive/master.zip) and unzip it inside a `plugins/PicoTags/` folder.

## Configuration

The configuration of the PicoTags plugin can be put inside the `config/config.yml` file of your Pico installation or inside a file named as you want inside the `config/` folder (for instance `config_plugin_tags.yml`).

Here is the conf with some explanations:
```
PicoTags.enabled: true
ptags:
  # Do you want to sort tags (case unsensitive) ?
  # true or false
  asort: true
  # Do you want to remove from the tags list
  # the ones that are used in only one page ?
  # true or false
  delunique: true
  # Specify the Twig template to use for the tag pages
  # The file has to be inside the theme in use folder
  # You have to indicate the `.twig` extension
  # See the template section into the README.md. 
  template: tags.twig
  # This is a way to split the tag_list array in multiple arrays.
  # See the template section into the README.md for more info 
  # on how to access them.
  nbcol: 2
  # Excluding pages from the tags list based on their template.
  # It has to be a string, with the `pipe (|)` as separator.
  # You have to escape single quotes with a backslash.
  # /!\ Be careful :
  # in some cases you can obtain 404 error
  # when there is no articles to display in a tag page.
  excluded_templates: "category|supcategory"
```


## Usage

### Describing your content

You simply have to add a 'Tags' attribute into the _meta_ of the pages you want.

__/!\ Make sure the _meta_ is capitalized: `Tags`: `tags` or `TAGS` won't work.__

```
/*
Title: LaTex and Phonetics
Description: LaTeX packages for students in linguistics
Author: Brice Boucard
Robots: index,follow
Date: 2017/10/02
Tags: LaTeX,Linguistics,package
*/
```

### In your templates

- To display the tags of an article, of a page, you simply have to call the `meta["Tags"]` array and let Twig do the job:

```
<article>
    <h2>{{ meta.title }}</h2>
    <p class="meta">Tags:
        {% for tag in meta["Tags"] %}
            <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
        {% endfor %}
    </p>
</article>
```

- When you want to display all the pages of your site and the associated tags, here is the code you can use:

```
{% for page in pages %}
    <article>
        <h2><a href="{{ page.url }}">{{ page.title }}</a></h2>
        <p class="meta">Tags:
            {% for tag in page.meta["Tags"] %}
                <a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a>
            {% endfor %}
        </p>
    </article>
{% endfor %}
```

- Listing all the tags used in your site:

```
<ul>
    {% for tag in tag_list %}
        <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
    {% endfor %}
</ul>
```

If you have used the `nbcol` option (let's say, with the value `2`), you can still access the `tag_list` array (all the tags) but also two new arrays:

```
<ul>
    {% for tag in tag_list_0 %}
        <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
    {% endfor %}
</ul>
<ul>
    {% for tag in tag_list_1 %}
        <li><a href="/tag/{{ tag }}">#{{ tag }}</a></li>
    {% endfor %}
</ul>
```

- Adding meta keywords to \<head\>:
```
{% if meta["Tags"] %}<meta name="keywords" content="{{ meta["Tags"] | join(',') }}">{% endif %}
```

- Tag pages: each tag has its own page, which URL looks like `example.com/tag/current_tag`. You have to create a specific template (see [Configuration section above](#configuration) in which you can simply paste this snippet in its `<body>`:
```
<h2>Posts tagged <a href="{{ page.url }}">#{{ current_tag }}</a></h2>
{% for page in pages %}          
    <article>
        <h2><a href="{{ page.url }}">{{ page.title }}</a></h2>
        <p class="meta">
            <span class="tags"><br />Tags :
                {% for tag in page.meta["Tags"] %}
                    <span><a href="{{ base_url }}/tag/{{ tag }}">#{{ tag }}</a></span>
                {% endfor %}
            </span>
        </p>
    </article>
{% endfor %}
```