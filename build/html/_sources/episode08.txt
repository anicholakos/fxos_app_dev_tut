.. Copyright (C) Romin Irani. Permission is granted to copy, distribute
   and/or modify this document under the terms of the Creative Commons
   Attribution-ShareAlike 4.0 International Public License.


.. _web_activities:

Episode 8: Using Web Activities 
===============================

Welcome to Episode 8 of the Firefox OS App Development Tutorial. In the
previous episode, we looked at how you could add persistence to your
application via the IndexedDB API. In this episode, we shall use Web
Activities, which as the official documentation states is a "way for
applications to delegate an activity to another (usually user-chosen)
application". 

Think of a simple example like making a phone call. This is standard
functionality that is available on the phone as part of the dialer application.
Now, let's say that you have a FirefoxOS Mobile App that at some point needs to
make a phone call. There is no need for you to understand the Telephony API and
try out some hair-raising stunts like that.

You know at the back of your mind that there is already a Dialer application
available. Similarly, if you need to send a SMS or an Email or even look up
Contacts, these applications are already present. So why not just launch those
in-built applications via a standard mechanism and offload the heavy-duty part
of the functionality to an application that already knows how to do it well.
That is the fundamental premise of a Web Activity.

You want to make a phone call in your application, just launch the Phone App.
You want to send a SMS in your application, just launch the SMS App. As simple
as that.


Prerequisites
-------------

* You have setup your machine with the Firefox OS Simulator.  If not you can
  check out :ref:`dev_setup`, which takes you through the entire setup.
* You have a basic understanding of writing Firefox OS Apps.  If not, I
  strongly suggest refering to earlier episodes, especially :ref:`hello_world`,
  that covers how to write your first Firefox OS App.


What this Episode covers 
------------------------

* `Web Activities
  <https://developer.mozilla.org/en-US/docs/Web/API/Web_Activities>`__ and why
  they are important.
* Sample Firefox OS Application that covers using various Web Activities.


Episode 8 in Action
-------------------

Let us check out the application in action first to understand what we are
trying to do here.

What we shall write is a mobile application that will allow us to invoke some
standard Web Activities like making a phone call, sending SMS, opening a web
page and so on. The intent in this episode is just to show you that these web
activities exist and that you should try to employ as many of them in your
application.

Note that the example screenshots are from the Firefox OS Simulator running
locally. Keep in mind that not all Web Activities can be run from the
Simulator. Try them in the Simulator but these activities are best executed on
the actual device. 

I am not lucky enough to have a device, so certain Web Activities do not work
for me and I will tell you those points as we move along. So you can use your
Firefox OS Simulator to run the example here but they will not function well
though it will clearly demonstrate what is possible. 

All right then, the first screen of the mobile app is shown below:

.. image:: illustrations/episode08/web_activities1.png
   :alt: WebActivities app on launch 
   :height: 350px

The screen shows a simple page with several buttons. Each of these buttons when
clicked will launch a specific Web Activity. It should be clear from the button
labels about what they will be doing.

When you click on the ``Call`` button, it will launch the Phone app on Firefox
OS.  Notice that we can provide the phone number to dial, so that will save the
hassle for the user to enter the number.

.. image:: illustrations/episode08/web_activities2.png
   :alt: WebActivities phone app 
   :height: 350px

When you click the ``SMS`` button, it will launch the Messaging App on the
device as shown below. Very useful activity where you can populate the number,
message, etc.

.. image:: illustrations/episode08/web_activities3.png
   :alt: WebActivities messaging app 
   :height: 350px

The button ``Pick Image`` is interesting. It shows the real power of Web
Activities where you will be presented with one or more options that can be
chosen to complete your Activity. So when we click on Pick Image, the Firefox
OS understands the activity pick and shows you automatically the 2 applications
that could fulfill your activity request. You can then select from them.

Neither of these activities work on the simulator, but rest assured they do
work on a real device.

.. image:: illustrations/episode08/web_activities4.png
   :alt: WebActivities pick image app 
   :height: 350px

When you click on ``View Web Page``, we launch the Web Activity that will
display a web page. Shown below is the display of my web site (Sorry for the
marketing plug :-)).

.. image:: illustrations/episode08/web_activities5.png
   :alt: WebActivities web activity app 
   :height: 350px

Finally, when you click the ``Open Phone Settings`` button, it will open up the
standard Settings page on your device. Think of an application which requires
Internet access and if you determine that the connection is not present, you
could ask the user if he/she would like to navigate to the ``Settings`` page
and turn on the Wi-Fi for example. If yes, you could use this Web Activity to
launch the Settings page as shown below:

.. image:: illustrations/episode08/web_activities6.png
   :alt: WebActivities phone setting app 
   :height: 350px

Let’s get going with the code. 


Download Full Source Code – Episode 8
-------------------------------------

I suggest that you begin with a full download of the project source code. Since
the project depends on libraries like jQuery and jQuery Mobile, it will save
you the hassle of downloading the dependent libraries.

Go ahead & download the code from: https://github.com/anicholakos/WebActivities

Extract all the code in some directory. You should see a directory structure
inside of ``WebActivities``, that looks something like this:

.. image:: illustrations/episode08/web_activities_files.png
   :alt: WebActivities files 
   :height: 320px


Firefox OS Web Activities
-------------------------

Before we discuss what are Firefox OS Activities, it is good to reiterate what
was mentioned in the beginning of this episode.

* Most of our Smartphones come with a variety of Applications. Some of these
  applications are available when you purchase the phone itself such as dialer,
  messaging, email, camera, and contacts. And over time you install a variety
  of other applications too that suit your needs.
* If you have written or used a variety of applications, you would have noticed 
  that at times some core functionality or actions taken by these application
  are the same.
* Some of the common actions in an application could include making a call,
  sending a SMS, sending an email, launching a camera to take a picture,
  viewing an external web application and so on.
* So typically, when you want to do these common actions in your application,
  then as a developer you have 2 options in front of you:
  
    * Write from scratch.
    * Use existing applications that can do the work.

* The first option (which all developers love at their back of their mind) is
  to write that functionality from scratch. While this is laudable, it is not
  practical. It is not practical for a number of important reasons: 

    * It will take you time to understand and write that functionality. For
      e.g. understanding the Telephony API is not a joke. At times the
      functionality may not be exposed at all.
    * Time to market is short and would you rather focus your time to build a
      great application or focus on plumbing efforts that are actually already
      present and are done in a standard and better fashion by other
      applications.
    * Users also play an important role. They are using your application day in 
      and day out. They are used to standard UX present in the Dialer App,
      Email App, SMS App and so on. So when you present your own UX to do the
      above functions, chances are that they will be lost and might even end up
      not using your application. A lot of time and effort has gone into
      creating these standard applications, so why not use them in your
      application.

* The second option and which is the focus of this episode is to use existing
  Applications that can do that work.

Hopefully you are convinced with the arguments that I have made now :-) and
want to get going with Web Activities and hopefully the official definition
that I have relisting below makes sense:

*Web Activities is a mechanism for applications to delegate an activity to
another (usually user-chosen) application.*

Firefox OS comes with a huge list of Web Activities that you can use today. And
while we are going to see just a few of them here, you can check out the list
and try them. For a fraction of the effort, you will augment your application
with features that your users will appreciate. 

There are several Web Activities available as per the list `here
<https://developer.mozilla.org/en-US/docs/Web/API/Web_Activities#Firefox_OS_activities>`__.  You have activities for:

* Browsing the Gallery
* Making a Phone Call, Sending SMS, Sending Email
* Opening up Contacts, Videos, Music, Gallery
* Picking an image from either Wallpaper, Gallery, Camera
* Picking up a Contact, Updating a Contact
* Recording a Video
* Share an image, email, etc

So whenever you want to launch a Web Activity, you will need to check out the
documentation because you need to provide at a high level the following:

#. What is the **name of the Activity**?
#. A set of **data filters** that will help the Activity tune itself in either
   functionality or prepopulate things for you automatically as needed.

Take the example of making a phone call. The *name* of the activity will be
*dial* and the *data filters* that we can provide are the type i.e.
(webtelephony/number) and the number that we wish to dial.

So for each of the activity, you should lookup the docs and determine what is
the name and the data filters that you can pass.

Once you have that information, the pattern to launch the activity is the same.
You will need to create a new ``MozActivity`` object passing the name and the
data. The activity will be launched automatically on creation. The control is
then transferred out of your application to the activity. Once the activity
completes, the control can switch back to your application and you can retrieve
any information via the two callback function ``onsuccess`` and ``onerror``
functions that will have additional information.

An example from the standard documentation is shown here:

.. sourcecode:: javascript 

    var activity = new MozActivity({
        // Ask for the "pick" activity
        name: "pick",

        // Provide the data required by the filters of the activity
        data: {
            type: "image/jpeg"
        }
    });

    activity.onsuccess = function() {
        var picture = this.result;
        console.log("A picture has been retrieved");
    };

    activity.onerror = function() {
        console.log(this.error);
    };

You will see that we launched the ``pick`` activity. And if the Activity
completed succesfully i.e. the user selected an image, then the ``onsuccess``
method will get called and you can retrieve the object that was selected via
the ``this.result`` value. Similarly, if there was some problem, the
``onerror`` method will be invoked. Simple isn’t it? 

Keep in mind that Web Activities are also known an **Intents**. These concepts
are present in other Smartphone OS like Android, Windows Phone and iOS. They
may refer to them with different names, but the concept is the same. So if you
have programmed in the other environments, this should be simple to understand.

OK. Lets get going with understanding the code and how we can use Web
Activities in our application.


WebActivities Application - ``manifest.webapp``
-----------------------------------------------

The first thing we should discuss is the manifest file. This should be familiar
now and it has the standard attributes like name, version, etc. There is
nothing special happening here.

.. literalinclude:: _static/episode08/manifest.webapp
   :language: javascript 
   :linenos:


WebActivities Application - ``index.html``
------------------------------------------

Next up is the ``index.html`` page, and it is a simple jQuery Mobile page.

.. literalinclude:: _static/episode08/index.html
   :language: html
   :linenos:

Let us discuss the index.html page in detail now:

* We have included the script in the ``app.js`` file on **line 11**.
* There are 2 pages in the mobile application: ``home`` beginning on
  **line 17** and ``showImage`` starting on **line 34**
* The ``#index`` page has several buttons for launching the various Activities
  (Intents). They are defined on **lines 24-28**.
* The ``showImage`` page in the mobile application will be used to display the
  image that we selected in the pick activity.


WebActivities Application - ``app.js``
--------------------------------------

.. literalinclude:: _static/episode08/app.js
   :language: javascript 
   :linenos:

Let us discuss the source code in detail now. The code should be
straightforward because invoking the Web Activity is always the same pattern.
You need to give a name, some data filters and optional trap the ``onsuccess``
and ``onerror`` callback methods.

* First, the standard stuff. On **line 1**, the standard jQuery ``ready``
  function is fired and you will find various click handlers defined to invoke
  the respective activities.
* **Line 4**: The ``#btnCall`` click invokes the dial Activity. Notice that we
  can pass the number that needs to be dialed. Keep in mind that the phone
  number will not get dialed automatically. The number will be pre-populated
  with whatever you provide in the ``data.number`` attribute.
* **Line 12**: The ``#btnSMS`` click invokes the new activity. The key here is
  to provide the ``data.type`` to indicate what it is that we want to create a
  new instance of and we provide the value websms/sms. Alternately, we can also
  provide the number to send the SMS to.
* **Line 21**: The ``#btnPickImage`` click invokes the pick Activity. We
  also provide the type of content to pick and specify the image type filters
  in there. Notice that we have the onsuccess and onerror callback methods
  defined here. The onsuccess method creates an HTML <image> element and builds
  the src for the image from ``window.URL.createObjectURL(this.result.blob)``
  call.  Notice that the blob was returned to us in the result of the callback.
  The ``onerror`` method is standard and we are simply displaying some message.
* **Line 46**: The ``#btnViewWebPage`` click invokes the view Activity. And we
  provide what is the type of content that we want to view and pass the
  additional information (url) to view. 
* **Line 51**: The ``#btnOpenSettings`` click invokes the configure Activity.
  And we provide what is the target that we wish to configure i.e. device. This
  will open up the Device Settings page.


Registering your App as an Activity Handler
-------------------------------------------

Another interesting aspect of Web Activities is that, while this episode covers
how to invoke web activities, you can also make your application available as
candidate to perform a specific activity. For e.g. assume that you write a
great Image Processing application which allows you to manipulate/tweak images
before sharing them. You can make your application as a candidate for the Pick
Activity too and then when any other application tries to launch the Pick
Activity, your application will also come up in the list that is shown to the
user.

*The important thing here is that your application will need to be installed on
the device for it to come up. Firefox OS will do the rest of making sure your
application will come up in the list. All you have to do is define in the
manifest.*

Check out
https://developer.mozilla.org/en-US/docs/WebAPI/Web_Activities#Registering_an_App_as_an_activity_handler
for more information.


Next Steps
----------

Web Activities are a key building block of any mobile application, whether it
is a Firefox OS app or any other mobile OS. Familiarize yourself with the
`various Web Activities
<https://developer.mozilla.org/en-US/docs/Web/API/Web_Activities#Firefox_OS_activities>`__
that are already present in the Firefox OS. I am pretty sure that you will
identify a use for them depending on the functionality of your application. And
remember, it is better to reuse existing functionality, then trying to create
your own. Your users will also be happy with your application for allowing them
to access functionality and UX that they are familiar with.
