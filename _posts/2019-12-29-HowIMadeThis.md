---
title: How I Made This
categories: [Jekyll, GitHub]
date: 2019-12-29
excerpt: "Using Jekyll to create a portfolio from my GitHub repository"
---


![Jekyll+GitHub](/assets/images/github_jekyll.jpg)

This portfolio was built using Jekyll, a static website generator that is a popular choice among users who wish to build a personal or portfolio website. I chose it because it is simple to set up if you have little to no experience in web development and has a number of attractive themes. Also, the website you create can be hosted on GitHub.


**Setting up the Blog**

1. Install Ruby: `sudo apt-get install ruby-full build-essential`

2. Install Jekyll and bundler gems `gem install jekyll bundler`

3. Followed this [YouTube tutorial](https://www.youtube.com/watch?v=qWrcgHwSG8M&t=55s) in which the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) custom repository is forked and used as a basis for building a simple website.

	  _Variations from tutorial_ :

	  - Once forking the repository, I deleted all the files recommended for removal on the official Minimal Mistakes [guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#remove-the-unnecessary)

	  - Update the Gemfile using the [remote theme method](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#remote-theme-method) so that the blog will render on GitHub

	  - Finally, in the tutorial you're instructed to copy this [code](https://github.com/dataoptimal/github-pages-tutorial/blob/master/posts_code.txt) into the machinelearning.MD. I found that this code threw an error for me before I deleted the first line ({% include base_path %}). This meant that the top-line navigation button for 'MachineLearning' page would not work, but at least the website will properly generate

  4. The website should be live. I used the [atom](https://atom.io/) text editor to edit and manage my posts. I also found this [series of tutorial videos](https://www.mikedane.com/static-site-generators/jekyll/) from Mike Dane very useful for understanding Jekyll basics
