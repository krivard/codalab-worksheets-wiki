[![CodaLab.org](http://willum070.github.io/docteam/images/codalab-logo-dinky-pos.png)](https://www.codalab.org)

Welcome to the CodaLab Wiki, the home of CodaLab documentation.

*What can you do with CodaLab?*

* **Join a competition.** Visit the [CodaLab.org competitions page](https://www.codalab.org/competitions) to see a list of current competitions. [Find out how to join a competition](User_Participating-in-a-Competition).
* **Host a competition.** The [Competition Roadmap](User_Competition-Roadmap) shows you everything you need to know to host a CodaLab competition.
* **Collaborate with worksheets.** 
* **Contribute to the project.** See [How to Contribute](Dev_How-to-Contribute) to find out how to report bugs, submit feature requests, and contribute your skills as a developer.

# CONTENTS
## General
* [CodaLab FAQ](Project_CodaLab_FAQ)

## Competitions
* [Competition Roadmap](User_Competition-Roadmap)
* [Building a Competition Bundle](User_Building-a-Competition-Bundle)
* [Building a Scoring Program for a Competition](User_Building-a-Scoring-Program-for-a-Competition)
* [Running a Competition](User_Running-a-Competition)
* [Participating in a Competition](User_Participating-in-a-Competition)
* [Groups and Permissions Discussion](Dev_Groups-and-permissions-discussion)

## Worksheets
* [Using the CodaLab Bundle Service and CLI (Linux)](User_Using-the-CodaLab-CLI)
* [Using the CodaLab Bundle Service and CLI (Windows)](User_Using-the-CodaLab-CLI-Windows)
* [Displaying Bundle Contents and Metadata](Dev_Displaying-Bundle-Contents-and-Metadata)
* [CodaLab CLI GitHub repository](https://github.com/codalab/codalab-cli)

## Developers
* [How to Contribute](Dev_How-to-Contribute)
* **Getting Started**
    * [UNIX](Dev_Getting-Started-on-UNIX-based-Systems)
    * [Windows](Dev_Getting-Started-on-Windows)
    * [Mac](Dev_Getting-Started-on-Mac)
* [Configure CodaLab for Development](Dev_Configure-Codalab-For-Development)
* [Set up data storage](Dev_Set-up-data-storage)
* [Configure CodaLab to work with Visual Studio](Dev_Configure-CodaLab-Visual-Studio)
* **Process**
    * [Developer Guidelines](Dev_Developer-Guidelines)
    * [Process for Tracking Issues](Dev_Issue-tracking)
    * [Process for Stories and Tasks](Dev_Scenarios,-Stories-and-Tasks)
    * Continuous integration
    * [Dev: Deploying CodaLab](Dev_Deploying-CodaLab)
* **Design and implementation notes**
    * [Design overview](https://github.com/codalab/codalab/blob/master/docs/SPECIFICATION.md)
    * [Bundles](https://github.com/codalab/codalab/blob/master/bundles/BUNDLES.md) and [local execution prototype](https://github.com/codalab/codalab/tree/master/bundles)
    * [E-mail notification](E-mail-notifications)
    * [Progressive registration](User_Progressive-Registration)

## Editing Guidelines
If you want to edit the wiki, you can clone it and work on it locally:

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