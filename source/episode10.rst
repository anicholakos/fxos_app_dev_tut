.. Copyright (C) Romin Irani. Permission is granted to copy, distribute
   and/or modify this document under the terms of the Creative Commons
   Attribution-ShareAlike 4.0 International Public License.


.. _mbaas:

Episode 10: Using mBaaS
=======================

Welcome to Episode 10 of the Firefox OS App Developent Tutorial. In the
previous episode, we looked at how you can use Device Storage in your Firefox
OS Applications.

In this episode, we shall be taking a look at an emerging area known as Mobile
Backend as a Service (mBaaS). The idea is that we shall be powered our Firefox
OS Application from Server side infrastructure and functionality that shall be
provided by the mBaaS Provider.

What we shall be doing is writing an application that displays random quotes. The
quotes are those said by famous men over the years. For example, Albert Einstein
said "The difference between stupidity and genius, is that genius has its
limits" and so on. All these quotes will not be present locally in the Firefox
OS App local storage or database, it will in fact be delivered from a
server-side solution. This will make it easier for us to update the
server-side with data whenever we want.


Prerequisites
-------------

* You have setup your machine with the Firefox OS Simulator.  If not you can
  check out :ref:`dev_setup`, which takes you through the entire setup.
* You have a basic understanding of writing Firefox OS Apps.  If not, I
  strongly suggest refering to earlier episodes, especially :ref:`hello_world`,
  that covers how to write your first Firefox OS App.


What this Episode covers 
------------------------

* What is `mBaaS
  <http://en.wikipedia.org/wiki/Mobile_Backend_as_a_service>`__, and
  why it is important. Discussion of
  `Kinvey <http://www.kinvey.com>`__, a leading mBaaS provider.
* Sample Firefox OS Application that demonstrates integration with
  Kinvey. In other words, our Firefox OS application will be powered by Kinvey. 


Episode 10 in Action
--------------------

Let us check out the application in action first to understand what we are
trying to do here.

What we shall write is a mobile application that shows us quotes made by famous
personalities. We shall first show the list of famous personalities. When you
select a personality, the quotes attributed to that person will be shown.

All right then, the first screen of the mobile app is shown below:

.. image:: illustrations/episode10/quotes1.png
   :alt: Quotes activity app on launch 
   :height: 350px

This screen shows the list of personalities. When you click on a particular
personality, say "Albert Einstein", you will see the quotes attributed to him
as shown in the screen below.

.. image:: illustrations/episode10/quotes2.png
   :alt: Quotes activity app with Einstein 
   :height: 350px

Keep in mind that all data for this application is accessed from a hosted
application that is running on the mBaaS provider Kinvey’s infrastructure. We
shall see how easy it is to focus on your mobile functionality and let the
heavy duty work of a server-side solution be taken care of by a provider, like
Kinvey.

The data is limited here, since I have just pulled some quotes from the `Brainy
Quote <http://www.brainyquote.com/>`__  site and populated my data store that is hosted on Kinvey.


Download Full Source Code – Episode 10
--------------------------------------

I suggest that you begin with a full download of the project source code.
Since the project depends on libraries like jQuery and jQuery Mobile, it will
save you the hassle of downloading the dependent libraries.

Go ahead & download the code from: https://github.com/anicholakos/Quotes

Extract all the code in some directory. You should see a directory structure
inside of ``Quotes``, that looks something like this:

.. image:: illustrations/episode10/quotes_files.png
   :alt: Files in Quotes activity
   :height: 350px

.. index:: mBaaS

What is a mBaaS?
----------------

mBaaS stands for `Mobile Backend as a Service
<http://en.wikipedia.org/wiki/Mobile_Backend_as_a_service>`__. To give it some
context, one of the challenges that most mobile developers face is that sooner
or later, they have to create a common server side solution which can contain
functionality for managing users, storing data in the cloud, etc.

For example, consider that you are writing a mobile game and you wish to track
high scores across your different users. In such a scenario, you have no option
but to centrally store user scores in some sort of a server side solution. This
is all and good but what this means is that you have to now learn a server side
stack and figure out how to write server side applications, host them and
manage them. This takes away your focus as a mobile developer but it is not a
problem that you can wish away. Typically there will be folks who are good on
the server-side and they can help you with the development, but if you are
familiar with only the mobile client side of things, chances are that you want
to get quickly started with it, almost no coding and still get the best of
breed server side solution.

This is exactly the spot that mBaaS providers want to address. They want you to
focus on your application and its common needs on the server-side like users,
authentication, data store to store application data and many such common
services. All access of these services will be provided via a REST API or
individual client side libraries that will wrap the calls and make it as easy
as possible for you to invoke the operations in the cloud. The mBaaS providers
also take the pain of hosting and running your site, so that is a great plus.

There are many such mBaaS players out there and in this episode, we shall take
a look at `Kinvey <http://www.kinvey.com/>`__ that I was able to get up and
running for the Random Quotes application within literally minutes. It is not a
stretch of my imagination but I got things up within an hour of signing up and
that included accessing my data via the JavaScript wrapper that Kinvey
provides.

So to summarize, what we want to do with Kinvey at a high level is as follows:

#. Create an application. Let's call it quotes.
#. Use the Kinvey datastore to create 2 collections: ``Authors`` and
   ``Quotes``. The authors will contain the names of the famous personalities
   and the quotes will contain 2 key attributes, who said it and what they
   said. The who will be the Author name from the Authors collection.
#. Load some sample data into the Kinvey Collections: Authors and Quotes.
#. Use the *Kinvey JavaScript Wrapper Library* to initialize the connection to
   our Server side and access the data via the Kinvey ``DataStore`` Object.

The rest of the stuff is simply your client side functionality.

*One point to note is that we have used Kinvey in the article but if you try out
other mBaaS solutions, you will find that at a conceptual level, they do the
same thing. I am not going to pit one against the other and which one is
better. I just haven’t done detailed research to give that kind of data here. I
signed up for Kinvey, found its process super smooth and everything worked as
documented. That is what I look for in any external public facing API. The rest
is just your functionality.* 


Setup Quotes on Kinvey
----------------------

In this section, I am going to do my best to explain to you the process of
signing up for Kinvey, creating your application and all the steps that we
identified in the previous section.

The first thing you will need to do is sign up for Kinvey. Visit
https://console.kinvey.com/ and sign up for the Service. You can go with the
free plan for the moment.

Once you have signed up successfully, you will be asked to create a new
application. Go ahead and create it. My new application screen is shown below
and you can use the same thing.

.. image:: illustrations/episode10/kinvey1.png
   :alt: Kinvey New App
   :height: 200px

FirefoxOS-mBaaS Application – ``manifest.webapp``
-------------------------------------------------

The first thing we should discuss is the manifest file. This should be familiar
by now, it has the standard attributes like name and version. There is
nothing special happening here.

Note that since we will be making external calls to the Kinvey service, we need
to mark the application as privileged and also to mention the permission for
systemXHR so that outbound HTTP calls are permitted.

.. literalinclude:: _static/episode10/manifest.webapp
   :language: javascript 
   :linenos:


FirefoxOS-mBaaS Application – ``index.html``
--------------------------------------------

Next up is the ``index.html`` page, which is just a simple jQuery Mobile page.

.. literalinclude:: _static/episode10/index.html
   :language: html
   :linenos:

Let us discuss the ``index.html`` page in detail now:

* Since we will be invoking the Kinvey JavaScript Wrapper, we are including the 
  reference to the Kinvey JS file on **line 11**.
* Next, we have included the our application script: ``app.js`` file on
  **line 12**.
* We have 2 pages in the mobile application. The first page is ``#home``,
  beginning on **line 17**, and the second page is ``#quotespage``
  beginning on **line 32**.
* The home page will show a list of authors that will be retrieved from our
  backend service.
* A click on any author will invoke the next service to retrieve the quotations
  that the author has made and the results will be shown in ``#quoteslist``
  unordered list element (**line 40**).


FirefoxOS-mBaaS Application – ``app.js``
----------------------------------------

.. literalinclude:: _static/episode10/app.js
   :language: javascript 
   :linenos:

Let us discuss the source code in detail now.

* First thing to note is the standard jQuery Mobile ``pageinit`` function on
  **line 49**. So, when the index page is ready, we first just clear up the
  author list.
* Next, we initialize the connection to the Kinvey service by invoking the
  ``mBaasInit()`` method on **line 54**. We will come to the rest of the code
  in a while.
* The ``mBaasInit()`` method is defined on **line 4**. It is standard
  boilerplate code that Kinvey provides. Essentially, what we are doing here is
  initialization the Kinvey connection via the ``Kinvey.init()`` method.
  Notice that you will need to substitute the value of ``YOUR_APP_KEY`` and
  ``YOUR_APP_SECRET`` with the respective values for your application. Once the
  initialization is good, we invoke the ``populateAuthors()`` method on
  **line 12**.
* The ``populateAuthors()`` method is defined on **line 18**. The key thing to
  note here is the ``Kinvey.DataStore.find`` method that we are using. The
  first parameter is the name of our collection (in our case it is authors).
  The second parameter is an optional query parameter which we are not using
  here but will do later on. The query parameter can be used to filter the
  results. The 3rd parameter is the success callback function that is providing
  the result. The result is an error of all the models in the collection and
  we simply iterate through it, extract out the information, and display the
  authors in an unordered list element.
* Now, visit **line 57**, where we are enabling a click function on any of the
  list elements in the ``authorList`` unordered list. If there is a click, it
  will invoke the ``populateQuotes`` method and pass the label, which will
  contain the author name.
* The ``populateQuotes`` method is defined on **line 32**. It utilizes the
  ``Kinvey.Query`` object first and passes the criteria for filtering the
  quotes by the who attribute. The value passed is that of the author name,
  since we need to now retrieve only the quotes of that author.
* The next call should be familiar now and the ``Kinvey.DataStore.find``
  method is invoked. This time it is invoked on the quotes collection, the
  Query filter is provided, which will give only those models whose who
  attribute has the value of the Author name selected. The 3rd parameter is
  once again the success callback method, where we iterate through the Quote
  records and populate it in the quotes variable.  We then switch the page to
  the quotespage on **line 44**.
* Finally, **lines 62-69** iterate through the quotes global variable and
  populates the ``quotesList`` unordered list.

This completes our discussion of writing Firefox OS applications that are
powered by a mBaaS. Load it in the WebIDE and run it on the simulator to see
it in action.
