---
title: Data Science on Chrome OS + Linux
categories: [Linux, GitHub, R, python, reference]
excerpt: "Instructions for how to enable Linux on a Chromebook and begin running a data science toolkit on Debian, including R, Python, Jupyter, git, etc."
toc: true
toc_label: "Content"
toc_sticky: true
toc_icon: 'cogs'
---

## Data Science on Chrome OS + Linux

![DS_Pixelbook](/assets/images/pixelbook_ds.jpg){: .align-center}

ChromeOS is a linux-based operating system developed by Google and is the default OS on all Chromebooks. It's a very lightweight OS and was initially limited Chrome and the suite of Google office applications. However, overtime it has grown to support Android Apps as well as Linux applications. This post documents the steps I took to set up a linux-based Data Science tool kit on my Pixelbook, including git, Python (Anaconda distribution), and R (and R Studio).

Note that I am working with the Debian (10) distribution of Linux.


## 1. Enabling and Setting Up Linux

 1. Set up Linux on your Chromebook following the instructions on [Google Support]([https://support.google.com/chromebook/answer/9145439?hl=en](https://support.google.com/chromebook/answer/9145439?hl=en)). Note that Debian is the default distribution.
 2. Set password for Linux:
  - Switch to root with `$ sudo su`
  - Run `passwd your-username-here`
  - It will prompt you twice for a new password. To exit, type exit`
 3. Update your Linux distro and packages from the terminal:
  `sudo apt-get update && sudo apt-get dist-upgrade`




## 2. Required and Suggested Linux Software, Libraries


**Install CL Utilities**

    - `sudo apt install nano` _Command line text editor_
    - `sudo apt install gdebi` _deb package installer_
    - `sudo apt install tree` _Utility for recursive directory listing_
    - `sudo pip install csvkit` _Utility for working with CSVs on the command line_



**Install Atom** - Text editor with embedded git control
  1. Download latest .deb file from [https://atom.io/](https://atom.io/)
  2. Navigate to wherever the .deb file is saved and run `sudo gdebi <deb_file_name>`


**Install Debian Backports**

Debian is a very stable distribution of Linux and as a feature doesn't stay as up to date as others automatically. For example, when I first installed R without backports, the most up to date version I could install was 1.5 years old. To get around this, you can install Debian backports. Explainer article [here](https://linuxconfig.org/how-to-install-and-use-debian-backports) and [here](https://chromeunboxed.com/add-debian-buster-backports-chromebook-chrome-os-updated-packages/)

Instructions [here](https://www.linuxuprising.com/2019/12/how-to-install-latest-firefox-non-esr.html)

- Open `/etc/apt/sources.list` as root with a text editor:
`sudo nano /etc/apt/sources.list`
- At the end of the file add the following line
`deb http://deb.debian.org/debian/ unstable main contrib non-free`

**Debian Backports -- OPTIONAL** - Set a low pin priority for the Debian Unstable repository so your system doesn't automatically install packages from it unless you manually specify this.
  - Create and open a file **/etc/apt/preferences.d/99pin-unstable** as root with a text editor, for example using nano command line text editor:
  - `sudo nano /etc/apt/preferences.d/99pin-unstable`
  - Paste the following in this file:

            `Package: *
      	    Pin: release a=stable
      	    Pin-Priority: 900

      	    Package: *
      	    Pin release a=unstable
      	    Pin-Priority: 10`






**Installing Linux libraries**

  In attempting to install certain packages on R, I was receiving error messaging pointing to missing libraries in my Linux install. For example, 'rvest' wouldn't install initially because 'libcurl' was not installed by default and [needed to be](https://community.rstudio.com/t/packages-installation-process-failed-on-linux-probably-due-to-missing-path-in-the-pkg-config-search-path/50619).

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




## 3. Setting up git

Instructions below are referred to the in [guide](https://kbroman.org/github_tutorial/pages/first_time.html) guide written by Karl Broman.

**Set up username and email:**

  `$ git config --global user.name "John Doe"`

  `$ git config --global user.email johndoe@example.com`

Setting up Atom as the default editor. Code for others linked [here](https://help.github.com/en/github/using-git/associating-text-editors-with-git)
`$ git config --global core.editor "atom --wait"`

Enable colored output in the terminal:
`$ git config --global color.ui true`

Generate a SSH key using your email address associated with GitHub:
- `$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
- Press Enter to save the key to the default location (/Users/you/.ssh/id_rsa)
- Type a secure passphrase twice

Add the SSH key to the ssh-agent [link](https://gist.github.com/JoaquimLey/31deaf857521319fcba8ee9f7af47299)
- `$ eval "$(ssh-agent -s)"`
- `$ ssh-add ~/.ssh/id_rsa`

Add the new SSH key to your GitHub Account [link](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)
- Copy the SSH key to your clipboard `cat ~/.ssh/id_rsa`
- Add SSH Key to your GitHub by accessing Settings > SSH Keys and GPG Keys > New SSH Key/Add SSH Key


**Creating a new repository from a local working directory:** [reference](https://help.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line)

- Create a new repository on GitHub.com
- Initialize git inside your working directory `git init`
- Add the files and commit `git add`, `git commit -m <message_here>`
- Add the URL for the remote repository where your local repository will be pushed and verify
  - `$ git remote add origin <remote repository URL>`
  - `$ git remote -v`
- Push the changes: `$ git push -u origin master`



**Adding a remote repository** [reference](https://help.github.com/en/github/using-git/adding-a-remote)
- `$ git remote add origin https://github.com/user/repo.git`
- If the remote URL needs to be reset use `$ git remote set-url origin https://github.com/USERNAME/REPOSITORY.git`


**Cloning existing repositories:**

- Navigate to the repository you'd like to clone on GitHub
- Click "Clone or Download" then choose "Clone with HTTPS" and click the clipboard icon to copy
- Within the terminal, navigate to whichever to the location where you want the cloned directory to be made
- Type `git clone` and paste the copied HTTPS URL
- Press 'Enter'






## 4. Installing Python via Anaconda


The following are the necessary steps to install Python via Anaconda via with Spyder, Jupyter Notebooks, and Jupyer Labs included. Following guide posted [here](https://randlow.github.io/posts/python/set-up-pixelbook-python/#install-nodejs):


**Required Files**

 - Install _nodejs_ which is required for _tldr_ and Jupyter Lab:

	 ` $ curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -`

	  `$ sudo apt-get install -y nodejs`

	  `$ sudo apt-get install -y build-essential`

 - Install _tldr_ a more user-friendly version of `man`, short and useful help page for commands:

	  `$ sudo npm install -g tldr`

	  `$ tldr --update`

	  `$ tldr conda`


**Install Anaconda and add Anaconda3 distribution to the Linux PATH**

1. Download Anaconda from the [official website](https://www.anaconda.com/download/#linux) and install the .sh file using bash. Ensure that you are in your home directory before doing so (i.e., /home/USERNAME)
`$ bash Anaconda3-x.x.x-Linux-x86_64.sh`

2. Adding Anaconda3 to the PATH using vim:

  - Open the .bashrc file: `vim ~/.bashrc`

  - Add the following line `export PATH="/home/<USERNAME>/anaconda3/bin/:$PATH"`

  - Rerun the .bashrc file `source ~/.bashrc`

  - Activate the Python environment to check that you have successfully added Anaconda Python executable files into your Linux PATH. `source activate root`


**Setting up Jupyter Notebooks, Jupyter Lab, Spyder, & Anaconda Navigator**

  Follow steps in the same [blog](https://randlow.github.io/posts/python/set-up-pixelbook-python/#id12) referred to above:

  - Obtain the IP address: `ip addr`

  - Analyze output and copy the second IP address refered to in the 'Broadcast section'. Something like **100.115.92.205**

  - Generate a _jupyter_notebook_config.py_ in the directory _/home/< username >/.jupyter/._ (create directory, if needed):

      `jupyter notebook --generate-config`

  - Open the config file and update the following lines under 'NotebookApp (Jupyter App Configuration):

	  `#c.NotebookApp.allow_origin ='*' #allow all origins`

	  `#c.NotebookApp.ip = '100.115.92.205' # listen to specific IP address`

   - Test to ensure everything was installed correctly:
   `jupyter notebook`
   `jupyter lab`
   `spyder`
   `anaconda-navigator`


**Extra Python Libraries Installed**

_Web Scraping_
- 'BeautifulSoup' `conda install -c anaconda beautifulsoup4`
- 'Selenium' `conda install -c anaconda selenium`

_Visualizations_
- 'Plotly/Orca' `conda install -c plotly plotly-orca`
- 'Cufflinks' `conda install -c conda-forge cufflinks-py`
- 'pydot' `conda install pydot`

_NLP_
- 'spacy' - `conda install -c conda-forge spacy`


**Follow up steps for Selenium**
- Install a browser of your choice on the Debian partition, I installed Chrome using instructions [here](https://linuxize.com/post/how-to-install-google-chrome-web-browser-on-debian-9/)
- Download [ChromeDriver - Webdriver](https://chromedriver.chromium.org/downloads)
- Copy the 'chromedriver' file to '/usr/local/bin' using `sudo cp chromedriver /usr/local/bin`
- Run `sudo chmod ugo+x /usr/local/bin/chromedriver`

Alternatively, I could have installed Firefox and the [required Libraries/Packages](https://www.mozilla.org/en-US/firefox/74.0.1/system-requirements/)






## 5. R and R Studio


**Installing current version of R on Debian**

- Ensure Debian Unstable Repository is added to the sources.list
- Add the CRAN repository to the list in sources.list Cran [link](https://cran.r-project.org/bin/linux/debian/) here.
    - `sudo nano /etc/apt/sources.list`
    - `deb http://cloud.r-project.org/bin/linux/debian buster-cran35/`
- Following guide from [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-install-r-on-debian-9)
    - `sudo apt install dirmngr --install-recommends`
    - `sudo apt install software-properties-common`
    - `sudo apt install apt-transport-https`
- Fetch and import the current key from [Cran](https://cran.r-project.org/bin/linux/debian/)
    - `apt-key adv --keyserver keys.gnupg.net --recv-key 'E19F5F87128899B192B1A2C2AD5F960A256A04AF'`
- Install up to date version of R
    - `apt-get update`
    - `apt-get install r-base r-base-dev`

- Download R Studio for [Ubuntu/Debian](https://rstudio.com/products/rstudio/download/#download)

- Install R Studio `sudo gdebi <rstudio deb file>`


**Incorporating R into Anaconda/Jupyter Notebooks**

Adding R to Jupyter notebooks installed via Anaconda is tricky. After a few failed attempts, I found the following worked ([stackoverflow link](https://stackoverflow.com/questions/44056164/jupyter-client-has-to-be-installed-but-jupyter-kernelspec-version-exited-wit/47895042)):

  - Open R from the **command line**

  - Install [IRKernel](https://irkernel.github.io/installation/) as well as the following dependencies: 'repr', 'IRdisplay', 'crayon', 'pbdZMQ', 'devtools'

  - Following [official instructions](https://irkernel.github.io/installation/), install 'IRkernel' and then make the kernel available to Jupyter by running the following:
    `IRkernel::installspec()` (for current user)
    `IRkernel::installspec(user = FALSE)` (for all users)


**Installing Packages in R:**

  **Important note**: Install packages from the command line in base R. Also, use `verbose=FALSE` when using install.packages()  

  Among the packages [recommended by RStudio](https://support.rstudio.com/hc/en-us/articles/201057987-Quick-list-of-useful-R-packages), I installed the following:

      `install.packages("rvest","tidyverse","shiny","dtwclust","caret","caretEnsemble","RCurl","RANN","WDI","leaflet","sf","fields","RODBC","DBI","Hmisc","zoo","devtools","jsonlite","rmarkdown","randomForest","multcomp","kableExtra","data.table","reshape","tm","plotly","forecast","tseries", "geojsonio", "tmap","corrplot","nnet" "ggmap","mapdata","mapview","rgdal","spData","fpc","tidyquant")`

  (_Ongoing issues with "xlsx" and Java_)


**R Appendix**

Links used in Troubleshooting IRKernel:
  - R Installation can break [following a Conda R install](https://github.com/ContinuumIO/anaconda-issues/issues/11108)
  - [“jupyter kernelspec --version” exited with code 127](https://stackoverflow.com/questions/44056164/jupyter-client-has-to-be-installed-but-jupyter-kernelspec-version-exited-wit)
  - [R in Jupyter under Linux](https://community.rstudio.com/t/r-in-jupyter-under-linux/18594)
  - Official Documentation [link](https://docs.anaconda.com/anaconda/user-guide/tasks/using-r-language/
  - Datacamp [link](https://www.datacamp.com/community/blog/jupyter-notebook-r#comments)
  - https://github.com/ContinuumIO/anaconda-issues/issues/11108
  - https://www.storybench.org/install-r-jupyter-notebook/





## Appendix

Listed below are some additional utilities and short-cuts for working with Linux

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


**ZSH/BASH**

Temporarily switch between zsh and bash within the same terminal by executing `exec bash` or `exec zsh`.

To source your profile at the same time, use `exec bash --login`

Set either to be the default by executing `chsh -s /bin/bash` or `chsh -s /bin/zsh`


**Aliases**

Aliases are declared inside `~/.bashrc` and can be added using any text editor (e.g. `nano ~/.bashrc`)

After adding aliases to the .bashrc, the aliases can be made availabe in the current session by typing `source ~/.bash_profile`

    `# Juptyter Notebook
    alias jn="jupyter notebook"`
