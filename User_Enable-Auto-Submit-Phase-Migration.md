# Enabling Automatic Submission and Phase Migration
The automatic submission feature automatically adds submissions to the leaderboard. Phase migration facilitates migrating submissions from phase to phase, saving participants from having to download and re-upload submissions when moving between phases.

## How to Enable Automatic Submission
Follow these steps to enable automatic submission for a competition.

1. Open competition.yaml in a text editor.
1. Use the `force_submission_to_leaderboard` key to toggle the feature by adding the following line at the top (as a peer to `title`, `description`, etc.).

    `force_submission_to_leaderboard: true`

    A value of **true** enables the feature; **false** disables it.

1. Save your changes.

If the competition has already been uploaded to CodaLab, you can toggle this feature by [editing the competition](User_Running-a-Competition#editing-a-competition) and checking/un-checking **Force submission to leaderboard** under **General Information**.

## How to Enable Phase Migration

1. Open competition.yaml in a text editor.
1. For each phase, use the `auto_migration` key to enable/disable automatic migration for each phase.

   ```
    ...
    phases:
      0:
        auto_migration: true # Enable auto
        ...
      1:
        auto_migration: false # don't do migrations for this phase
        ...

    ```

    A value of **true** enables the feature; **false** disables it.

1. Save your changes.

If the competition has already been uploaded to CodaLab, you can toggle this feature by [editing the competition](User_Running-a-Competition#editing-a-competition) and checking/un-checking **Auto migration** under the appropriate **Competition Phase**.