# Competition Roadmap
This document provides an overview of how competitions work, and describes the processes involved in creating and running CodaLab competitions. 

## Contents
- How competitions work
- Types of competition

## How competitions work
In a typical CodaLab competition, participants compete to find the best approach for a particular problem. Competitions are conducted in two phases: training, and competition. The appropriate data is made available to participants at each phase of the competition. During the training phase, participants have access to training data to refine their algorithms. During the competition phase, participants are provided with competition data to generate results which they can then submit to the competition. Results are calculated at the end of each phase, at which point participants can see the competition results on the leaderboard.

## Types of competition
CodaLab competitions can be set up in several different ways. There are four main templates for competitions which we will refer to throughout the documentation. Competition templates are differentiated primarily by the type of data to be evaluated, and the method of evaluation. They are as follows:

- Pi: The simplest template, named for the subject matter of our demo competition. With the Pi template, competitors submit an answer which is compared with the known correct answer. Scores are then ranked according to their difference from the correct answer.

- Computer Vision: Similar to the Pi template, except that the Computer Vision template compares image-based data rather than straight numeric or text values.

- Basic Machine Learning: With the basic ML template, each competitor is provided with a set of training data which they can use to create a machine learning prediction. Each competitor then submits their prediction, which is then run against known correct answers and ranked.

- Complete Machine Learning: With this template competitors are provided with a set of training data which they can use to create an algorithm. This algorithm is then used to produce a prediction, which is compared with the gold standard. Scores are ranked according to their difference from the gold standard.




Windows-based binary executables are supported.