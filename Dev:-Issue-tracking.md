# Bugs
## Entering bugs:
* Add title
* Add labels 'Bug' and 'New' and priority - 'P1' or 'P2' or 'P3' (see priority definitions below)
* Assign to appropriate person if known, otherwise, leave unassigned
* Select appropriate milestone if known, otherwise, leave unselected
* Add the issue, appropriate repro steps, screen shots, etc., and expected outcome 
 
### Priority Definitions:
1. Ship Blocker - Must be fixed before shipping 
2. Should be fixed before shipping, painful to cut
3. Nice to have

## Triaging Bugs:
* Remove label 'New'
* Add or adjust Priority label, as appropriate
* Assign to the appropriate person
* Select the appropriate milestone

## Closing Bugs:
* Add appropriate comments
* Close bug

# Searching bugs

GitHub provides quite a few options to search issues. Options are explained in [this article](https://help.github.com/articles/searching-issues). For example, if you wanted to find all opened bug with “About” in the title, then use the search string:

    about in:title state:open

To further restrict the results to P2 bugs:

    about in:title state:open label:Bug label:P2