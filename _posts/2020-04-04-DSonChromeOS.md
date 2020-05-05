---
title: Data Science on Chrome OS
date: 2020-04-04
header:
  image: "/assets/images/pixelbook_ds.jpg"
excerpt: "Guide for doing Data Science on ChromeOS"
---




## Setting up Linux on Chromebook for Data Science

The steps below are ones I took to set up my Pixelbook's version of Debian 10 so that I could get common Data Science tools running, including R (and R Studio), Python (Anaconda distribution), and Jupyter notebooks.

****

### Linux System

**Initial Steps**
 1. Set up Linux on your Chromebook following the instructions on [Google Support]([https://support.google.com/chromebook/answer/9145439?hl=en](https://support.google.com/chromebook/answer/9145439?hl=en)). Note that Debian is installed by default.
 2. Set password for Linux:
  - Switch to root with `$ sudo su`
  - Run `passwd your-username-here`
  - It will prompt you twice for a new password. To exit, type exit`
 3. Update your Linux distro and packages from the terminal:
  `sudo apt-get update && sudo apt-get dist-upgrade`

****

**Install CL Utilities**

    - `sudo apt install nano`
    - `sudo apt install gdebi`
    - `sudo apt install tree`
    - `sudo apt install mawk`
    - `sudo pip install csvkit`

****

**Install Atom**
  1. Download latest .deb file from [https://atom.io/](https://atom.io/)
  2. Navigate to wherever the .deb file is saved and run `sudo gdebi <deb_file_name>`


****

**Install Debian Backports**

Debian is a very stable distribution of Linux and as a feature doesn't stay as up to date as others automatically. For example, when I first installed R without backports, the most up to date version I could install was 1.5 years old. To get around this, you can install Debian backports. Explainer article [here](https://linuxconfig.org/how-to-install-and-use-debian-backports) and [here](https://chromeunboxed.com/add-debian-buster-backports-chromebook-chrome-os-updated-packages/)


**Adding the Debian Unstable Repository**
_necessary for installing more up to date software (e.g. Firefox)_
_instructsions here [https://www.linuxuprising.com/2019/12/how-to-install-latest-firefox-non-esr.html](https://www.linuxuprising.com/2019/12/how-to-install-latest-firefox-non-esr.html)_

- Open `/etc/apt/sources.list` as root with a text editor:
`sudo nano /etc/apt/sources.list`
- At the end of the file add the following line
`deb http://deb.debian.org/debian/ unstable main contrib non-free`

****

**Installing Linux libraries**

  In attempting to install certain pacakges on R, I was receiving error messaging pointing to missing libraries in my Linux install. For example, 'rvest' wouldn't install initially because 'libcurl' was not installed by default and [needed to be](https://community.rstudio.com/t/packages-installation-process-failed-on-linux-probably-due-to-missing-path-in-the-pkg-config-search-path/50619).

  Note that some instructions will ask that you [add-apt-repository](https://tecadmin.net/add-apt-repository-ubuntu/) before installing the software itself

  In total, I found myself needing to install the following to support installation of all the R packages I wanted to use (many pertained to mapping):


Required for **devtools**:

      -   `sudo apt install libcurl4-openssl-dev`

      -   `sudo apt install libssl-dev`

      -   `sudo apt install libxml2-dev`

Required for **sf**:

      -   `sudo apt install libudunits2-dev`

      -   `sudo apt install gdal-bin`

      -   `sudo apt install libgdal-dev`

Required for **geojsonio**:

      -   `sudo apt install libprotobuf-dev protobuf-compiler`

      -   `sudo apt install libv8-dev`

      -   `sudo apt install libjq-dev`

Required for **mapview**:

      -   `sudo apt install libfontconfig1-dev`

      -   `sudo apt install libcairo2-dev`


I also needed to [install Java](https://www.r-bloggers.com/installing-rjava-on-ubuntu/) for **xlsx**:

      -  `sudo apt-get install -y default-jre`
      -  `sudo apt-get install -y default-jdk`
      -  `sudo R CMD javareconf`


****

**ZSH**

Set up [ohmyzsh](https://github.com/ohmyzsh/ohmyzsh) to manage the zsh configuration. Getting started guide [here](https://www.sitepoint.com/zsh-tips-tricks/)

Install ZSH:

  - `sudo apt update`
  - `sudo apt upgrade`
  - `sudo apt install zsh`
  - Verify installation: `zsh --version`
  - Set zsh as the default shell `chsh -s $(which zsh)`
  - Log out and log back in of shell, at which point you'll be prompted with a few options for configuration. I've chosen **2** with the recommended configuration

Install Oh My ZSH! [official link](https://ohmyz.sh/)
  - `sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"` or `sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"`

Customization

    - Open congif file: `sudo nano ~/.zshrc`

    - Note that default theme is set to 'robbyrussel'. This can be changed to any of the pre-installed themes or a [custom themes](https://github.com/ohmyzsh/ohmyzsh/wiki/Customization)

    - Custom themes must be installed in the themes folder `~/.oh-my-zsh`

****

**ZSH/BASH**

Temporarily switch between zsh and bash within the same terminal by executing `exec bash` or `exec zsh`.

To source your profile at the same time, use `exec bash --login`

Set either to be the default by executing `chsh -s /bin/bash` or `chsh -s /bin/zsh`

****

**Aliases**

Aliases are declared inside `~/.bashrc` and can be added using any text editor (e.g. `nano ~/.bashrc`)

After adding aliases to the .bashrc, the aliases can be made availabe in the current session by typing `source ~/.bash_profile`

    `# Juptyter Notebook
    alias jn="jupyter notebook"`


****
**Appendix**

**OPTIONAL** - Set a low pin priority for the Debian Unstable repository so your system doesn't automatically install packages from it unless you manually specify this.
  - Create and open a file **/etc/apt/preferences.d/99pin-unstable** as root with a text editor, for example using Nano command line text editor:
  - `sudo nano /etc/apt/preferences.d/99pin-unstable`
  - Paste the following in this file:

            `Package: *
      	    Pin: release a=stable
      	    Pin-Priority: 900

      	    Package: *
      	    Pin release a=unstable
      	    Pin-Priority: 10`
