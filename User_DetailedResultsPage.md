There is a feature "enable_detailed_results", which can be included in the competition YAML file. 
enable_detailed_results: True
After enabling this feature, the scoring program should take care of the creation of an html file. If this file is created by the scoring program, CodaLab can access the file which is displayed in the leaderboard. There are a few important points to be able to use this module correctly
* The images need to be embedded using base64 encoding in the html page rather than adding a html tag "img". This is needed because the images can't be accessed from the blob storage link (needs SAS key again for each image). 
* If you are using any javascript or CSS formatting in the html file, please make sure these files are publically accessible by the html file. Again the html file can't access any private blob storage in CodaLab.
* If Linux compute worker is used, add these lines to the scoring program (only for python and matplotlib).

`import matplotlib`
`matplotlib.use('Agg') #very important to make it work on Linux`

Example scoring program to create an html page with figures and tables:

    import matplotlib
    matplotlib.use('Agg')
    import os, os.path, sys
    import math
    import matplotlib.pyplot as plt
    import shutil
    from pylab import *
    import itertools
    import numpy as np

    def f(t):
        return np.exp(-t) * np.cos(2*np.pi*t)

    def createHTML(outputDir):
	htmlOutputDir = os.path.join(outputDir, "html")
	imagesOutputDir = os.path.join(htmlOutputDir, "images")
	if not os.path.exists(htmlOutputDir):
			os.makedirs(htmlOutputDir)
			os.makedirs(imagesOutputDir)
			
 	t1 = np.arange(0.0, 5.0, 0.1)
	t2 = np.arange(0.0, 5.0, 0.02)
	fig1 = plt.figure()
	plt.plot(t1, f(t1), 'bo', t2, f(t2), 'k')
	plt.savefig(os.path.join(outputDir,"testCurve.png"), bbox_inches=0, dpi=300)
	
	#image 1 (Example)
	imageOrg = os.path.join(outputDir, "testCurve.png")
	data_uri1 = open(imageOrg, 'rb').read().encode('base64').replace('\n', '') #encode the image as base64 to embed it in the html
	img_tag1 = 'src="data:image/png;base64,{0}"'.format(data_uri1)
	
			
	htmlString = '''<!DOCTYPE html>
	<html>
	<head>
        <title>Demo Html</title>
        <link href="theme.default.css" rel="stylesheet">  #Make sure this is a public location
        <script type="text/javascript" src="jquery.min.js"></script> #Make sure this is a public location
        <script type="text/javascript" src="jquery.tablesorter.min.js"></script> #Make sure this is a public location
        <script>
           $(function(){
              $("#table1").tablesorter({widgets: ['zebra']});
           });
        </script>
        <style>
			table.tablesorter td {
				text-align: center;
			}
			
			td.left-align {
				text-align: left !important;
			}
        </style>  
	</head>
	<body>
	<p>Description here</p>
	<p>
	The overall score and the scores in different categories of this system is given in the below table. </p>
	<p>
	<img border="0" %s alt="FROC" width="576pt" height="432pt">
	<p>
	The following table summarizes the results.</p>
	<p>
	<table border=1 class = "tablesorter" id="table1" style="width: auto; margin: 10px auto 0 auto;">
	    <thead>
	    <tr>
            <td class ="firstcol">FPs/scan</td><td align=center width='54'>1/8</td>
            <td align=center width='54'>1/4</td>
            <td align=center width='54'>1/2</td><td align=center width='54'>1</td>
            <td align=center width='54'>2</td><td align=center width='54'>4</td>
            <td align=center width='54'>8</td><td align=center width='54'>average</td>
        </tr>
        </thead>
        <tbody>
			<tr class = ""><td class="left-align">Category 1</td><td>0.470</td><td>0.491</td><td>0.573</td><td>0.658</td><td>0.711</td><td>0.761</td><td>0.778</td><td>0.634</td></tr>
			<tr class = ""><td class="left-align">Category 2</td><td>0.423</td><td>0.483</td><td>0.567</td><td>0.611</td><td>0.714</td><td>0.778</td><td>0.822</td><td>0.628</td></tr>
			<tr class = ""><td class="left-align">All</td><td>0.450</td><td>0.488</td><td>0.570</td><td>0.638</td><td>0.712</td><td>0.768</td><td>0.797</td><td>0.632</td></tr></tbody>        
		</tbody>
	</table>	
	</div>	
	</body>
	</html>''' % (img_tag1)
		
	htmlfile = open(htmlOutputDir+"\\detailed_results.html","w")
	htmlfile.write(htmlString)
	htmlfile.close()
  
    ### main 
  
    if __name__ == "__main__":
	##Stuff for CodaLab scoring program to work##
	input_dir = sys.argv[1]
	output_dir = sys.argv[2]
	print sys.path[0] 
	outputDir = output_dir+"/"
	
	submit_dir = os.path.join(input_dir, 'res') + "/"
	truth_dir = os.path.join(input_dir, 'ref') + "/"
	
	if not os.path.isdir(submit_dir):
		print "%s doesn't exist" % submit_dir

	if os.path.isdir(submit_dir) and os.path.isdir(truth_dir):
		if not os.path.exists(output_dir):
			os.makedirs(output_dir)
			
	##writing codaLab scores.txt##
	output_filename = os.path.join(output_dir, 'scores.txt')              
	output_file = open(output_filename, 'wb')
	output_file.write("SENSITIVITY: %.3f\n" % (float(88) / float(100)))
	output_file.write("CANDIDATES: %.3f\n" % (float(120) / float(50)))
	output_file.write("TP: 88\n")
	output_file.write("FP: 20\n")
	output_file.write("FN: 12\n")
	output_file.write("TN: 32")
	output_file.close()
	
	##CodaLab stuff ends	
	createHTML(outputDir)`