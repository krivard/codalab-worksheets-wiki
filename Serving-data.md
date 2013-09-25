
Follow the following procedure to replicate the data server setup.

### Create a Linux VM to host the server

Login to [Windows Azure management portal](http://manage.windowsazure.com) using the codalabs@outercurve.org account ID.

From the management portal, create new Virtual Machine from gallery, choosing:

        OS = Ubuntu 13.04
        VM size = medium (2 cores)
        Set the certificate and the username (azureuser) and password
        DNS name = codalab-data.cloudapp.net

Wait a few minutes until the server is provisioned, then use SSH to log into it. In this case log on to:

        azureuser@codalab-data.cloudapp.net

### Setup the server


