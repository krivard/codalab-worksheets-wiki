# Hosting a competition

The job execution and provenance facilities of CodaLab Worksheets enable a wide variety of applications in computational experimentation, including hosting competitions on machine learning tasks.

We provide a small application built on the [CodaLab Worksheets API](http://codalab.org/codalab-cli/rest.html) for running small-scale Kaggle-like competitions. It is packaged as a simple Python script in the [codalab-cli repository](https://github.com/codalab/codalab-cli). Competition participants can "submit" their models to the competition by running them on an public dataset provided by the competition organizers, then tagging the resulting predictions bundle with a specific tag. The script searches for bundles with the configured tag, and "mimics" the submitted executions -- rerunning the models while replacing the public dataset with the hidden evaluation dataset. Competition organizers can run this script manually, as a cron job, or as a long-lived daemon.

# Installation and setup

Clone the [codalab-cli repository](https://github.com/codalab/codalab-cli):

    $ git clone https://github.com/codalab/codalab-cli.git

Install all the dependencies:

    $ cd codalab-cli
    $ ./setup.sh server

Now when you run the competition script with the help flag `-h`, you should see something like this:

    $ scripts/competitiond.py -h

    usage: competitiond.py [-h] [-l] [-d] [-v] config_file output_path

    Competition leaderboard evaluation daemon.

        1. Find bundles tagged with {submission_tag} and filter them.
        2. Run the {predict} command with the submitted bundle to generate
           predictions on the test set.
        3. Tag the resulting test run bundle with {predict.tag}, untagging
           any previous test run bundles for the same submitter.
        4. Run {evaluate} command with the test run bundle.
        5. Tag the resulting evaluation bundle with {evaluate.tag}, untagging
           any previous evaluation bundles for the same submitter.

    If in daemon mode, performs the above steps in a loop every
    {refresh_period_seconds} seconds. Otherwise, just runs through them once.

    All bundles created by this daemon are added to {log_worksheet_uuid}.
    Each user will be limited to {max_submissions_per_period} every
    {quota_period_seconds}, and {max_submissions_total} ever.

    positional arguments:
      config_file           YAML/JSON file containing configurations.
      output_path           path to write JSON file containing leaderboard.

    optional arguments:
      -h, --help            show this help message and exit
      -l, --leaderboard-only
                            Generate a new leaderboard but without creating any new runs.
      -d, --daemon          Run as a daemon. (By default only runs once.)
      -v, --verbose         Output verbose log messages.

The competition script takes in a config file (in YAML or JSON format) and performs the actions
listed above, then generates a leaderboard in JSON format at the specified output path.

First, let's create a config file for your competition. Start up your favorite editor and create
a new file, say, at `~/competition-config.yml` (this can be anywhere), with the following contents:

```yaml
max_submissions_per_period: 5                               # allows at most 5 submissions per user per period, where period is 24 hours by default
log_worksheet_uuid: '0x2263f854a967abcabade0b6c88f51f29'    # uuid of the worksheet to create new run bundles in
submission_tag: cool-competition-submit                     # configure the tag that participants use to submit to the competition

# Configure how to mimic the submitted prediction bundles
predict:
  mimic:
  - {new: '0xbcd57bee090b421c982906709c8c27e1', old: '0x4870af25abc94b0687a1927fcec66392'}  # replace `old` bundle with `new` bundle

# Configure how to evaluate the new prediction bundles
evaluate:
  # Essentially
  #     cl run evaluate.py:0x089063eb85b64b239b342405b5ebab57 \
  #            test.json:0x5538cba32e524fad8b005cd19abb9f95 \
  #            predictions.json:{predict}/predictions.json --- \
  #            python evaluate.py test.json predictions.json
  # where {predict} gets filled in with the uuid of the mimicked bundle above.
  dependencies:
  - {child_path: evaluate.py, parent_uuid: '0x089063eb85b64b239b342405b5ebab57'}
  - {child_path: test.json, parent_uuid: '0x5538cba32e524fad8b005cd19abb9f95'}
  - {child_path: predictions.json, parent_path: predictions.json, parent_uuid: '{predict}'}
  command: python evaluate.py test.json predictions.json

# Define how to extract the scores from the evaluation bundle
score_specs:
- {key: '/stdout:f1', name: f1}
- {key: '/stdout:exact_match', name: exact_match}
```

# Running the competition

There are many ways you could choose to set up the competition. The recommended way is to set up a static webpage that loads the generated leaderboard JSON file and formats it into a leaderboard table using a simple templating language such as [Mustache](https://mustache.github.io/). We even provide an (example HTML page that does just that)[https://github.com/codalab/codalab-cli/blob/ecbc9146918415b3a53d1e61dc8c9c9185cc10ba/scripts/leaderboard.html]. Just make sure to tweak it for your own purposes.

Running the competition script in daemon mode will start a long-running process that periodically checks for new submissions, runs them, then updates the leaderboard file.

    $ scripts/competitiond.py -d ~/competition-config.yml /var/www/leaderboard.json

# Submitting a model (as a participant)

For the participant, submitting a model involves uploading their code, running it against a public dataset provided by the competition organizers, tagging the bundle appropriately, then waiting for the next time the competition script checks for new submissions. The [submission tutorial for the SQuAD competition](https://worksheets.codalab.org/worksheets/0x8403d867f9a3444685c344f4f0bc8d34/) provides a good example.
