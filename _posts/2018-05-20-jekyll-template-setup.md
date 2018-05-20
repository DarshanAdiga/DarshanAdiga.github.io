# Jekyll Template setup for GitHub Pages

This website of my technical explorations is hosted by [GitHub Pages](https://pages.github.com) which uses [Jekyll](https://jekyllrb.com/) to generate the static site using [Markdown](https://en.wikipedia.org/wiki/Markdown) content. Initially, I had used one of the [default themes](https://pages.github.com/themes/) supported by GitHub pages. But soon, I got bored of it and wanted to try something different.

So I started looking for better Jekyll templates (other than the ones supported by GitHub Pages) for my posts and started setting up everything. After successfully having updated the template, I thought why not make a post about this adventure! 

I have two purposes for writing this post. First, this might be helpful for anybody who is looking to use a different theme than the default ones and second, a few years down the line when I decide to upgrade/move to some better templates I might need to follow the same procedure again. 

### Pre-requisites

This post assumes you already have [GitHub Pages enabled repository](https://pages.github.com) or at the least, you know how to create one. If not, please [visit here](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/) to know about it.

### Content

1. Install Ruby and Jekyll
2. Setup a quick-start template
3. Configure and add the content
4. Drink a cup of coffee!

## Install Ruby and Jekyll

You can skip this step if you already have Ruby and Jekyll installed! If you don't know, skip to last part of this step to know how to verify the version of Jekyll installed.

I started from scratch, so I had to install Ruby, Jekyll and Bundle. I blindely followed these [installation steps](https://jekyllrb.com/docs/installation/#ubuntu) described for Ubuntu 16.04.

To verify if Jekyll is successfully installed and is of latest version (as of 2018-05-20), run this:

```bash
bundle exec jekyll --version
```

Which should give you

> jekyll 3.1.6

Yaay! I know have Jekyll installed!



## Setup a Quick-start template

First thing I did when I wanted to change the theme of my GitHub pages site was, look for other GitHub pages based sites and see what themes are being used. After spending hour or so, I ended up at this site called [Skinny Bones](https://mmistakes.github.io/skinny-bones-jekyll/) and I liked the simplicity of it.

I started following [these steps describing how to use this quick-start](https://mmistakes.github.io/skinny-bones-jekyll/getting-started/) template and customise it for myself.

