---
title: How I built this portfolio
categories: [Jekyll, GitHub, reference]
excerpt: "This portfolio website was surprisingly easy to build, even without much knowledge of CSS or HTML! This post includes the steps I took and tutorials I leveraged to get Jekyll and minimal mistakes running."
---

# How I made this

![Jekyll+GitHub](/assets/images/github_jekyll.jpg){: .align-center}

This portfolio was built using Jekyll, a static website generator that is a popular choice among users who wish to build a personal or portfolio website. I chose it because it is simple to set up if you have little to no experience in web development and has a number of attractive themes. Also, the website you create can be hosted on GitHub.


**Setting up the Blog**

1. Install Ruby: `sudo apt-get install ruby-full build-essential`

2. Install Jekyll and bundler gems `gem install jekyll bundler`

3. Followed this [YouTube tutorial](https://www.youtube.com/watch?v=qWrcgHwSG8M&t=55s) in which the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) custom repository is forked and used as a basis for building a simple website.

	  _Variations from tutorial_ :

	  - Once forking the repository, I deleted all the files recommended for removal on the official Minimal Mistakes [guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#remove-the-unnecessary)

	  - Update the Gemfile using the [remote theme method](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#remote-theme-method) so that the blog will render on GitHub

	  - Finally, in the tutorial you're instructed to copy this [code](https://github.com/dataoptimal/github-pages-tutorial/blob/master/posts_code.txt) into the machinelearning.MD. I found that this code threw an error for me before I deleted the first line which has text **include base_path**. Deleting this line allowed for the site to render, although the top-line navigation button for 'MachineLearning' page would not work.

  4. The website should be live. I used the [atom](https://atom.io/) text editor to edit and manage my posts. I also found this [series of tutorial videos](https://www.mikedane.com/static-site-generators/jekyll/) from Mike Dane very useful for understanding Jekyll basics


  **Integrating R Markdown documents into Jekyll Posts**

  The process of turning your RMD code into Jekyll posts is pretty straightforward.

  - Within your RMD file, replace the output format in the YAML to read what's copied below and reknit the RMD.

  `
  output:
    md_document:
      variant: markdown_github
  `

  - The resulting MD file generated can be posted by Jekyll can post once adding an appropriate YAML header that Jekyll recognizes. Something like the one for this page:

  ````
  ---
  title: How I Made This
  categories: [Jekyll, GitHub]
  date: 2019-12-29
  excerpt: "Using Jekyll to create a portfolio from my GitHub repository"
  ---
  ````

  - Any charts or graphs generated by your RMD will have to be copied over to your Jekyll site file directory and linked within the MD you generated.

  **Converting ipynb into Jekyll Posts**

.ipynb files can be converted to .MD files using **nbconvert** which is package inside anaconda. To execute, from the command line simply type `jupyter nbconvert --to markdown FILENAME.ipynb`



**Editing Appearance of Minimal Mistakes**

While the official Minimal Mistakes [Quick-Start Guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/) is a great place to start editing the appearance of the blog, I found Katerina Bosko's post at [Cross-Validated.com](https://www.cross-validated.com/Personal-website-with-Minimal-Mistakes-Jekyll-Theme-HOWTO-Part-II/) invaluable.

Finally, the official Jekyll message board, [Jekyll Talk](https://talk.jekyllrb.com/) was also helpful in providing answers to commonly answered questions relating to Jekyll and Minimal Mistakes.
