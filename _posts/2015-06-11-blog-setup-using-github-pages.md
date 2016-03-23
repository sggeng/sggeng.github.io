---
layout: post
title:  "Blog Setup Using GitHub Pages"
date:   2015-06-11 18:45:00
meta: "A quick rundown on setting up a blog (like mine) on GitHub Pages."
categories: blog
tags: misc
---
In looking to setup my new site and blog, I had a number of specific things I wanted.  Easy publishing.  Markdown support.  Easy template modification.  Custom domain. Oh, and ideally free.  I ended up deciding to use [GitHub Pages][pages], and leverage `git` for publishing and GitHub for hosting.  Here is a quick summary of how to stand things up.

Setup is simply a matter of creating a repository under your account with the convention `(username).github.io`, where `(username)` is your GitHub account name.  To work with it, just use `git` to pull it down and publish.  For example, if you setup the repository on GitHub using `username`, then the following will pull it down locally.

{% highlight bash %}
$ git clone https://github.com/username/username.github.io
$ cd username.github.io
{% endhighlight %}

Since GitHub Pages uses the [Jekyll][jekyll] blog engine, standing up a file based site is easy.  A few simple commands will install Jekyll and create an initial site with a template.

{% highlight bash %}
$ gem install jekyll
$ jekyll new .
$ jekyll serve
# view the site locally being served at http://localhost:4000
{% endhighlight %}

Site settings are located in the `_config.yml` file, and will contain [global variables][global] for the site.  Configuration is done here using the built in variables, and custom ones can be added as well.

To add new posts, just add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.md`. It's just a markdown file, so content is easy to write.  A [front matter](http://jekyllrb.com/docs/frontmatter/) can be added to control which page template to use, and can also be used to control the title, date, link, etc...

Then once the site is built, just use `git` to push the contents and publish.

{% highlight bash %}
$ git add .
$ git commit -m "Initial commit"
$ git push -u origin master
{% endhighlight %}

This is great for publishing and version history.  `git` has everything covered.  Rollbacks are easily available as well by performing a `git revert [commit]`.

To use a [custom domain][domain], add a file CNAME (all caps) to the repository/project with the custom domain in the contents of the file (ex: www.domain.com).  Then, of course, update the domain registration to point to the GitHub Pages url.

To have a [custom 404][404] page, add a file 404.html (or 404.md for a markdown file) at the root level of the repository. Your content for those unexpected errors can now be controlled there.

For more details, everything is outlined in much better detail on the [GitHub Pages documentation][basics].

Hope this helps :-)

[jekyll]:      http://jekyllrb.com
[pages]:       https://pages.github.com/
[basics]:      https://help.github.com/categories/github-pages-basics/
[404]:         https://help.github.com/articles/custom-404-pages/
[domain]:      https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/
[global]:      http://jekyllrb.com/docs/variables/