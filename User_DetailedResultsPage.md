There is a feature "enable_detailed_results", which can be included in the competition YAML file. 
enable_detailed_results: True
After enabling this feature, the scoring program should take care of the creation of an html file. If this file is created by the scoring program, CodaLab can access the file which is displayed in the leaderboard. There are a few important points to be able to use this module correctly
* The images need to be embedded using base64 encoding in the html page rather than adding a html tag "img". This is needed because the images can't be accessed from the blob storage link (needs SAS key again for each image). 
* If you are using any javascript or CSS formatting in the html file, please make sure these files are publically accessible by the html file. Again the html file can't access any private blob storage in CodaLab.
* If Linux compute worker is used, add these lines to the scoring program (only for python and matplotlib).

`import matplotlib`
`matplotlib.use('Agg') #very important to make it work on Linux`

Example scoring program to create an html page with figures and tables:


