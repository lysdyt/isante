

# Introduction #

Patients are always assigned a local site identifier (patientID) when initially registered. This identifier is also the ID associated with their fingerprints in the local fingerprint database and the ID that is transmitted to the national fingerprint database. For national synchronization purposes, a patient’s first local identifer becomes her master patient identifier (masterPID). The national fingerprint server (administered by UGP in Port-au-Prince) stores and supplies this master identifier to additional sites the patient may visit. Local identifiers are associated with master identifiers whenever a local fingerprint database is synchronized with the national fingerprint database. Frequency of synchronization is proposed to be daily or weekly, depending upon ongoing experience with the volume of transactions and the speed of synchronization processing.

![https://sites.google.com/site/haitioejan2012/home/jeremie.jpg](https://sites.google.com/site/haitioejan2012/home/jeremie.jpg)

# Setup #

## 1. Windows Side ##

The goal is to setup a task that will merge the local fingerprint database with the national consolidate one, retrieve the duplicate fingerprint entries and upload that list to the iSanté server for deduplication.

  1. copy the fpmerge folder and its content from ~/support/fpmerge in the iSanté code to C:\Program Files\BioPlugin\fpmerge
  1. Replace the existing curl.exe with the appropriate one for your operating system (http://curl.haxx.se/download.html)
  1. Update the "hybrid\_datamerger.ini" file according to your requirement. It contains PORT and IP of Destination BioPlugin Server. It also links to your local Database.
    1. PORT: Port of the destination BioPlugin Server
    1. IP: IP of the destination BioPlugin Server
    1. ConnectStr: ODBC Connection to the source (local) BioPlugin Database
  1. Update the "hybrid\_datamerger.cmd" file with your login information to the iSanté Server (lines 20-22)
  1. Verify the installation:
    1. Execute "hybrid\_datamerger.cmd"
    1. Consult the hybrid\_datamerger.log to see if the transaction worked or curlErrorFile.htm if it exists to debug
  1. The uploaded DuplicateTemplateFound.log should be found in to iSanteMergers\_done folder with today's date
    1. Set up a Scheduled task in Windows that will execute C:\Program Files\BioPlugin\fpmerge\hybrid\_datamerger.cmd daily.


IMPORTANT >>> In destination machine, BioPlugin Server needs to RUN. Otherwise it won't be work.

--- Files in this folder ---
Read Me First.txt: documentation
curlSendFile.cmd: secondary executable that uploads a file to the serveur via HTTP with cURL and calls the iSante side merging process
hybrid\_datamerger.cmd: main executable file that starts the fingerprint database merge process with hybrid\_datamerger.exe and then calls curlSendFile.cmd for every DuplicateTemplateFound.log in the iSanteMergers\_todo folder
hybrid\_datamerger.exe: executable file created by M2Sys that merges the local database with a destination database. It uses the hybrid\_datamerger.ini file for its configuration.
hybrid\_datamerger.ini: configuration file for hybrid\_datamerger.exe.
hybrid\_datamerger.log: log of the previous executions of hybrid\_datamerger.cmd
dataMerge.txt: file created by the hybrid\_datamerger.exe (not used in our process)
DuplicateIDFound.txt: file created by the hybrid\_datamerger.exe (not used in our process)
MergedRecords.txt: file created by the hybrid\_datamerger.exe (not used in our process)


## 2. iSanté Side ##

Everything should run without any additional setup.
The batch file that runs mergeFingerprintDate() is ~/patientStatusBatch.php
You can verify that the merger process works by monitoring the various logs created by the mergeFingerprintDate() function.

# Logs #

## Folders ##
/var/backups/itech/fpDuplicateLogs/
(accessible by php server because it is owned by www-data)

## 1) a log of the execution in /var/log/itech/ ##

Done via the ~/batch-jobs.php file

## 2) log of event in table eventLog in database ##

a record is added to the table eventLog in the database on completion of the process with function recordEvent().

## 3) Archives ##

/var/backups/itech/fpDuplicateLogs/processed
the file ~/config-linux/scripts/110var.sh has been updated accordingly with the following lines:
#this directory holds fingerprint duplicates data
mkdir -p /var/backups/itech/fpDuplicateLogs/processed
chown -R itech:www-data /var/backups/itech/fpDuplicateLogs
chmod -R 771 /var/backups/itech/fpDuplicateLogs

# Steps executed daily #

  1. Scheduled task C:\Program Files\BioPlugin\fpmerge\hybrid\_datamerger.cmd on local windows computer hosting fingerprint database.
    1. :: hybrid\_datamerger.exe FP1 Merges Local Fingerprint Database with distant Fingerprint Database defined in hybrid\_datamerger.ini
    1. It imports the DuplicateTemplateFound.log file to the current folder
    1. DuplicateTemplateFound.log is copied to the iSanteMergers\_todo folder and renamed with today’s date iSanteMergers\_todo.
    1. For each DuplicateTemplateFound.log in iSanteMergers\_todo, executes curlSendFile.cmd that uploads the file and calls the iSante server side merging function.
    1. exits
  1. The cURL script uploads a DuplicateTemplateFound.log with today’s date (eg DuplicateTemplateFound-20120120.log) and calls ~/upload.php. Function mergeFingerprintData(‘NameOfTheFile.log’); from ~/backend/materializedViews.php is executed.
    1. It output the received data from the cURL script to /var/backups/itech/fpDuplicateLogs/NameOfTheFile.log (eg /var/backups/itech/fpDuplicateLogs/DuplicateTemplateFound-20120120.log)
    1. It opens the ‘NameOfTheFile.log’ file if it exists
    1. For each duplicate
      1. it updates masterID in patient
      1. it updates lstModified in encounter
      1. it applies changeFingerprintID()
      1. it moves the NameOfTheFile.log file to /var/backups/itech/fpDuplicateLogs/processed folder and adds the day of processing (example: DuplicateTemplateFound-20120120.log)
    1. It adds an event in table eventLog and saves the duplicateIDs with function recordEvent()