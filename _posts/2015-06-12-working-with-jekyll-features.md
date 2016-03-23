---
layout: post
title:  "Working With Jekyll Features"
date:   2015-06-12 10:15:00
meta: "A helpful list of tips and resources, while setting up my blog."
categories: blog
tags: misc
featured: true
image: typewriter.jpg
---
With my site running on [GitHub Pages](https://pages.github.com/), I am using [Jekyll](http://jekyllrb.com/) to build out my site structure.  With its variable and templating support, customizing the site has been easy.  Here are some helpful tips I've noted along the way, which may be of use.

###Variables
Site related [variables](http://jekyllrb.com/docs/variables/) defined in the `_config.yml` are handy for global customization, which can be leveraged for pages, layouts, and "[includes](http://jekyllrb.com/docs/templates/#includes)".  A number of feature [variables](http://jekyllrb.com/docs/variables/) exist for customization, but custom variables can be added for additional configuration (for example, an additional `linkedin_username` or any other additional template value).  See below for an example of a custom variable, and a handy use.

###Template Composition
General page layouts can be defined in the `_layouts` folder, and then referenced in the [Front Matter](http://jekyllrb.com/docs/frontmatter/) of a page or post.

As a simple example, one can create a `simple.html` file in the `_layouts` folder with content like the following.

{% highlight html %}
<html>
  <head><title>{{ "{{ page.title " }}}}</title></head>
  <body>
  {{ "{{ content " }}}}
  </body>
</html>
{% endhighlight %}

Then, in a post `YYYY-MM-DD-test.md` in the `_posts` folder, the "layout" can be chosen in the "front matter".  The markdown content will be placed where the template placeholder exists within the layout HTML page.

    ---
    layout: simple
    title:  "Simple Example"
    ---
    Markdown content here...
    
For a more detailed example, the following syntax will enumerate the posts in the site using the `site.posts` variable (or for the top 10 recent posts, you can use `site.related_posts`).

{% highlight html %}
  <ul class="post-list">
    {{ "{% for post in site.posts " }}%}
      <li>
        <span class="post-meta">{{ "{{ post.date | date: '%b %-d, %Y' " }}}}</span>
        <h2>
          <a class="post-link" href="{{ "{{ post.url | prepend: site.baseurl " }}}}">{{ "{{ post.title " }}}}</a>
        </h2>
      </li>
    {{ "{% endfor " }}%}
  </ul>
{% endhighlight %}

###Controlling Excerpts
Excerpts are also available, and can be displayed using the `{{ "{{ post.excerpt " }}}}` variable.  Excerpts are automatically defaulted to the first paragraph, but can be controlled with the variable `excerpt_separator`.  The value can be set in the post front matter, or globally in the `_config.yml`.  Then the excerpt separator can be used to control the snippet used for the excerpt.  A number of other [Post Variables and Options](http://jekyllrb.com/docs/posts/) are available to fine tune and dial the behavior.

###Custom Variables
Any custom variables can be declared in the [Front Matter](http://jekyllrb.com/docs/frontmatter/) for a post or page, and then used. For example: a markdown post with a front matter like below...

    ---
    layout: post
    custom_navigation: landing
    ---

can then use the expression `{{ "{% if page.custom_navigation " }}%}` to detect if it is set, and `{{ "{{ page.custom_navigation " }}}}` to embed the value.  This can be handy when using a single layout for various posts, while having control over the layout at a post level (with a simple value in the post's front matter).

###Code Highlighting
In addition to the markdown syntax to drop in code blocks, the `{{ "{% highlight html " }}%}` block can be used with a specific language to use for syntax highlighting (in this example, `html`).  Also, adding `linenos` after the language will add line numbers to the block.
For a list of available languages (e.g. lexers), the Liquid template syntax uses [pygments](http://pygments.org/docs/lexers/).

###Escaping Content
When writing this post to give examples of template syntax, I needed to use liquid template escaping in order to escape a block like  `{% raw %}{{ "{% if page.title " }}%}{% endraw %}`.  This is done by placing `{% raw %}{{ "{% endraw %}` on the left of the syntax to escape, and `{% raw %}" }}{% endraw %}` just inside the right of the closing block.  For example, to escape `{{ "{{ page.custom_navigation" }}}}` place `{% raw %}{{ "{% endraw %}` on the left of the block to escape, like `{% raw %}{{ "{{ page.custom_navigation {% endraw %}` and place `{% raw %}" }}{% endraw %}` just inside the right side, like `{% raw %}page.custom_navigation " }}}}{% endraw %}`.  Or an easier option is to wrap the content using the `{{ "{% raw " }} %}` liquid template keyword (and appropriate closing `{{ "{% endraw " }} %}`).

I hope this helps!
