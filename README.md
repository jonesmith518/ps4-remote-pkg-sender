# PS4 Remote PKG Sender v2  

This application has been highly inspired by @irefuse and is a full refactored version of the orginal repo.  

Based on Electron, Webpack, Vue, Express, Axios, Element-UI and Node this does not have any extra
dependencies. With this we can createa a cross platform application that works on mac, linux and Windows
and provides a nice GUI.  


## New features in v2.*
The refactored Version provides a better GUI and technicall more ordered features.  
*NEW Configure your server with your base path
*NEW Prepare Custom Server Configuration (build-in express, apache, nginx, custom, ...)  
*NEW Controll the Server application by click and have it running in the background  
*NEW Closing windows doesn't stop the Server but stops if you quit the application.   
*NEW Show a list of all Server side listed PKG's  
*NEW Miscs download link to flatZ PS4 Remote Package Installer homebrew  
*NEW Extended Menu and Tray Icon  
*NEW Separate Server Window (Logs, Server Routes, Controls)
*NEW Separate PS4 API Logs Window
*NEW Catch any possible Error on Request or Response with the RPI on your PS4
*NEW Scan base path (deep scan support) directory for fPKG's and serve them with the server
*NEW Search for titles through your found files
*NEW Add Served files to your Queue and install them on your PS4
*NEW Processing Center reflects any status changes in your server list, too.

#ToDo for
* Search your PS4 automatically in the current network (WIP)  
* Add a FAQ and Troubleshooting Area
* Configuration values for timeout and heartbeat
* Implement Auto-updater

## How To
So there you have it. How can you use it?  

### on PS4
Start HEN v1.8+  
Start flatZ Remote Package Installer  

### on PC
1.) Start PS4 Package Sender V2
2.) Switch to Config and select your Networkinterface (IP Adress)
2.1) Choose a Server Port if necessary and apply or restart the server
3.) Choose your base path where your files are settled
4.) Switch to Server and add your files to the queue (Processing Center)
5.) Start your install process with any of your files.

## Troubleshooting
Your PS4 and PC have to be on the same Network.  
If you have connection issues, check your Router or Firewall.  
If you think there is an issue, please report it.  

## Credits:
Thanks to flatz, Specter, xvortex
