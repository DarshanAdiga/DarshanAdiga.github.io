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

You can skip this step if you already have Ruby and Jekyll installed! I started from scratch, so I had to install Ruby, Jekyll and Bundle. I blindely followed these [installation steps](https://jekyllrb.com/docs/installation/#ubuntu) described for Ubuntu 16.04.

To verify if Jekyll is successfully installed and is of latest version, run this:

```bash
bundle exec jekyll --version
```

Which should give you (at the time of writing this post)

> jekyll 3.1.6

Yaay! I know have Jekyll installed!

## Setup a Quick-start template

First thing I did when I wanted to change the theme of my GitHub pages site was, look for other GitHub pages based sites and see what themes are being used. After spending hour or so, I ended up at this site called [Skinny Bones](https://mmistakes.github.io/skinny-bones-jekyll/) and I liked the simplicity of it.

I started following [these steps describing how to use this quick-start](https://mmistakes.github.io/skinny-bones-jekyll/getting-started/) template and customise it for myself.

After downloading the template and running below commands, I had a static site up and running at [http://localhost:4000](http://localhost:4000)

```bash
bundle exec jekyll build
bundle exec jekyll serve
```

Few keypoints here:

- Jekyll takes all the markdown content present under `_posts` directory and generates a static site content under `_site` directory
- Later the content of`_site` are served at [http://localhost:4000](http://localhost:4000)
- All the content is served at localhost over `http` schema

As of now, there was no content under `_posts` directory. Let's see how to add some posts and customize the theme.

##Configure and add the content

Let's look at basic set of things to configure in the selected template.

- The `_config.yml` file contains almost all the major properties related to your site. Set `title`, `description`,  and under `owner`, set the `name` and other fields

- The `url` property is used by Jekyll as the base url to serve your site's assets. It is importent to set the scheme properly otherwise you'll end up getting [mixed content error](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/what-is-mixed-content). In my case I set it to [https://darshanadiga.github.io/](https://darshanadiga.github.io/) But here, the problem is, you will not be able to server your site on localhost! So you might have to avoid setting the `url` when testing and once you are ready to push, you can set it to your address at which your site will be hosted.

- To add the navigation items, edit the `_data/navigation.yml` file. For example,

  ```yaml
  - title: POSTS
    url: http://darshanadiga.github.io
    excerpt: List of all posts
    image:
  
  - title: ABOUT ME
    url: http://darshanadiga.github.io
    excerpt: Who am I?
    image:
  ```

   Similarly, to add footer items, edit the `_data/footer.yml` file. For example,

  ```yaml
  - title: Home
    url: http://darshanadiga.github.io
  
  - title: About Me
    url: http://darshanadiga.github.io
  ```

This is enough to make it look like this is your own site. I'll update once I explore further.



Now let's add some content to your site.

Create some markdown data and name file something like `2018-05-21-test-data.md` and put this file under `_posts` directory.

Then rebuild and serve your site by

```bash
bundle exec jekyll build
bundle exec jekyll serve
```

Now visit [http://localhost:4000](http://localhost:4000) and there should be a post listed with a sample text from the `2018-05-21-test-data.md` file under the date `2018-05-21`.

Finally, add all these content (the `_site` directory can be ignored) into your GitHub Pages repository and your site with an awesome template should be up and running. 



Now you can go have a cup of coffee!

