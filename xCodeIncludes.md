# Introduction #

Including a library in your XCode project is, more or less, a simple matter of including its headers and binary files in your project's search path. This article will describe a very simple series of steps to include any library in your Xcode project.


# Details #

  1. Download your library. For example:
  1. Unzip to where you would like to keep your libraries. I keep mine in user-home-directory/Library/Developer/Ext. This should create the following folder: user-home-directory/Library/Developer/Ext/UnittWebSocketClient.
  1. Now we need to include the library in our project. Our extracted folder should have two subdirectories: bin & include. The bin folder holds the binary builds of your library and the include folder holds the source header files. Grab one of the binary files in one of the platform folders, for example: Debug-iphoneos/lib/UnittWebSocketClient.a. Drag and drop this into your project inside of Xcode. Xcode will create a reference to this specific path in your active target. We want to change this to be up at the project level so all the targets can use it. We also want it to choose the appropriate binary for the right environment and mode, for example: selecing the user-home-directory/Library/Developer/Ext/UnittWebSocketClient/bin/Debug-iphoneos/lib/UnittWebSocketClient.a when you are debugging against a physical iOS device.
  1. Change the specific reference in our target to a dynamic one in our project. Open the "Build Settings" on your target. Type "search" in the search box at the top and double-click "Library Search Paths" to edit it. Remove the reference that was added. Now open the "Build Settings" in your project and edit its "Library Search Paths" to include "user-home-directory/Library/Developer/Ext/UnittWebSocketClient/bin/$(CONFIGURATION)$(EFFECTIVE\_PLATFORM\_NAME)". These last two flags will tell it to dynamically choose the right binary based on your environment and build mode.
  1. We need to add the headers as well. Edit the "Header Search Paths" in your project to include a "user-home-directory/Library/Developer/Ext/UnittWebSocketClient/include" entry. Back in your target, make sure that in your "Header Search Paths" there is an entry "$(inherited)".

You should now be set to start using your library!