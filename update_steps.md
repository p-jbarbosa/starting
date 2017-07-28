# d3ComponentLibrary CPK and CPF Libraries Update

[d3ComponentLibrary](https://github.com/webdetails/d3ComponentLibrary) plugin was updated to the new [CPK](https://github.com/webdetails/cpk) and [CPF](https://github.com/webdetails/cpf) versions, and therefore it could now be installed and used on Pentaho 7.x versions.

The following steps represent the process executed to perform this update (they also may represent a little help to future updates on plugins with similar dependencies):

> **Attention**: To complete the necessary plugin build it's necessary to have **ant** installed on your machine. 

1. Fork the project repository from github repository
    * this will allow you to create your own project instance
    * in this case: [d3ComponentLibrary](https://github.com/webdetails/d3ComponentLibrary)
1. Clone to your local machine the project
1. Go to your cloned project folder 
1. Find the project's **build.properties** file and updated it to reference the correct libraries version
    * for this operation we updated the following entrances
    ``` properties
    cpf.pentaho.artifact=cpf-pentaho
    dependency.cpk.revision=7.0.0.0-25
    dependency.cpf.revision=7.0.0.0-25
    ```
    * this changes should be done on both master and releases branchs
1. Create the new jars for CPK and CPF libraries executing the following ant command on your local project version
    ``` ant
    ant clean resolve
    ```
    * this command will create a new directory, called **lib**, below your local project main folder, where you can check for the newly create jar files
    * this folder is ignored by git
1. Build the binaries for distribution executing the ant command
    ``` ant
    ant dist
    ```
    * the execution will create another folder, called **dist**, also under the root of your local project, where you can find a .zip file that will keep all the necessary artifacts to allow the plugin installation
    * this folder is also ignored by git
1. Make this .zip file available on a web server
    * You can use github for this, the release section of a project allows file uploads that will not be tracked on git tree, and on draft mode no one besides you knows that they exist
    * this step will allow plugin installation tests, using the Pentaho Marketplace features
1. Test new plugin version installation with Pentaho Marketplace
    * **Note**: Although this step can be avoid by unzip the new plugin version (.zip file) on _\<local\_pentaho\_installation\>/pentaho-server/pentaho-solutions/system_, you will eventually need to do it, for complete cycle test purposes
    1. Fork and clone from github, to your local machine, the [marketplace-metadata](https://github.com/pentaho/marketplace-metadata) project (like you already have done with d3ComponentLibrary)
    1. Go to the project root directory, find __marketplace.xml__ and open it
    1. Locate and change the entry related to d3ComponentLibrary pluginfor this type of updates we only care about the _version_ tag:
        * add another one, based on the previous
        * change the _version_ sub tag to a new one
        * change also the _package\_url_ tag to point to the .zip new web location (point 7 of this sequence)
    1. Commit/push this changes to your own **marketplace-metadata** project
    1. Change the plugin repository .xml used by your Pentaho instance to show and install plugins
        * Find the file _\<local\_pentaho\_installation\>/pentaho-server/pentaho-solutions/system/karaf/etc/pentaho.marketplace.ba.cfg_
        * change the pointer used by Pentaho to load the Marketplace repository, to use your  marketplace-metadata on github
    1. Launch Pentaho server
    1. Go to Marketplace find d3ComponentLibrary and install it
1. Update your plugin instance on github (here d3ComponentLibary), by performing commit/push
1. Make a Pull Request to the main plugin project on github
1. Test the official plugin compilation
    * Pull Request acceptance on the main project will automatically trigger an official plugin build (similar to the one we have performed on steps 5 and 6)
    * the .zip file produce is made available on [jenkins plugin repository](http://ci.pentaho.com/job/d3componentlibrary/)
    * use this location to perform the same installation test exposed on step 8 (again, you can test this by only unzip de file to _../pentaho-solutions/system/_ folder)
1. Download the .zip file from jenkins plugin repository
1. Commit/push the official .zip file to [marketing-ctools repo](https://github.com/pentaho/marketing-ctools)
    * if you don't have permissions to do it, ask for someone to do it for you
    * this commit/push will should create another folder, to hold the new plugin version
1. Open a jira ticket, for EngOps, to ask them to copy this .zip, from marketing-ctools repo to the final repository on __ctools.pentaho.com__
1. After they acknowledge on this opoeration, test again the plugin installation, now using the final .zip file location (now you need to perform step 8)
1. Commit/Push the __marketplace.xml__ from your local **marketplace-metadata** project
    * this will be used to update the official __marketplace.xml__ file
1. Make a Pull Request to [marketplace-metadata](https://github.com/pentaho/marketplace-metadata), for the __marketplace.xml__ file
1. You're done.