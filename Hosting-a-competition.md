The job execution and provenance facilities of CodaLab Worksheets enable a wide variety of applications in computational experimentation, including hosting competitions on machine learning tasks.

We provide a small application built on the [CodaLab Worksheets API](http://codalab.org/codalab-cli/rest.html) for running small-scale Kaggle-like competitions. It is packaged as a simple Python script in the [codalab-cli repository](https://github.com/codalab/codalab-cli). Competition participants can "submit" their models to the competition by running them on an public dataset provided by the competition organizers, then tagging the resulting bundle with a specific tag. The script searches for bundles with the configured tag, and "mimics" the submitted executions, rerunning the models while replacing the public dataset with the hidden evaluation dataset. Competition organizers can run this script manually, as a cron job, or as a long-lived daemon.

# Installation and setup

# Running

# Submitting
