##Scenarios targeted for November

###1 Upload to & download from MyCodaLab using the CodaLab execution client

I have installed the CodaLab execution client on my machine.

As a registered user of CodaLab, I can obtain a token which allows the client tool to connect to my account on the CodaLab site.

I create a bundle locally. Then, using the client tool and appropriate credentials, I can issue a command to upload the bundle to my account.

Likewise, given the URL for a bundle in my account, I can issue a command in the client tool to download it.

Notes:

*  How are bundles named? What does the URL to my bundle look like?
*  Checksums should be used to validate the integrity of a bundle (see current prototype)
*  Thoughts on dealing with dependencies. In particular what does it mean to upload a bundle that has dependent bundles which only exist locally. In the first pass, we could assume that bundles do not have dependencies.
*  What are notes in the current prototype.

