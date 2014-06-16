# Running a Competition
This document explains how to create and run a CodaLab competition. It is assumed that you have already created a competition bundle by following the steps in the [Competition Roadmap](https://github.com/codalab/codalab/wiki/User_Competition-Roadmap).

## Contents
- [Uploading a Competition Bundle](#uploading-a-competition-bundle)
- [Editing a Competition](#editing-a-competition)
- [Publishing a Competition](#publishing-a-competition)
- [Un-Publishing a Competition](#un-publishing-a-competition)
- [Viewing Participants](#viewing-participants)
- [Approving/Denying Participants](#approvingdenying-participants)
- [Viewing Submissions](#viewing-submissions)
- [Deleting a Competition](#deleting-a-competition)

## Uploading a Competition Bundle
The competition bundle contains all of the information and files needed by CodaLab to create a competition.

**To upload a competition bundle to CodaLab**

1. Log in to <a href="http://www.codalab.org" target="_blank">codalab.org</a>.
1. At the top of the page, click **My CodaLab**. This will take you to the dashboard.
1. Click the **Competitions I'm Running** tab.
1. Click the **Create Competition** button.
1. Click the **Upload Definition** button. 
1. Using the **Open** dialog, select your competition bundle (.zip) and click **Open**. CodaLab will use the bundle to create a new competition.
1. Return to the dashboard to manage the competition. From the dashboard, you can edit, publish, and delete the competition, as well as manage participants and submissions.

## Editing a Competition
Once your competition has been uploaded, you can edit it by using the dashboard. Here, you can:
- Modify start and end dates for competition phases.
- Modify the HTML pages that describe the competition.
- Update reference data, scoring program, and logo.
- Toggle the public visibility of the competition.

**To edit a competition**

1. Log in to <a href="http://www.codalab.org" target="_blank">codalab.org</a>.
1. At the top of the page, click **My CodaLab**. This will take you to the dashboard.
1. Click the **Competitions I'm Running** tab.
1. Click the **Edit** button for the competition you want to edit.
1. Make the desired changes, then scroll to the bottom of the page and click **Submit**.

## Publishing a Competition
Publish a competition to make it publicly visible. Prior to being published, competitions are only visible to the competition organizer.

**To publish a competition**

1. Log in to <a href="http://www.codalab.org" target="_blank">codalab.org</a>.
1. At the top of the page, click **My CodaLab**. This will take you to the dashboard.
1. Click the **Competitions I'm Running** tab.
1. Click the **Publish** button for the competition you want to publish.

## Un-Publishing a Competition
Un-publishing a competition takes it offline so that it can only be viewed by the competition organizer.

**To un-publish a competition**

1. Log in to <a href="http://www.codalab.org" target="_blank">codalab.org</a>.
1. At the top of the page, click **My CodaLab**. This will take you to the dashboard.
1. Click the **Competitions I'm Running** tab.
1. Select a competition and click the **Edit** button.
1. Under **General Information**, uncheck the checkbox next to **Publicly Available**.
1. Scroll to the bottom of the page and click **Submit**.

## Viewing participants
When a participant registers for a competition, they are added to a list of participants. Follow these steps to view the participants for a competition.

**To view competition participants**

1. Log in to <a href="http://www.codalab.org" target="_blank">codalab.org</a>.
1. At the top of the page, click **My CodaLab**. This will take you to the dashboard.
1. Click the **Competitions I'm Running** tab.
1. Select a competition, and click the **Participants** button to see the list of participants.

## Approving/Denying Participants
The competition organizer must approve or deny each participant who registers for a competition. Follow these steps to approve or deny newly registered participants.

**To approve/deny participants**

1. Follow the instructions to view participants for a competition. Pending participants are listed at the top.
1. For each participant, select a status of **Approve** or **Deny** from the drop-down menu. You can give a short message to the participant by typing in the **Reason** field.
1. Click the **Process** button to complete the approval process.

## Viewing Submissions
The competition organizer can view all of the submissions for a competition. These are displayed in a table which shows:

- Time and date of submission
- Participant ID
- Filename (click to download the competition bundle for that submission)
- Status
- Leaderboard (True if the submission has been submitted to the leaderboard, otherwise False)

Click the plus sign in the last column to access additional links for each competition. Here, you can:

- Download the submission
- View standard output and error logs
- Download evaluation output from the prediction and scoring steps

## Deleting a Competition

**To delete a competition**

1. Log in to <a href="http://www.codalab.org" target="_blank">codalab.org</a>.
1. At the top of the page, click **My CodaLab**. This will take you to the dashboard.
1. Click the **Competitions I'm Running** tab.
1. Click the **Delete** button for the competition you want to delete. You will be prompted to confirm the deletion.
**NOTE:** If you have previously published the competition, you will need to un-publish the competition first. See [Un-Publishing a Competition](#un-publishing-a-competition).