---
layout: module
title: Module 6&#58; Using Hooks
---
In this module we'll learn how to use hooks to manipulate the native Android or iOS project. Cordova Hooks represent special scripts which could be added by application and plugin developers or even by your own build system to customize cordova commands.

Hooks might be related to your application activities such as such as `before_build`, `after_build`, etc. Or, they might be related to the plugins of your application. For example, hooks such as `before_plugin_add`, `after_plugin_add`, etc applies to plugin related activities. For a full list of the available hook triggers read the [hooks documentation](https://cordova.apache.org/docs/en/latest/guide/appdev/hooks/). These hooks can be associated with all your entire app, a single platform in your app or be specific to only one plugin.

## Steps
1. Open a new terminal window.

2. In the root project folder create a new folder call `scripts`

        mkdir scripts

3. Create a new file called `scripts/deletejunk.js` and copy the following code into the file:

        module.exports = function(context) {
          /*
           * Remove junk files from the project before build so they don't get compiled
           * into the application, preventing unwanted bloat.
           */

          var fs = require("fs");
          var path = require("path");

          // All directories in the www path that you want scanned for junk
          var foldersToProcess = [
              ".",
              "js",
              "css"
          ];

          // An array of files that you consider junk and want removed
          var junkFileList = [
              ".DS_Store",
              "Thumbs.db"
          ]

          foldersToProcess.forEach(function(folder) {
              processFiles("www/" + folder);
          });

          function processFiles(dir) {
              console.log("Scanning directory for junk...");
              fs.readdir(dir, function(err, list) {
                  if(err) {
                      console.log("File processing error " + err);
                      return;
                  }
                  list.forEach(function(file) {
                      file = dir + "/" + file;
                      fs.stat(file, function(err, stat) {
                          if(!stat.isDirectory()) {
                              if(junkFileList.indexOf(path.basename(file)) === 0) {
                                  fs.unlink(file, function(error) {
                                      console.log("Removed file " + file);
                                  });
                              } else {
                                  console.log("Skipping file " + file);
                              }
                          }
                      });
                  });
              });
          }
        }


4. Add the hook to the `config.xml` file anywhere inside the `widget` tag so it is run when you build your app:

        <hook type="before_prepare" src="scripts/deletejunk.js"/>

5. Try building your app:

        phonegap build browser

   Now anything filenames you add into the `junkFileList` will be removed from the `www` folder.

>To learn more about hooks check out the [hooks documentation](https://cordova.apache.org/docs/en/latest/guide/appdev/hooks/) on the Apache Cordova site.


<div class="row" style="margin-top:40px;">
 <div class="col-sm-12">
 <a href="lesson5.html" class="btn btn-default"><i class="glyphicon glyphicon-chevron-left"></i> Previous</a>
 <a href="lesson7.html" class="btn btn-default pull-right">Next <i class="glyphicon glyphicon-chevron-right"></i></a>
 </div>
</div>
