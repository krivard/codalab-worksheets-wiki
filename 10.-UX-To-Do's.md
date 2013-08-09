1. Debug Developer Documentation - Go through the [the getting started for developers wiki page](https://github.com/codalab/codalab/wiki/Getting-Started-for-Developers) and make sure those instructions are working
1. Fixed header - Use the correct [foundation 4](http://foundation.zurb.com/) technique to make the header menu stay at the top of the browser viewport
1. Sections - Use the correct [foundation 4 sections](http://foundation.zurb.com/docs/components/section.html) technique to put sections on the "my competitions" page.
1. Login/Signup/Logout - Make these show up in formatted reasonably on the page, instead of full width.
1. Verify steps to generate css. For Windows the steps are:
   * Install Ruby from  [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/). During installation make select the option to add Ruby to your PATH. 
   * Open a command window and install compass by executing: 

     `gem update –system`

     `gem install compass`

   Then, to generate the css files after you've made some changes to the scss files in the sass folder:

     `cd codalab\apps\web\static\foundation`

     `compass compile`

   Or to start a process which will automatically re-generate the files when changes are made:

     `cd codalab\apps\web\static\foundation`

     `compass watch .`