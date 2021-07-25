---
title: "How to Make a Blog Like This for Yourself"
date: 2020-06-14
categories:
  - blog
tags:
  - manual
---

This post captures my experience of setting up this personal blog.

I wanted to create a personal site for a while, but it took almost half a year to find time to finish it (thank you, COVID quarantine). This post is meant to be a log for myself, but if you're looking for a way to set up your personal site in an hour or so you may find it useful.

# Setting up Tools 
## Ruby
Jekyll is a Ruby-based personal blog framework, so you will need Ruby on your machine if you want to test-launch your site locally before pushing it online.
Every Mac comes with a pre-installed Ruby distribution but for reasons described [here](https://thoughtbot.com/blog/psa-do-not-use-system-ruby) we won't use it. 
Instead, we install a local one as described in the [Ruby site](https://www.ruby-lang.org/en/documentation/installation/#homebrew): 

```bash
 brew install ruby
```

It will tell you to add the new Ruby location to your `PATH` variable. I have a zsh shell, so for me, it would be like:

```bash
 echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc
```

After you reload the source and execute:

```bash
 which ruby
```

it should be something like this:
```
 /usr/local/opt/ruby/bin/ruby
```

If it still points to the system ruby (`/usr/bin/ruby`) then something went wrong, and you need to debug your PATH. 

## Jekyll 
Now we install two gems (Ruby plugins) into our brand new local Ruby:
```bash
 gem install bundler jekyll
```

## Site Repository

Github provides a free hosting service called [GitHub Pages](https://pages.github.com). All you need to do is to create a GitHub repository named `*username*.github.io`
where *username* is your GitHub username. My GitHub username is `aksholokhov`, so after creating the repo, I was able to clone it to my machine via:

```bash 
 git clone https://github.com/username/username.github.io
```


# Setting up Structure
For this blog I use a Jekyll-based [minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/) theme. There are [many ways](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)
how you can set it up, I personally used the "start from 'jekyll new'" way described in their quickstart guide. Anyway, your effort should result in a dummy personal blog located in your repository folder. 

# Test Launch 
From your repository directory, you will need to execute
```bash 
 bundle install
```
to tell Ruby to install all the necessary Jekyll plugins. They are listed in the Gemfile located in the root of your site repo. One it's done, you can launch your site via
```bash
 bundle exec jekyll serve
```
At the end of its output, it will tell you the address and the port where your blog locates. For me, it was "http://127.0.0.1:4000". Open this link in your browser, and you'll see your new personal blog! It probably does not tell anything about you yet, so you may start filling it in by editing the following files:

1. `_config.yml` -- general description of your site. It also contains your name, email, the site's title, links to your twitter/github/instagram etc. 
2. `_data/navigation.yml` -- navigation structure of your site. You can add more subsections like "Teaching", or "Publications".
3. `_posts/` -- your posts live here. There might be several Jekyll-created posts there, so you'll need to delete them and to replace them with your real posts. 

# Troubleshooting 
## Github Error
If you see something like this:
```
GitHub Metadata: No GitHub API authentication could be found. Some fields may be missing or have incorrect data.
```
when launching `jekyll` locally, check out [this](https://idratherbewriting.com/documentation-theme-jekyll/mydoc_install_jekyll_on_mac.html#githuberror) instruction. 

# Additional Tweaks
## Latex
If you are going to write about math you'll need to add the $\LaTeX$ support to your site. It's a bit tricky, but [this](https://www.janmeppe.com/blog/How-to-add-mathjax-to-minimal-mistakes/) instruction worked just fine for me:

$$
    e^{i\pi} = -1
$$