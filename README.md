Branch for hosting the [webpage for TeMoto 2](https://utnuclearroboticspublic.github.io/temoto2)

The source files are stored in "src/" and the webpage is built into the "site/", meaning that this branch is not using github's webpage generator (because it has a limited list of supported plugins).

## Setup

**1) Clone the "gh-pages" branch:**

```
git clone -b gh-pages https://github.com/UTNuclearRoboticsPublic/temoto2.git
```
Or if you already have temoto2, then:
```
roscd temoto2
git fetch origin gh-pages
git checkout gh-pages
```
Although if you are developing Temoto and you want to modify the gh-pages, then stashing uncommited changes and checking out to appropriate branch might be inconvenient. In this case the easiest thing to do is to clone temoto gh-pages branch to a separate place.

**2) Set up ruby, bundler, jekyll locally:**

Follow the steps 1-4 in the [Requirements section of the jekyll setup tutorial](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#requirements)

If you encounter some permission issues while installing bundler gem, then I didn't find a better solution from reinstalling ruby (other "sudo this and chmod that" solutions seemed sketchy). [Follow this guide](https://gepolv.github.io/ubuntu/ruby/bundler/2016/06/21/gem-install-bundle-issue-on-ubuntu.html) and things should go smoothly.

**3) Install Jekyll and other dependencies from the GitHub Pages gem:**
```
cd temoto2/src
bundle install
```

**4) Build the website:**

Each time you modify the content, the website has to be built. Go to the temoto2 (gh-pages branch) root folder and run:
```
jekyll build --source ./src --destination ./site
```
You can also make jekyll regenerate the site each time the content is changed by adding the "--watch" flag:
```
jekyll build --source ./src --destination ./site --watch
```

**5) Run the Jekyll site locally:**

```
cd temoto2/src
bundle exec jekyll serve
```

## How to add/modify web content

As previously mentioned, the source content is in the "*temoto2/src*" directory. This directory contains bunch of files and folders.

**1) Folders:**

* ***_layouts***: Contains the layout files that, well, manages the layout of the webpage
* ***_plugins***: Contains custom plugins. At the moment there is a plugin that hels to generate navigation pane links from H2 headings
* ***_site***: For who knows what reason jekyll genrates the site also into this folder
* ***doc***: Contains supplementary materials like images
* ***layers, managers*** Folders that were created for organizing purposes. These contain content related sub-pages

**2) Files:**
* ***_config.yml, Gemfile, Gemfile.lock***: The holy things for jekyll. Mostly contain information about site generator dependencies
* ***index.md***: Homepage
* ***p_about.md, ... , p_tutorials.md***: These are the pages that will appear in the navigation pane at all times

So in order to add/modify content, you can edit the "*p_ .md*" pages, create new ones or create new pages into specific page groups just like *layers/managers*. If you **do not want** some specific page group (<YOUR_PAGE_GROUP>) to appear in the navigation pane, then apply the following modification in the "*_layouts/default.html*":
```
<!-- Insert all pages to the nav bar -->
  {% for page_s in site.pages %}
    {% unless page_s.url contains 'managers' or page_s.url contains 'layers' or page_s.url contains <YOUR_PAGE_GROUP> %}
```
