# Bugs
This page describes how to file and track issues for the CodaLab project. All bugs are tracked as [issues](https://github.com/codalab/codalab/issues?state=open) in the CodaLab GitHub repository. 

## Entering bugs:
* Add a meaningful title.
* Add the 'Bug' and 'New' labels, to identify the issue as a bug.
* Assign a priority - 'P1' or 'P2' or 'P3' (see priority definitions below).
* Mark the but as either 'Worksheets' or 'Competitions'.
* Assign the issue to the appropriate person if known, otherwise leave it unassigned.
* Select the appropriate milestone if known, otherwise leave it unselected.
* Use the Comment field to describe the issue. Be sure to include the appropriate repro steps, screen shots, and so forth, as well as the expected outcome. 
 
### Priority Definitions:
1. Ship Blocker - Must be fixed before shipping 
2. Should be fixed before shipping, painful to cut
3. Nice to have

## Triaging Bugs:
* Remove the 'New' label.
* Add or adjust Priority label, as appropriate.
* Assign the issue to the appropriate person.
* Select the appropriate milestone.

## Closing Bugs:
* Add the appropriate comments.
* Close the issue.

# Searching bugs

GitHub provides quite a few options to search issues. Options are explained in [this article](https://help.github.com/articles/searching-issues). For example, if you wanted to find all opened bug with “About” in the title, then use the search string:

    about in:title state:open

To further restrict the results to P2 bugs:

    about in:title state:open label:Bug label:P2