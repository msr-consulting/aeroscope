# Aeroscope String Pot Project
This is a small project for developing code to read a linear encoder for Aeroscope, Inc. in Broomfield, CO.  

## Encoder 
The encoder is an SE1 compact string pot from Intertechnology, Inc.  The resolution of the encoder is 56.4 pulses per inch and is used directly in the acquisition to retrieve position.  The encoder output is captured by an NI 9401 cDAQ card which sits in a NI 9781 cDAQ chassis.

## Repository Contents

The repository contains all contents of project except those files automatically generated at development time.  The repository contains two folders - ``images`` and ``src``.  The ``images`` folder contains images used for the executable icon as well as a screen shot of the user interface.  The ``src`` directory contains all of the code for this project which consists of a single VI and a project.  The project is required for building an executable and installer. 

## User Interface.

The user interface is shown in the image below.  It consists of the following elements:
* A plot of the string pot position in inches.  The position is plotted against the sample point.
* ``Acquisition Time`` - this is the time stamp of the current acquisition point and is the time that is recorded in the file.
* ``Configuration Complete?`` LED - indicates that the 9401 card has been found.
* ``dt`` - the acquisition period in ms.  This control is used to determine how often a sample point is acquired.  The default is 100 ms, or 10 Hz.
* ``File Location`` - this is the location of the file to which the data will be written to.  The file is always called ``PotData`` with a number appended and is always written to the ``c:\data`` directory.  If this directory does not exist, then it is created.
*   ``Save`` - button the user may press to save data.  The default is ``FALSE`` or not saving.
*   ``Current File Size (kB)`` - Current size of the file in kilobytes.  Everytime the application is started, a new file is opened and the header (``t   V``) is written to it so the minimum file size is 0.005 kB.
![FP](https://github.com/lo-co/aeroscope/blob/master/images/string%20pot%20fp.png)

### Configuration

At startup, the application probes the system for the correct card (9401).  If this card is not found, then the application will raise a dialog that will inform the user that the card was not found.  If it was successful, then the ``Configuration Complete?`` LED will be lit and the application will do several things:

* Rename the 9401 card ``EncoderMod`` (this is observable in MAX)
* Create a task for a counter input configured for encoder capture.
* Open a file with the name as defined above and write a header to that file.

The 9401 should be connected as follows for proper operation:

* The A lead should be connected to ``PFI0``, or pin 14
* The B lead should be connected to ``PFI2``, or pin 17
* Ground should be connected to any COM (pin 0 will do)
* The final lead should be connect to the +5V on the encoder and to PFIO7 (pin 27) on the card.  This pin will power the encoder with a high DIO signal (~4V)  

### File Writing

Files are written in ASCII format at whatever rate the user specifies via the control ``dt``.  The file is tab delimited and will contain two columns: time and position in inches.  Time is a double precision float which represents seconds since midnight 1 January 1904The lines are terminated with a carriage return.  

### Notes

* DO NOT use the first slot on the current chassis.  As of 01/2022, this pin is bent.

