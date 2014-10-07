# About CodaLab Documentation
This topic explains how CodaLab documentation is set up, and shows you how to work with the documentation. CodaLab documentation is presented from two resources:
- The CodaLab wiki, which you are now reading, contains the main CodaLab documentation. When accessed from codalab.org, wiki pages open in a new window.
- CodaLab GitHub IO pages contain a subset of documentation and other content which is more closely integrated with the site. This includes the About Page, the CodaLab forum, and competition overview topics.

    **Note:** The main page of the CodaLab GitHub IO Pages (http://codalab.github.io/codalab) contains a redirect to codalab.org.

No documentation is stored with the main site, relieving contributors of the need to re-deploy whenever documentation changes are made.

## Editing the Wiki
You must be a project contributor to make changes to the CodaLab wiki. See [How to Contribute](https://github.com/codalab/codalab/wiki/Dev_How-to-Contribute) for the steps you will need to take.

Once you have been granted access, you can clone the wiki and work on it locally:

    git clone https://github.com/codalab/codalab.wiki.git

The CodaLab project welcomes your wiki contributions! The wiki has its own repository, separate from the CodaLab repo. Forking is not necessary, and you will not need to make pull requests for your changes. In light of this, please take the time to ensure that any documentation updates you contribute are technically correct. If you have made significant additions or changes please ask community members to review your work by posting a request on the [CodaLab Users Group](http://codalab.github.io/codalab/forum.html) and/or the [CodaLab Dev](https://groups.google.com/forum/#!forum/codalabdev) forum. 

### Naming Conventions
In order to keep wiki content coherent we have adopted the following page naming convention to make it easy to find things. We are using pseudo-namespaces to organize pages. This is accomplished by using a prefix plus underscore for the page names, such as "Dev_configuring your setup".

This is the current subject area breakdown to make things consistent:

* **Project** - General project documentation
* **User** - Competition organizer/participant documentation
* **UX** - User Experience/interaction design documentation
* **Dev** - Developer documentation
* **Test** - Test documentation
* **Release** -  Release documentation
* **Trash** - Things we no longer need but don't want to discard yet

While codes are convenient for quick classification, it is better if pages are found by naturally following the above content guide.

## Working with CodaLab GitHub IO Pages
GitHub repositories contain a special branch named gh-pages, which stores the files for the repo's [GitHub Pages](https://pages.github.com/) website. You can see CodaLab's gh-pages branch [here](https://github.com/codalab/codalab/tree/gh-pages).

To work with the CodaLab GitHub Pages, follow these steps:

1. If you have not already done so, [clone the CodaLab repository](https://github.com/codalab/codalab/wiki/Dev_Configure-Codalab-For-Development#get-the-source-code).
1. Open a command prompt and navigate to the CodaLab folder.
1. Create a 'blessed' remote.
    
    a. Navigate to the newly cloned directory.

        cd codalab

    b. Assign the original repo to a remote called "blessed".

        git remote add blessed https://github.com/codalab/codalab.git

    c. Get the latest changes from blessed:

        git checkout master
        git pull blessed master

1. Create a new orphan branch named 'gh-pages':

    `git checkout --orphan gh-pages`

1. Remove all files from the old working tree:

    `git rm -rf .`
    `rm '.gitignore'`

1. Pull content from the gh-pages branch on the 'blessed' remote:

    `git pull blessed gh-pages`

    It is important to note that you are pulling from the blessed remote, NOT from your personal repository. Once you have done this, you should be able to make changes and push them to the gh-pages branch of your own repository. From there you can make pull requests.

You should be able to see and edit the files now. For more information about working with Git project pages, see [Creating Project Pages manually](https://help.github.com/articles/creating-project-pages-manually).

## Working with CodaLab Links
Although no help content is hosted on CodaLab.org, there may be times when you will need to edit the links that point to the content. This section shows you the locations of the files you will need to modify, and shows you how to make the changes. Note that modifying any CodaLab source code will require you to make a pull request.

### Main Page
The main page contains a number of links that may need editing at some point. You can find the main page at `\codalab\apps\web\templates\web\index.html`.

### Header and Footer Links
Markup for the header and footer links is defined in the base template `\codalab\apps\web\templates\base.html`. This is the base template for all pages on the site. 

The header contains a number of links including a link to the CodaLab wiki ("Help"). In the event that this URL needs to change, the markup for the link is located within the `<!-- Right Nav Section -->`:

```
<li id="liHelp"><a href="https://github.com/codalab/codalab/wiki" target="_blank">Help</a></li>
``` 

Note that because these pages are create using the Foundation framework, the links are defined as an unordered list.

To find the footer bar markup open `base.html` and grep for "footer". The following example shows how the footer markup appears.

```
<!-- Footer -->
<footer id="footer">
    <div class="small-6 columns">
        <ul class="inline-list left">            
        <li>   
            <span>
              <a href="http://research.microsoft.com/" target="_blank" target="_self"><img src="{{ STATIC_URL }}img/msr_footer.png" border="0"/></a>
            </span>
        </li>
        <li>   
            <span>
              <!-- <a href="{{last_commit}}">{{app_version}}</a> -->
            </span>
        </li>
        </ul>
    </div>
    <div class="small-6 columns">
        <ul class="inline-list right">
            <!-- <li>CodaLab v.0 ()</li> -->
            <li><a href="http://codalab.github.io/codalab/about.html" target="_self">About</a></li>
            <li><a href="http://codalab.github.io/codalab/forum.html" target="_self">Forum</a></li>
            <li><a href="http://go.microsoft.com/?linkid=9837806" target="_blank">Survey</a></li>
            <li><a href="http://codalab.github.io/codalab/notices.html" target="_self">Notices</a></li>
            <li><a href="http://codalab.github.io/codalab/terms.html" target="_self">Terms of Use</a></li>
        </ul>
    </div>
</footer>
```

