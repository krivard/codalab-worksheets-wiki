# About CodaLab Documentation
This topic explains how CodaLab documentation is set up, and shows you how to work with the documentation. CodaLab documentation is presented from two resources:
- The CodaLab wiki, which you are now reading, contains the main CodaLab documentation. When accessed from codalab.org, wiki pages open in a new window.
- [CodaLab GitHub IO pages](http://codalab.github.io/codalab) contain a subset of documentation which provides a tighter integration with the site.

No documentation is stored with the main site, relieving contributors of the need to re-deploy whenever documentation changes are made.

## Working with CodaLab GitHub IO Pages
To work with the CodaLab GitHub IO pages, follow these steps:

1. Open a command prompt and navigate to your CodaLab repository.
1. Create a new orphan branch named 'gh-pages':

    `git checkout --orphan gh-pages`

1. Remove all files from the old working tree:

    `git rm -rf .`
    `rm '.gitignore'`

1. Pull content from gh-pages branch:

    `git pull origin gh-pages`

You should be able to see and edit the files now. For more information about working with Git project pages, see [Creating Project Pages manually](https://help.github.com/articles/creating-project-pages-manually).

## Editing the Wiki
If you want to edit the CodaLab wiki, you can clone it and work on it locally:

    git clone https://github.com/codalab/codalab.wiki.git

Note that the wiki is its own repo, separate from the CodaLab repo. Forking is not necessary, and you will not need to make pull requests for your changes. In light of this, please take the time to ensure that any documentation updates you contribute are technically correct. The CodaLab project welcomes your contributions!

In order to keep this content coherent we have adopted the following page naming convention to make it easy to find things. We are using pseudo-namespaces to organize pages. This is accomplished by using a prefix plus underscore for the page names, such as "Dev_configuring your setup".

This is the current subject area breakdown to make things consistent:

* **Project** - Project Documentation
* **User** - Competition Organizer Documentation
* **UX** - User Experience / Interaction Design Documentation
* **Dev** - Developer Documentation
* **Test** - Test Documentation
* **Release** -  Release Documentation
* **Trash** - Things we no longer need but don't want to discard yet

While codes are convenient for quick classification, it is better if pages are found by naturally following the above content guide.