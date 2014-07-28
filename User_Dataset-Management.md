

## Creating datasets

1. Login
1. Click on "My Datasets" in the top right near your username
1. Click the "Create dataset" button in the top left of the content area
1. Fill in all of the relevant information
1. Click the "Upload" button
1. If everything was successful, the dataset should appear in the list of datasets

## Removing datasets


1. Login
1. Click on "My Datasets" in the top right near your username
1. Find the dataset you want to delete, click the "DEL" button on the right
1. Confirm that you want to delete it -- the dataset may already be in use in a competition, which you should be warned about.

## Modifying datasets


1. Login
1. Click on "My Datasets" in the top right near your username
1. Find the dataset you want to edit, click the "EDIT" button on the right
1. Once you are done save the form

## Downloading datasets directly


1. Login
1. Click on "My Datasets" in the top right near your username
1. Click the "Download" button to the right of the dataset you want to download

## Using datasets in your competition.yaml

For `input_data`, `scoring_program`, or `reference_data` you change the file name to the UUID of the dataset.

```
phases:
  0:
    phasenumber: 0
    reference_data: af5e8c26-73b0-485a-a8b9-a572dd88d828
    scoring_program: 21a8f881-2e53-4c71-b841-47e78e0b4040
    input_data: few2f881-2rp3-4221-b121-j4kj3934jt42

```

For datasets you use a new attribute `key` instead of `url`:

```
    datasets:
      1:
        name: Training Data
        key: 21a8f881-2e53-4c71-b841-47e78e0b4040
        description: Training data
```

## Using datasets in existing competitions

1. Login
1. Go to "My Codalab"
1. Go to "Competitions I'm running" tab
1. Find the competition you want to edit, click "Edit"
1. Scroll to the phase you want to modify and you can select a new `input_data`, `reference_data`, or `scoring_program` from datasets you have uploaded.
1. Click "Save" to complete 

*NOTE: You cannot change Dataset entries without uploading a new `competition.yaml`, this is a limitation of the current Competition Edit Form*