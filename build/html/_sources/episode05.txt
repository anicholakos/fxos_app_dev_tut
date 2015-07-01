.. Copyright (C) Romin Irani. Permission is granted to copy, distribute
   and/or modify this document under the terms of the Creative Commons
   Attribution-ShareAlike 4.0 International Public License.

.. _locationlocation:

Location, Location, Location
============================

Welcome to Episode 5 of the Firefox OS App Development Tutorial. This episode
will take you through the steps for writing Firefox OS Apps that are location
aware. What this means is that your application can interact with the
`GPS receiver <http://en.wikipedia.org/wiki/GPS_navigation_device>`__
that is present on the Firefox OS Device, retrieve the current position
(`latitude <http://en.wikipedia.org/wiki/Latitude>`__, `longitude
<http://en.wikipedia.org/wiki/Longitude>`__) and thereby help you write
location based applications.

Prerequisites
-------------

* You have setup your machine with the Firefox OS Simulator.  If not you can
  check out :ref:`dev_setup`, which takes you through the entire setup.
* You have installed the ``zipcodeapp`` that we covered in :ref:`hello_world`.
  This tutorial uses that as an example, but if you have any other application
  installed in the Firefox WebIDE, then you should be able to follow along.

What this Episode covers 
------------------------

* What are Location Based Applications.
* HTML5 Geolocation API.
* Sample Firefox OS Application that covers using HTML5 Geolocation API.
* Sample Firefox OS Application that uses HTML5 Geolocation API to retrieve
  current position (latitude, longitude) and plot that on the Google Map. 

Episode 5 in Action
-------------------

Let's check out the application in action first. This will help you understand
what we shall be achieving by the end of this tutorial.

What we shall write is two mobile applications that will retrieve our current
position. On successfully getting the current position from the GPS receiver on
the Firefox OS phone, the first app will simply display the current longitude
and latitude, while the second app will plot that location on a map.

All right then, the first app, Locate Me, is shown below:

.. image:: illustrations/episode05/locateme3.png
   :alt: LocateMe app with location 
   :height: 350px

Here is the second app, Map Me:

.. image:: illustrations/episode05/mapme3.png
   :alt: MapMe app with location 
   :height: 350px

Download Full Source Code
-------------------------

I suggest that you begin with a full download of the project source code. Since
Map Me depends on jQuery, it will save you the hassle of downloading the
dependent libraries.

Go ahead & download the code from: https://github.com/anicholakos/WhereIAm

You'll notice that there are two seperate folders for both applications. Go
ahead and extract all the code in some folder, on my machine the code is
present in /home/anicholakos/Projects/WhereIAm but it could be any directory of
your choice. Now when you want to load up one of the apps, just select one of
the folders in ``WhereIAm``.

.. index:: GPS

Location Based Applications
---------------------------

Smartphones come with a GPS receiver that is able to provide you with the
current position of the device. Location Based Applications are those
applications that make use of the device location to give you better localized
data. If you are searching for restaurants in an area where you are currently
taking a walk, it makes sense for the application to utilize the current
location via the GPS and provide you with restaurants that within a certain
radius around you. Location is here to stay and the ability of an application
to know the users location is key.

HTML5 Geolocation API
---------------------

HTML5 has support for determining the location of the user via the `Geolocation
API <http://diveintohtml5.info/geolocation.html>`__. It supports both a one
time location retrieval and also continuous location retrieval depending on the
need of your application. Geolocation support is determined via the
navigator.geolocation object in the browser. Firefox OS supports the HTML5
Geolocation support and you will find similar support in most mobile browsers
and desktop browsers out there. You can check out the following `table
<http://caniuse.com/#feat=geolocation>`__ to see what browsers support
geolocation.

In this episode, we shall be looking at the one-time location retrieval via the
HTML5 Geolocation API. While the continous location retrieval is also
available, I suggest that you should have a good use case for the same because
you need to keep in mind that the easiest way to drain out the phone battery is
via a badly written GPS application.

Locate Me
---------

In this application, we shall first look at the HTML5 Geolocation API so that
we can familiar with its usage. In the next application, we shall cover using
that location to plot on the Google Map.

Let's check out the application running on the Firefox OS Simulator. The first
screenshot is shown below and is a simple screen with a button labeled
LocateMe.

.. image:: illustrations/episode05/locateme1.png
   :alt: LocateMe app without coordinates 
   :height: 350px

When you click on the Where am I? button, it will popup a permission screen as
shown below. This permission screen is not part of the application but is a
standard one that all browsers display to ask for permission of the user.

.. image:: illustrations/episode05/locateme2.png
   :alt: LocateMe app asking for permission for GPS
   :height: 350px

Let us spend a minute on why this is needed. Determining the location of a
device behind the scenes has privacy implications and the recommended practice
is to always ask the user for permission, also known an opt-in. HTML5
Geolocation specification makes it clear that browsers need to ask the user for
permission before determining the location. Each browser does this differently
via a bar that typically appears just under the address bar and the Firefox OS
behavior is what you are seeing below.

* If you say “Yes” to the browser asking for permission, then it goes about its
  work to determine the location.
* If you refuse permission, then the error callback method (which we shall see
  in a while) will have the value PERMISSION_DENIED. So in well-written mobile
  applications, you should be prepared for a user refusing permission to get
  the location.  
* Once you give the browser permission, it can remember that option on
  subsequent uses as you can see from the Remember my choice toggle button. You
  may also go into the Device Phone Settings and clear the permission.

OK, so we can safely click on the "Allow" button. This will use the HTML5
Geolocation API, retrieve the current latitude, longitude and display them on
the screen.

Let's now take a look at the code.

Locate Me - manifest.webapp
---------------------------

The first thing we should discuss is the manifest file. This should be familiar
now and it has the standard attributes like name, version, etc.

What is specific to our example and important is to note that we have an
additional permission for geolocation on **line 14**.

.. literalinclude:: _static/episode05/locateme/manifest.webapp
   :language: json
   :linenos:

Locate Me - index.html
----------------------

Next up is the index.html page and it contains a button ``LocateMe`` on **line
#21**. The button click handler and all relevant source code is present in
``app.js`` as referenced on **line 10**.

.. literalinclude:: _static/episode05/locateme/index.html
   :language: html
   :linenos:

Locate Me - gpsapp.js
---------------------

This file contains the code that is invoked when the button is clicked on **line
3**.

.. literalinclude:: _static/episode05/locateme/gpsapp.js
   :language: javascript
   :linenos:

Let us discuss the source code in detail now since it contains the HTML5
Geolocation JavaScript API.

* On **line 3** we have an ``EventListener`` so that when the button is clicked,
  the application checks to see if
  GPS is available which is shown on **lines 4-14**. If the browser supports
  the HTML5 Geolocation API, then the coordinates are stored, otherwise an
  error message is displayed. As mentioned before, Geolocation support in the
  browser is present via the navigator.geolocation object.
* Notice that three parameters that are provided if Geolocation is supported on
  **lines 6-10**. The first parameter is the success callback function. This
  function will be invoked when the application is able to determine the
  location. The second parameter is the error callback method. This method will
  be invoked when the browser is unable to determine the location. The third
  parameter is an object that contains a timeout value that specifies the
  amount of time to wait for location data.
* If the browser is able to determine the location, the coordinates are stored
  using the store function that is declared on **line 17**. The stored string
  contains all the information about the location that you want. We extract out
  the latitude and longitude as shown in the code and we display that in the
  element with ``id="coordinates"``. In addition to them, you can also extract
  out other attributes like altitude, speed, heading, etc.
* If your location could not be determined, the error callback method is
  invoked. In our case, it is the error handler method on **line 7**. A
  parameter of type PositionError is passed to it. You can use this
  PositionError method to determine the exact reason why it failed via the code
  and message attributes. It could have 3 values:
  ``PositionError.PERMISSION_DENIED``, ``PositionError.UNAVAILABLE``, and
  ``PositionError.TIMEOUT``. So, if you need to do some custom handling or error
  message depending on the error, you can check the value and then give a
  custom message instead of the message attribute.

Note: We can also provide a 3rd parameter that is of type PositionsOptions,
where we specify extra criteria to help guide the Geolocation API for the kind
of location data characteristics. For example, we could have passed
{enableHighAccuracy : “true”} , which tells the API to use the highest possible
accuracy. Other attributes that you could specify are timeout and maxAge. Refer
to the specification for more detail.

Map Me
------

We will now extend our code to not just display the current latitude and
longitude but to also show a map with a marker that will represent our current
location. For MapMe we use a JavaScript library called `Leaflet
<http://leafletjs.com>`__ to display the interactive map.

.. literalinclude:: _static/episode05/mapme/manifest.webapp
   :language: json
   :linenos:

Map Me - index.html
-------------------

The home page contains a simple button and instruction to invoke the HTML5
Geolocation API. When the button is pressed, a map is displayed with the user's
location. All relevant source code is present in app.js as referenced on **line
14**.

.. literalinclude:: _static/episode05/mapme/index.html
   :language: html
   :linenos:
   
Map Me - app.js
---------------

The heart of the application is in the app.js file below:

.. literalinclude:: _static/episode05/mapme/app.js
   :language: javascript
   :linenos:
   
Let us look at the code in detail. We do the following:

* On **line 4**, we call the ``setupLeafletMap()`` function.
* On **line 13**, we create a Leaflet instance bound to the element with
  ``id=map``.
* On **lines 16-19**, we point Leaflet to a server where it can find image
  tiles that show nearby streets and stitch them together to display on the
  screen.
* On **lines 22-37**, Leaflet is told what to do once the user's location is
  found. It creates a map marker and a circle which represents the accuracy of
  the displayed location. 
* On **lines 6-9**, define what should happen when the ``#locateMe`` button is
  pressed. When the button is pressed the map element is unhidden and the
  ``leafletFindLocation`` function is called. 
* **Lines 43-50** deal with the removal of any location markers that already
  exist on the map. This allows the user's location to be found multiple times.
* **Line 52** tells Leaflet to find the user's location. Leaflet will then fire
  the ``locationfound`` or ``locationerror`` event.  

Local Installation and Testing
------------------------------

This completes our discussion of writing Firefox OS applications that utilize
the HTML5 Geolocation API. Now comes the part of seeing it actually work. All
we need to test out this application is:

* You should have the Firefox OS Simulator set up in your WebIDE.
* A working internet connection from your machine.
* You should have downloaded/written the application as described above. You
  should navigate to your own directory structure when called to later.
            
Steps to install the application in your Firefox OS Simulator should be
familiar to you now. Simply go to Project and click Open Packaged App. Navigate
to the folder containing the two application and click on one of the folders to
install one of the applications. On successful validation, the application will
be installed and it will come up in your OS Simulator.  

Next Steps
----------

I encourage you to enhance the current application by using the current
location to do interesting things like retrieving nearby points of interest:
hotels, movie theatres, etc. You could even write a weather application that
detects where you are and retrieves the current weather conditions via a public
API.

Coming up Next
--------------

The next episode will take you through another HTML5 API: Local Storage. This
is a very critical API to help build persistence (saving data) in your
application. Local Storage is also one of the key techniques to enable your
application to work offline.                                 
