# Competition Roadmap
This document provides an overview of how competitions work, and describes the processes involved in creating and running CodaLab competitions. 

## Contents
- [How Competitions Work](#how-competitions-work)
    - [Competition Bundle File Structure](#competition-bundle-file-structure)
    - [Types of Competition](#types-of-competition)
- [Competition End to End Process](#competition-end-to-end-process)
    - Planning a Competition
    - Making Data Available Securely
    - Creating a Scoring Program
    - Creating a Competition Bundle
    - Running a Competition

## How Competitions Work
In a typical CodaLab competition, participants compete to find the best approach for a particular problem. Competitions are conducted in three phases: test, training, and competition. The appropriate data is made available to participants at each phase of the competition. During the test and training phases, participants have access to training data to develop and refine their algorithms. During the competition phase, participants are provided with competition data to generate results which they can then submit to the competition. Results are calculated at the end of each phase, at which point participants can see the competition results on the leaderboard.

### Competition Bundle File Structure
Competitions consist of a set of files collectively known as a "bundle". Although technically CodaLab considers any zipped archive to be a bundle, competition bundles generally contain a specific assortment of files:
- **competition.yaml** define the contents of the competition bundle, as well as links to external data which the organizer may want to make available.
- **HTML pages** contain descriptive text and instructions for each phase of the competition.
- **program files** contain the files that make up the scoring program. This is typically Python (.py) files, but Windows-based binary executables are also supported.
- **reference data** contains training data for the competition.

This listing gives a general idea of the files which make up a competition, although this can vary depending on the type of competition.

### Types of Competition
CodaLab competitions can be set up in several different ways. There are four main templates for competitions which we will refer to throughout the documentation. Competition templates are differentiated primarily by the type of data to be evaluated, and the method of evaluation. They are as follows:

- Pi: The simplest template, named for the subject matter of our demo competition. With the Pi template, competitors submit an answer which is compared with the known correct answer. Scores are then ranked according to their difference from the correct answer.

- Computer Vision: Similar to the Pi template, except that the Computer Vision template compares image-based data rather than straight numeric or text values.

- Basic Machine Learning: With the basic ML template, each competitor is provided with a set of training data which they can use to create a machine learning prediction. Each competitor then submits their prediction, which is then run against known correct answers and ranked.

- Complete Machine Learning: With this template competitors are provided with a set of training data which they can use to create an algorithm. This algorithm is then used to produce a prediction, which is compared with the gold standard. Scores are ranked according to their difference from the gold standard.

## Competition End to End Process
In this section we'll walk through the major segments of the competition creation process.

### Planning a Competition
The very first step in creating a competition is planning. Some questions you will want to ask are:
- Which competition template will I choose for a starting point?
- How will I make data securely available to participants?
- Which steps will I take to secure my data?
- What type of scoring program will I need to create?

### Making Data Available Securely
[TBD]

### Creating a Scoring Program
The scoring program evaluates the competition submissions for each phase, comparing each submission with a set of reference data, and then passing the scores along to CodaLab for display on the competition leaderboard.

For more information see [Building a Scoring Program for a Competition](https://github.com/codalab/codalab/wiki/User_Building-a-Scoring-Program-for-a-Competition).

### Creating a Competition Bundle
The next step is creating a competition bundle. For detailed instructions, see [Building a Competition Bundle](https://github.com/codalab/codalab/wiki/User_Building-a-Competition-Bundle).

### Running a Competition
Once your competition is up and running, you can manage it from your [CodaLab Dashboard](https://www.codalab.org/my/). For more details, see [Running a Competition](https://github.com/codalab/codalab/wiki/User_Running-a-Competition)