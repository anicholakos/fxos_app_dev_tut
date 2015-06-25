.. Copyright (C) Romin Irani. Permission is granted to copy, distribute
   and/or modify this document under the terms of the Creative Commons
   Attribution-ShareAlike 4.0 International Public License.


.. _device_storage:

Device Storage
==============

Welcome to Episode 9 of the Firefox OS App Development Tutorial. In the
previous episode, we looked at how you can use Web Activities in your Firefox
OS Applications. In this episode, we shall look at how you can access device
storage in your Firefox OS Apps and  we will see how to browse
through the content on your SD card, pictures, videos and music areas on your
phone.

Prerequisites
-------------

* You have setup your machine with the Firefox OS simulator. If not you can
  check out :ref:`dev_setup`, which takes you through the entire setup.
* You have a basic understanding of writing Firefox OS Apps. If not, I
  strongly suggest refering to earlier episodes, especially :ref:`hello_world`,
  that covers how to write your first Firefox OS App.

What this Episode covers 
------------------------

* `Device Storage
  <https://developer.mozilla.org/en-US/docs/Web/API/Device_Storage_API>`__ and
  how to use the Device Storage APIs.
* Sample Firefox OS Application that covers how to access Device Storage.

Episode 9 in Action
-------------------

Let us check out the application in action first to understand what we are
trying to do here.

What we shall write is a mobile application that will allow us browse through
the files that are present on the different storage areas of the device. These
different storage areas include the SD card, pictures, videos, and music. It will
give you enough information to see how to access the files, get the file
metadata. The episode does not cover how to write or create new files but the
Storage API does provide that facility too, should you need that in your
application.

All right then, the first screen of the mobile app is shown below: 

.. image:: illustrations/episode09/devicestorage1.png
   :alt: Opening image of Device Storage app
   :height: 350px

The screen has 4 buttons that will allow you to browse the contents of the
particular device storage area on your phone.

Keep in mind that the SD card browsing will not work on the simulator, but it should
work fine on the device. I am able to browse videos, music, and pictures just fine on the simulator. 

When you click on the "Browse Music" button, it will access the music storage
area and list down the contents. I believe the simulator goes and fetches it
from the "Music" folder on the OS that you running it on and, in my case, it lists
the files that are there in the "Music" folder. Notice that we list down the
files and also other associated meta data like when it was last updated, file type, and the file size.

Similarly, when you click on the "Browse Pictures" button, it will display all
the images that are present in the Pictures storage area. I simply show a
little preview by creating an image element as shown below. Once again, we not
only list the files but also display the meta data: last updated, file type
and file size.

.. image:: illustrations/episode09/devicestorage2.png
   :alt: App displaying pictures 
   :height: 350px
      
Download Full Source Code
-------------------------

I suggest that you begin with a full download of the project source code. Since
the project depends on libraries like jQuery and jQuery Mobile, it will save
you the hassle of downloading the dependent libraries.

Go ahead & download the code from: https://github.com/anicholakos/DeviceStorage

Extract all the code in some folder. On my machine, the code is
present in ``/home/anicholakos/Projects/DeviceStorage`` but it could be any
directory of your choice. You should see a folder structure inside of
DeviceStorage, that looks something like this:

.. image:: illustrations/episode09/devicestoragefolder1.png
   :alt: Folder structure of DeviceStorage
   :height: 150px

.. index:: Device Storage

Firefox OS Device Storage
-------------------------

The Device Storage API in Firefox OS is used to access the file system. Until now
we had seen how to use persistence in our application via the LocalStorage and
IndexedDB APIs and when you use those APIs you are pretty much dealing with
storage but at a high level. You are not dealing with individual files and
directories and reading/writing to them.

The Device Storage API is just about that. Reading and writing to files
directly. The Firefox OS exposes 5 storage areas that you can read and write
from. They are:

* Apps
* SD card
* Videos
* Music
* Pictures

The first storage area i.e. apps is available for certified applications only.
This area is used by applications to read/write their data. We shall not
attempt to do anything with that in this episode.

The other four areas stand for pretty much what their names indicate and you
can read and write to any of the areas.

The API is fairly simple. All you need to do is invoke the
``navigator.getDeviceStorage()`` method and pass the storage area name to it, such as
"SD card", "music", "pictures", and "videos", which will return a
DeviceStorage Object that you can use to access the storage area. 

You have methods that let you browse/list down the contents of the storage
area (which we shall see in the code later on). There are methods available for
additional stuff too like:

* Determining free space
* Writing/creating a file
* Deleting a file

Keep in mind that access to the storage area is a privileged operation and
hence you must define the type of your application as privileged in the
``manifest.webapp`` file that we shall see in the next section. Additionally, you
will have to specify the permissions (read or readwrite) for each of the
storage areas that you wish to access in your application.

DeviceStorage Application
-------------------------

OK, let's get going with understanding the code and how we can access Device Storage in our application.

DeviceStorage Application - ``manifest.webapp``
-----------------------------------------------

The first thing we should discuss is the manifest file. This should be familiar
now and it has the standard attributes like name, version, etc. There is
nothing special happening here.

The permissions are the most important over here. To access the storage areas
in Firefox OS, your application needs to be a privileged application, so we are
defining that in the ``“type”: “privileged”`` entry.

Next we need to mention what permissions our application needs. You have to
create an entry for as many storage areas that you want to access in your
application and also the type of access that you want i.e. read or readwrite.
The readwrite mode will be needed if you plan to create/modify files.

You will notice in the manifest that we have provided readwrite permissions for
each of the 4 storage areas (SD card, videos, music, and pictures).

.. literalinclude:: _static/episode09/manifest.webapp
   :language: json
   :linenos:

DeviceStorage Application - ``index.html``
------------------------------------------

Next up is the ``index.html`` page and it is a simple jQuery Mobile page.

.. literalinclude:: _static/episode09/index.html
   :language: html
   :linenos:

Let us discuss the ``index.html`` page in detail now:

* We have included the script in ``app.js`` file on **line 11**.
* There is only 1 page in the mobile app as seen on **line 17**.
* This page has several buttons, which when clicked will display the contents of that storage area in the ``#results`` div shown on **line 24-27**.

DeviceStorage Application - ``app.js``
--------------------------------------

.. literalinclude:: _static/episode09/app.js
   :language: javascript
   :linenos:

Let us discuss the source code in detail now.

* First, the standard stuff. On **line 25**, the standard jQuery ready function is fired and you will find various click handlers defined for the 4 buttons.
* Each of the button click handlers invokes the same function listContents, which is provided a single parameter i.e. the name of the storage area to browser/list the contents.
* The listContents function is defined on **line 1**.
* Pay attention to **line 4**, where we make a call to the navigator.getDeviceStorage (storagename) method. This is a standard call and we can pass either sdcard, videos, music or pictures to it. The return value is a object that we can use to enumerate the different files present over there.
* We use a cursor that we will use to enumerate through all the files. This cursor instance is obtained via the ``enumerate()`` function on the files object.
* If the cursor is obtained successfully, the onsuccess callback method is invoked. In this method, we simply go through each result. Remember the ``this.result`` gives a File object that has attributes like name, ``lastModifiedDate``, type and size.
* So we are simply creating an image element and other meta data information element and appending it to the ``#results`` element in the page.
* If the file object is ``null``, it means that there are no more elements and we can mark the done attribute as true. If it is not done, then we move to the next record via the continue method.

Local Installation and Testing
------------------------------

This completes our discussion of writing Firefox OS applications that access
Device Storage. Now comes the part of seeing it actually work. All we need to
test out this application is:

* You should have installed the Firefox OS simulator add-on in your Firefox Browser.  
* A working internet connection from your machine.  
* You should have downloaded/written the application as described above. We will assume that the ``index.html`` and ``manifest.webapp`` file are present in a folder names ``DeviceStorage``. You should navigate to your own directory structure when called to later.

Steps to install the application in your Firefox OS Simulator should be
familiar to you now. Simply launch the Firefox OS Simulator in your Firefox
Browser. From the Dashboard, click on the "Add Directory" button and point it to
the ``manifest.webapp`` file for the DeviceStorage application. On successful
validation, the application will be installed and it will come up in your OS
Simulator. For sample screenshots, refer to the section at the beginning of
this blog post for Episode 9 in Action. 

Next Steps
----------

Device Storage is a key feature that you can use to write your own files and
also create applications like File Explorer, etc. It can also be used to have a
better control in imaging applications, where you want to access all the files
present, and read/write to them via your own code. I recommend going through the
official documentation on Device Storage and try to create a file, etc.

Keep in mind not to use the Device Storage as a general mechanism for
persistence. If you want to build application level persistence in your
application, it is recommended that you still use LocalStorage and/or IndexedDB
API.

Until the next episode, stay tuned and give me feedback.
