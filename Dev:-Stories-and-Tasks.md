# Tasks
## Entering tasks:
* Add title
* Add label 'Task'
* Assign to appropriate person
* Select appropriate milestone
* Add task definition

## Closing Tasks:
* Assign to 'MedicalTest' to verify
* Add appropriate comments
* Close Task

## Verifying Tasks
When a task is verified, the label 'Verified' will be added. This allows anyone to filter through the closed tasks and understand which have been tested.

# Searching bugs and tasks

GitHub provides quite a few options to search issues. Options are explained in [this article](https://help.github.com/articles/searching-issues). For example, if you wanted to find all opened bugs with “About” in the title, then use the search string:

    about in:title state:open

To further restrict the results to P2 bugs:

    about in:title state:open label:Bug label:P2