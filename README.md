# Bat project

Here you find some Python scripts and other resources that were created for bat surveys as part of a fauna, flora and habitat (FFH) monitoring project by an organisation in Germany: <a href="http://bi-wollenberg.org" target="_blank"> BI&nbsp;„Rettet&nbsp;den&nbsp;Wollenberg“&nbsp;e.V.</a>. Visit the [project's homepage (German only)](http://ffh-monitor.org) for more information.<br>
<br>
Some scripts involve mass data handling of bat call recordings from the <a href="http://www.bat-pi.eu/EN/index-EN.html"  target="_blank">Bat-Pi&nbsp;</a>, a great <a href="https://www.raspberrypi.org/" target=_blank>Raspberry Pi</a> based ultrasonic bat call recorder and the transfer of ultrasonic bat sounds to <a href="http://www.wsl.ch/dienstleistungen/produkte/software/batscope/index_EN" target="_blank">BatScope&nbsp;3</a>, a free bat call analysis and classification software.<br>
<br>
Some MySQL scripts are provided as well for those whishing to transfer BatScope sequence and call data to a MySQL database server and run queries against it.<br>
<br>
Another script deals with georeferencing and handling of sonogram screenshots from the <a href="http://www.mekv.de/bat3/index.htm" target="_blank">SSF&nbsp;BAT&nbsp;3</a> hand held bat detector.<br>
<br>
If you are interested in our Arduino based devices used in the field during bat observation, you might want to visit the pages with our <a href="https://github.com/ffhmon/arduino" target="_blank">Arduino-Sketches</a>.<br>
<br>
All code is provided in the hope that it is helpful to other bat friends and nature enthousiasts. We are open for questions or feedback. Please feel free to contact me through GitHub or drop a message in <a href=mailto:ffhmonitor@gmail.com>my mailbox</a>.<br>
<br>
FVG, November 2017
<hr>

## makeBatNightDirectories.py
Script for the <a href="http://www.bat-pi.eu/EN/index-EN.html"  target="_blank">Bat-Pi&nbsp;</a> (first and second edition). It will prepare and reorganise your bat recordings for further automatic mass data processing, e.g. with SCAN'R. What it does:

<ul><li>it creates a consistent directory structure for each bat observation night
<li>bat nights are organized in bat observation sites
<li>recordings are moved preserving original file timestamps
</ul>
Please see comments in the script for more detailed information.
<hr>

## makeBatScopeXml.py
#### Georeferencing Bat-Pi recordings and prepare the transfer to bat call analyzer software
Script for the <a href="http://www.bat-pi.eu/EN/index-EN.html"  target="_blank">Bat-Pi&nbsp;</a> (first and second edition). It georeferences all wav files and creates an XML meta data file for each recording, allowing them to be transferred to <a href="http://www.wsl.ch/dienstleistungen/produkte/software/batscope/index_EN" target="_blank">BatScope&nbsp;3</a>, a bat call analyser and classification software for Mac OS X. 

What this script does:
<ul><li>it reads current Bat Pi device settings from /out/bin/recordings.sh
<li>it reads GPS track points from /out/data/gps (gpx-file or an alternative 'fixed-geo.txt') and geo references all recordings
<li>it reads logged temperatures from a /out/ENVLOG.TXT file for each recording
<li>it writes an XML file for each wav recording with device settings, GPS data and temperatures into /out/data/batscope/ 
<li>it writes a session KML file with georeferenced recordings into /out/data/reports/pi-route.kml for use with GIS software
<li>it writes a session XML with archived device settings for the current session into /out/data/reports/pi-session.xml 
<li>it writes a session CSV with archived device settings for the current session into /out/data/reports/pi-session.csv
</ul>
Please note, that the Bat-Pi normally does not log temperatures. We built our <a href="https://github.com/ffhmon/arduino" target="_blank">own environment datalogger</a> and provide an environment log file accordingly. The data format is documented in the script.

Also note that a special ImporterModule for the BatScope software is needed in order to read the XML meta data files. (See the Bat-Pi Importer below). 

## Bat-Pi Importer (BatPi1ImporterModule.py)
#### Importer module for the transfer of Bat Pi recordings into a BatScope 3 database

In order to import the <a href="http://www.bat-pi.eu/EN/index-EN.html"  target="_blank">Bat-Pi&nbsp;</a> wav files and their corresponding XML metadata, this script must reside on the BatScope Computer in the directory where BatScope's importer modules reside. This directory usually is:<br><code>/Library/Application Support/BatScope/ImporterModules</code>

In BatScope 3, the process is called 'SD card conversion'. The /out/data directory of the Bat-Pi has to be copied on to the BatScope computer and is seen by BatScope as an 'SD Card'. The sub directory <code>/out/data/batscope</code> should contain XML files with metadata for each recording. See the makeBatScopeXml.py script above for creating those XML meta data files.

As soon as the importer script is present on your BatScope computer, BatScope will offer a data converter called "Bat-Pi v1 Importer". For more information, please consult the BatScope manual on how to access the convert functionality. Look for a chapter called 'Converting and Importing Foreign Audio Data'. The manual can be found on the <a href="http://www.wsl.ch/dienstleistungen/produkte/software/batscope/index_EN" target="_blank">BatScope&nbsp;homepage</a>.

<hr>

## MySQL scripts for creating a database for BatScope data
#### create-batscope-tables.sql
The BatScope dataset consists of two tables:
<ul>
<li>2016-call-import</li>
<li>2016-sequence-import</li>
</ul>

You can build those tables from BatScope CSV exports using any good MySQL database tool or by using the script provided here. 
You also could download a full year's survey dataset from <a href="https://drive.google.com/drive/folders/0B5SuoFpMQB38LW96bzlJRHdhekU" target="_blank">our file repository on Google Drive</a>.

## MySQL scripts for selecting sequence and call data from a BatScope dataset
#### get-genus-sequences.sql
Gets a full list of sequences for a certain bat genus. Use the latin Genus name without species e.g. 'Eptesicus'.
#### get-matching-sequences.sql
Gets a list of sequences for which manual sequence classification matches automatical call classification (MostLikelySpecies field in BatScope). Use full latin species name e.g. 'Eptesicus nilssonii'.
#### get-classified-call-list.sql
Gets a list of calls from sequences with a manual sequence classification matching automatic call classification (MostLikelySpecies field) and where only one species was found in the recording (for better statistical results). Use full latin species name e.g. 'Eptesicus nilssonii'.
#### get-unclassified-call-list.sql
Gets a list of calls from sequences with a manual sequence classification on genus level only ("spec.") matching automatical call classification (MostLikelySpecies field) on a species with the genus. Use latin genus only e.g. 'Eptesicus' and 'Eptesicus spec.'

<hr>

## Python script for SSF BAT3 screenshots
#### processSSFBatScreenshots.py
Script for processing screenshot (BMP) files from a SSF BAT3 detector. It renames the original BMP files to meaningfull YYYYMMDD-HHmmss names and converts the BMP format to valid JPG files and sets EXIF data to correct picture time stamp.

Optionally the script can use a temperature / humidity data logger file and GPX files for the georeferences. 

There are dependencies on tools that run on Linux based systems only. For detailed information, please see the inline coments in the script.



