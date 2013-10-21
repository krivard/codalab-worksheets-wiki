##Scenarios targeted for November

###1 Upload to & download from MyCodaLab using the CodaLab execution client

I have installed the CodaLab execution client on my machine.

As a registered user of CodaLab, I can obtain a token which allows the client tool to connect to my account on the CodaLab site.

I create a bundle locally. Then, using the client tool and appropriate credentials, I can issue a command to upload the bundle to my account.

Likewise, given the URL for a bundle in my account, I can issue a command in the client tool to download it.

Notes:

* How are bundles named? What does the URL to my bundle look like?
* Need to agree on an archiving format for bundles: ZIP? A key attribute is that we must be able to read part of a bundle without having to decompress the entire bundle. 
* Checksums should be used to validate the integrity of a bundle (see current prototype)
* Thoughts on dealing with dependencies. In particular what does it mean to upload a bundle that has dependent bundles which only exist locally. In the first pass, we could assume that bundles do not have dependencies.
* What are notes in the current prototype.

###2.1 View my bundles on the CodaLab web site

After I login on the CodaLab web site, I can access pages under MyCodaLab that allow me to see the bundles that I have uploaded with the client tools. Each item in the bundle list view shows key attributes of the 
bundle (e.g. title, when created, type...)

###2.2 View the details of my bundle.

(Not sure if that's important in the short term)
From the list view for my bundles, I can select a bundle and see more information about it.

###2.3 Download a bundle from the CodaLab web site

From the list view for my bundles, I have the option to download any bundle that I choose. I download them one at a time.

###2.4 Upload a bundle to the CodaLab web site from a page on the site itself.

On the CodaLab web site, I can access a page that allows me to upload a bundle to the site. I must be logged in to do the upload.

