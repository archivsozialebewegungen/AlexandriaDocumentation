The CD exporter plugin
======================

The CD exporter plugin creates ISO images to burn on CDs or DVDs.
You select the documents you want to have on the CD and the
exporter runs a process that gathers all the documents and the
events belonging to these documents and creates a CD where
you may browse through the exported documents and events.

The CD code is an *angular.js* application that just runs in
the browser. In fact the application is completely packaged in
the `app.zip` file you find in the `cdexporter` directory.
The export just copies all the document files to the unpacked
app and creates one json file that contains all the other data
that is used by the angular app.

The code structure
------------------

`GenerationEngine`
..................

The main class is the `GenerationEngine` class. The interface of
this class is quite simple: It just has a run method that gets
an `ExportInfo` instance as parameter. This `ExportInfo` contains
all the information about which documents and events should be
exported. What it does is quite simple:

* It unzips the `app.zip` file to the CD export directory that
  is given in the configuration. The name depends on information
  given in the `ExportInfo`.
  
* It then calls the `Exporter` that writes all information of
  the selected documents and events into a dictionary variable.
  More on the `Exporter` see below.
  
* Then it writes texts that are needed also into the dictionary
  variable.
  
* Then some runners are called on the collected data. These runners
  may do anything they like with the data, but their main purpose
  is to link or copy the document data to the app directory.
  
* The dictionary then is written as json file in the app directory.

* And last but not least a CD ISO image is generated.

To do this, quite a lot of stuff needs to be injected into the
`GenerationEngine`. This is done on instantiation. The engine gets

* A messager class (there exist two different implementations, a
  `ConsoleMessenger` for console applications and a `MessageBarMessenger`
  for gui applications).

* The config class that contains information about the export directory
  or the call to `genisoimage`. Later on we will also support
  iso image generation on Windows, but that is not yet implemented.
  
* The text generator class. Currently there exists only a text generator
  for the generation of chronology images. But a further text generator
  will be implemented soon for more general purpose CDs.
  
* The runners. Currently there are the following runners:
  
  * event sorter - sorts the events chronologically
  * document sorter - sorts the documents by id
  * thumbnail provider - copies the thumbnails to the app
  * display file provider - copies the display files to the app
  * pdf file provider - copies the pdf files to the app

The `CDDataAssembler`
.....................

This class gets a lot of DAOs injected - as may be expected because
it needs all the information from the database. Currently this class
has just one ugly and much to long method called `export()` that gets
an `ExportInfo` as parameter (and an empty dictionary object that is
filled during the export process).