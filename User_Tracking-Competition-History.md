# Tracking Competition History
Competition history enables competition organizers to view all of the output files for competition participants, providing an audit trail of all successful submissions in the competition. This is accomplished by adding history functions to your evaluation code. First, a history folder is created:

```
input/history/<phase #>/<submission #>/
```
For example:

```
input/history/000/001/ # phase 0, submission 1
input/history/000/002/ # phase 0, submission 2
```

During the competition a running history of scores is kept:

```
input/history/000/001/output/scores.txt # submission #1 scores
input/history/000/002/output/scores.txt # submission #2 scores
```

Each `scores.txt` file shows whether the current submission is correct, along with an `audit_score` which shows the running percentage of correct answers. For example, say you have just made your third submission. Let's assume that the first two submissions were incorrect, but the third one is correct. The `scores.txt` file would look like this:

```
correct:1.0
audit_score:0.3
```

In this example, `scores.txt` indicates that the current entry is correct (`correct:1.0`), and  that one of three submissions is correct (`audit_score:0.3`).

Competition participants can access the history for entries by following these steps:

1. Open [My Codalab](http://www.codalab.org/my).
1. Click the **Competitions I'm In** tab, then select a competition to view.
1. Click the **Participate** tab for the competition.
1. Click **Submit/View Results**.
1. Expand the row of the submission for which you want to view history.
1. Click **Download evaluation output from scoring step**. The .zip archive will contain two files: `scores.txt`, and `metadata`.

Competition organizers can access the history for entries by following these steps:

1. Open [My Codalab](http://www.codalab.org/my).
1. Click the **Competitions I'm Running** tab.
1. Click the **Submissions** button for the competition.
1. Expand the row of the submission for which you want to view history.
1. Click **Download evaluation output from scoring step**. The .zip archive will contain two files: `scores.txt`, and `metadata`.

## Adding History to a Scoring Program
In order to log history, you need to incorporate a few functions into your scoring program (`evaluate.py`). These functions create the history folder and create the `scores.txt` file for each submission. The following example code shows `evaluate.py` for an example "Hello World" competition. All of the history functionality is indicated by code comments.

```
#!/usr/bin/env python
import sys
import os

input_dir = sys.argv[1]
output_dir = sys.argv[2]

submit_dir = os.path.join(input_dir, 'res')
truth_dir = os.path.join(input_dir, 'ref')

# Create a path for the history folder
history_dir = os.path.join(input_dir, 'history')

private_dir = os.path.join(output_dir, 'private')

audit_score = None
audit_submission_count = 0

if not os.path.isdir(submit_dir):
    print "%s doesn't exist" % submit_dir

if os.path.isdir(history_dir):
    # read all phases and their submissions:
    # input/history/<phase #>/<submission #>/
    #
    # Example:
    # input/history/000/001/ # phase #0, submission #1
    # input/history/000/002/ # phase #0, submission #2
    phases = {}

    for phase_folder in os.listdir(history_dir):
        if os.path.isdir(os.path.join(history_dir, phase_folder)):
            phases[phase_folder] = []
            for submission_file in os.listdir(os.path.join(history_dir, phase_file)):
                if os.path.isdir(os.path.join(history_dir, phase_folder, submission_file)):
                    phases[phase_folder].append(submission_file)

    # create list of score files
    #
    # Example:
    # input/history/000/001/output/scores.txt # submission #1 scores
    # input/history/000/002/output/scores.txt # submission #2 scores
    score_files = []

    for phase, submissions in phases.items():
        for submission in submissions:
            file_name = os.path.join(history_dir, phase, submission, 'output', 'scores.txt')
            if os.path.exists(file_name):
                score_files.append(file_name)

    # read and save score files
    #
    # Example scores.txt:
    #   correct:1.0
    #   audit_score:0.5
    scores = []
    audit_score = 0

    for score_file in score_files:
        file_data = open(score_file).read()
        first_line = file_data.split("\n")[0]
        audit_score += float(first_line.split(":")[1])

    audit_submission_count = len(score_files)
    if audit_submission_count > 0:
        audit_score = audit_score / audit_submission_count

if os.path.isdir(submit_dir) and os.path.isdir(truth_dir):
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)

    output_filename = os.path.join(output_dir, 'scores.txt')
    output_file = open(output_filename, 'wb')

    truth_file = os.path.join(truth_dir, "truth.txt")
    truth = open(truth_file).read()

    submission_answer_file = os.path.join(submit_dir, "answer.txt")
    submission_answer = open(submission_answer_file).read()

    audit_submission_count += 1

    if truth == submission_answer:
        correct = 1.0
    else:
        correct = 0.0

    audit_score = (audit_score + correct) / audit_submission_count

    output_file.write("correct:%s\n" % correct)
    output_file.write("audit_score:%s\n" % audit_score)

    output_file.close()

# Saving private info
if not os.path.exists(private_dir):
    os.makedirs(private_dir)

private_file = open(os.path.join(private_dir, 'priv.txt'), 'wb')
private_file.write("audit_score = %s" % audit_score)
private_file.close()

```
