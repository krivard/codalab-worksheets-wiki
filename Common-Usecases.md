# Common Usecases
This wiki document is intended to guide users to perform common tasks with codalab.

## Upload an experiment with an ipython notebook.
Consider the executable paper at (). To create this notebook, 
* source on github: https://github.com/sidaw/polymom
* upload the github to X.
* try to convert the ipython notebooks using the default codalab/python image -- observe that the notebook fails because it lacks a particular library (ipdb in this case).
* Create a new docker image (arunchaganty/polymom) and install these missing libraries.
docker ...
* rerun with this new image.
* profit!