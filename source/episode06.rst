.. Copyright (C) Romin Irani. Permission is granted to copy, distribute
   and/or modify this document under the terms of the Creative Commons
   Attribution-ShareAlike 4.0 International Public License.


.. _storage:

Enabling Storage
================

Welcome to Episode 6 of the Firefox OS App Development Tutorial. This episode
will take you through the steps for writing Firefox OS Apps that add
persistence capability to your application. What this means is that you can use
a local database of sorts to save your data and the data will be available not
just across the application but also across application restarts.

Prerequisites
-------------

* You have setup your machine with the Firefox OS simulator.  If not you can
  check out :ref:`dev_setup`, which takes you through the entire setup.
* You have installed the ``zipcodeapp`` that we covered in :ref:`hello_world`.
  This tutorial uses that as an example, but if you have any other application
  installed in the Firefox WebIDE, then you should be able to follow along.

What this Episode covers 
------------------------

* What is the HTML5 LocalStorage API.
* Sample Firefox OS Application that covers using HTML5 LocalStorage API.

Episode 6 in Action
-------------------

Let us check out the application in action first. This will help you understand
what we shall be achieving by the end of this episode.

What we shall write is a mobile application that will allow us to save some
quick notes. Each note will have a title and some details. These notes will be
saved in the Local Storage of the device and then we can view all our notes
too.

All right then, the first screen of the mobile app is shown below:

.. image:: illustrations/episode06/save_notes1.png
   :alt: SaveNotes app on launch 
   :height: 350px

When we click on the ``Add a Note`` button, we get a screen as shown below
where we can enter the title and details for the new note. Once we are done, we
can save the note by clicking on the ``Save Note`` button.

.. image:: illustrations/episode06/save_notes2.png
   :alt: SaveNotes app with first note being edited
   :height: 350px

If the note is successfully saved, it will display a message as shown below:

.. image:: illustrations/episode06/save_notes3.png
   :alt: SaveNotes app with first note saved 
   :height: 350px

To view all the notes, you need to click on the ``View Notes`` button from the
main screen. This will retrieve all the notes from the Local Storage and
display them to you in a collapsible list form.

.. image:: illustrations/episode06/save_notes4.png
   :alt: SaveNotes app with list of notes 
   :height: 350px

You can click on any of the ``+`` signs and it will expand to show you the note 
details as shown below:

.. image:: illustrations/episode06/save_notes5.png
   :alt: SaveNotes app with list with expanded 2nd note 
   :height: 350px

If you wish to delete all the notes, there is also a ``Clear`` button. This
will delete permanently all the notes from the Local Storage.

Let’s get going with the code. Note that the example screenshots are from the
Firefox OS simulator running locally. So you can use your Firefox OS Simulator
to run all the examples.

Download Full Source Code – Episode 6
-------------------------------------

I suggest that you begin with a full download of the project source code.
Since the project depends on libraries like jQuery and jQuery Mobile, it will
save you the hassle of downloading the dependent libraries.

Go ahead & download the code from: https://github.com/anicholakos/SaveNotes

Extract all the code in some directory. You should see a directory structure
inside of ``SaveNotes`` that looks something like this:

.. image:: illustrations/episode06/save_notes_files.png
   :alt: SaveNotes app directory contents 
   :height: 350px

HTML5 Storage APIs
------------------

The HTML5 Storage API is a JavaScript API that will allow your application to
store data and retain that data even across browser restarts. 

As a first step to make web applications work offline, we need a storage API
that allows the application to store application specific data and use it as
needed. Here are some examples of how you could use storage:

* Games: You need to store the current moves in an application, so that if the
  user were to come back later to the application, it would replay back the
  moves. This also allows applications to store a user's highscores in a game.
* Business Apps: How about saving information locally? For example, taking offline orders, writing notes locally, and looking them up later.

You will easily be able to think of more use cases.

The Storage API also known as the Local Storage API provides a key based
storage with a simple API to go along with it. The amount of storage size
allocated per domain is anywhere from 5MB-10MB, which should be sufficient for
most applications. There is also another API available for storage (IndexedDB),
which provides a more sophisticated database like structure a la SQL, but we
shall keep the discussion limited now to simple storage support in the form of
key-based values.

Few things to do about the Storage API:

* The Local Storage Object that is provided to you is available under
  ``window.localStorage``, but for all purposes, you can use as
  ``localStorage``. All the methods for saving, removing, etc will be invoked
  in a format that looks like ``localStorage.<some-method>``.
* There are just 6 methods that you need to be familiar with to understand the
  localStorage API. If you have worked with maps or key-value data structures
  before in any language, it should be familiar.
* The methods are:
    * ``getItem(<key>)``: Returns back the value for the key.
    * ``setItem(<key>,<value>)``: Sets a value (Creates or overwrites) for a
      key.
    * ``removeItem(<key>)``: Deletes the entry for that particular key.
    * ``clear()``: Removes all the entries in the Local Storage.
    * ``length``: Returns a count of the number of keys in the local storage.
    * ``key(<index>)``: returns the value for a key as per the index. The
      index ranges from 0 to (length-1).

SaveNotes Application
---------------------

OK. Let's get going with understand the code and how the Local Storage API has
been used to persist (save) notes in our application.

SaveNotes Application - ``manifest.webapp``
-------------------------------------------

The first thing we should discuss is the manifest file. This should be familiar
now and it has the standard attributes like name and version. There is
nothing special happening here.

.. literalinclude:: _static/episode06/manifest.webapp
   :language: javascript 
   :linenos:

SaveNotes Application - ``index.html``
--------------------------------------

Next up is the ``index.html`` page and it is a simple jQuery Mobile page.

.. literalinclude:: _static/episode06/index.html
   :language: html 
   :linenos:

Let us discuss the ``index.html`` page in detail now:

* We have included the script in the ``app.js file`` on **Line 11**.
* There are 3 pages in the mobile application:
    * ``home`` (**Lines 17-28**).
    * ``add-notes`` (**Lines 31-44**).
    * ``view-notes`` (**Lines 47-59**).
* The ``#home`` page has two buttons for ``Add a Note`` and ``View Notes``. 
* The ``#add-notes`` page has a form for entering the title (``#noteTile``),
  details (``#noteDetails``) and two buttons for saving the note
  (``#btnSaveNote``) and clearing the fields (``#btnClearNotes``).
* The ``#view-notes`` page has a button in the header to clear all notes
  (``#clearAllNotesBtn``) and it has a div area (``#note-list``) to display all
  current notes, once we get them from the Local Storage.

SaveNotes Application - ``app.js``
----------------------------------

.. literalinclude:: _static/episode06/app.js
   :language: javascript 
   :linenos:

Let us discuss the source code in detail now since it contains the HTML5
LocalStorage JavaScript API:

* **Lines 2-5** indicate that when we click on the ``Add a Note`` button in the
  ``home`` page, it will navigate to the ``add-notes`` page. This is
  jQuery Mobile stuff.
* Let us first focus on adding or saving the note, **lines 27-39**. Here you
  will notice that we first extract out the values that the user has entered
  for ``title`` and ``detail``. Then on **line 32** we check if the
  ``localStorage`` object is available i.e. if support for it is there. If yes,
  we simply invoke the ``localStorage.setItem(<key>, <value>)`` method. This
  will save the key-value in the local storage. Note that we are using the
  title of the note as the key. So if you enter the same title again but with
  different details, it will overwrite the value because the key is the same.
  So keep in mind, that the ``setItem`` method functions as both an ``Add`` and
  an ``Update``.  That is all there is to save the item. Note that we have
  saved a simple string value here, but you could put entire JSON Objects over
  here (Object, Array) and read back the values.
* Now let us focus on **lines 7-24**.  **Lines 7-9** indicate that when we
  press the ``View Notes`` button on the ``home`` page, it will navigate to the
  ``view-notes`` page.
* We clear the content i.e. any existing items that were present on the page
  by clearing the html content for ``#note-list`` on **line 11**.
* Then we iterate through the ``localStorage`` keys one by one, retrieve the
  values and form the jQuery Mobile collapsible div for each note entry. Note
  that this loop (**lines 13-22**) makes use of 3 additional methods of
  localStorage:
     * We first use the ``localStorage.length`` method to get the total number
       of items that are present.
     * Next we get the key for each item via the ``localStorage.key(index)``
       method.
     * Finally, we use the ``localStorage.getItem(<key>)`` method to retrieve
       the details for the note. 
* For each note, we are simply creating a collapsible div for jQuery
  Mobile UI and appending it to the ``#note-list`` element.

Local Installation and Testing
------------------------------

This completes our discussion of writing Firefox OS applications that utilize
the HTML5 LocalStorage API. Now comes the part of seeing it actually work. All
we need to test out this application is:

#. You should have installed the Firefox OS Simulator into the WebIDE.
#. You should have downloaded/written the application as described above. We
   will assume that the ``index.html`` and ``manifest.webapp`` files are
   present in a directory named ``SaveNotes``. You should navigate to your own
   directory structure when called to later.

Debugging Local Storage
-----------------------

If you recollect Episode 3 : Working with the OS Simulator, we had covered how to start the WebIDE and run an application
in the FirefoxOS simulator and debug the execution.

You can debug the current state of the localStorage object in the same manner. 
To try that out, go to the WebIDE in your Firefox browser and start the 
SaveNotes application. Once connected to the running instance of the SaveNotes application, 
click the wrench and the console window will be visible.

   .. image:: illustrations/episode06/debugging1.png
      :alt: Developer console
      :height: 150px

In the developer console, go ahead and type localStorage as shown above and hit Enter. 
This will show the current state of the localStorage object as shown below. For example, I had a few notes saved 
and hence you see that list. I suggest you give it a try by inspecting localStorage without any items, then 
add a few items and then inspect the value again. You will be able to see the list of items that you have added.

   .. image:: illustrations/episode06/debugging2.png
      :alt: Developer console object 
      :height: 150px

You can even try out the different methods of the localStorage API. For example, in the screenshot below, 
I have tried out localStorage.length. That returns me a value of 2, since I have 2 notes saved. Similarly, 
I have also tried out localStorage.key(0) that returns me the value of the key at index 0.

   .. image:: illustrations/episode06/debugging3.png
      :alt:  Developer Console localStorage API
      :height: 150px

Next Steps
----------

I encourage you to enhance the current application by giving the ability to delete individual notes. 
You could even opt for a more array-like representation of the data that you put in ``localStorage``. 
Consider adding a search feature on the View Notes screen. There are lots of opportunities for improvement!

If you would like to learn more about local storage, I strongly suggest for you for try out the following tutorial: `Dive Into HTML5: Storage <http://diveintohtml5.info/storage.html>`__.

Alternatively, you can also look at a couple of my blog posts: `Storage 101 <http://rominirani.com/html5-recipes-storage-101/>`__ and `More on Local Storage <http://rominirani.com/html5-recipes-more-on-storage/>`__.

Coming Up Next
--------------

The next episodes will either cover another HTML5 API for persistence called IndexedDB. 
This is more powerful compared to ``localStorage`` and gives you an SQL-like ability to work with your databases.
