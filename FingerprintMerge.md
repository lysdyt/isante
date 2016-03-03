You will set up a task to merge the local fingerprint database with the national consolidated database, retrieve any duplicate fingerprint entries that may exist, and upload that list to the local iSanté server to aid with patient de-duplication.

HOW TO SETUP

1. Copy the ~/support/fpmerge folder and its contents from ~/support/fpmerge on the local  iSanté server to C:\Program Files\BioPlugin\fpmerge on the local Windows fingerprint server.

2. Replace the curl.exe file in C:\Program Files\BioPlugin\fpmerge with the appropriate one for your operating system (http://curl.haxx.se/download.html)

3. Update the "hybrid\_datamerger.ini" file to correspond to your local configuration. It contains PORT and IP of the destination national BioPlugin Server and the connect string for your local fingerprint database:

> - PORT: Port of the destination BioPlugin Server

> - IP: IP of the destination BioPlugin Server

> - ConnectStr: ODBC Connection to the source (local) BioPlugin Database

4. Update the "hybrid\_datamerger.cmd" file with your login information for the local iSanté Server (lines 20-22)

5. Verify the installation:

> - Execute "hybrid\_datamerger.cmd"

> - Consult the hybrid\_datamerger.log to see if the transaction worked or curlErrorFile.htm if it exists to debug

> - The uploaded DuplicateTemplateFound.log should be found in the iSanteMergers\_done folder with the current date

6.   Set up a scheduled task in Windows that will execute:

C:\Program Files\BioPlugin\fpmerge\hybrid\_datamerger.cmd
daily.




IMPORTANT >>> the BioPlugin Server on the national fingerprint server must be running. Otherwise the process will not work.





--- Files in the ~/support/fpmerge folder ---



- Read Me First.txt: documentation

- curlSendFile.cmd: secondary executable that uploads a file to the serveur via HTTP with cURL and calls the iSanté local merge process

- hybrid\_datamerger.cmd: main executable file that starts the fingerprint database merge process with hybrid\_datamerger.exe and then calls curlSendFile.cmd for every DuplicateTemplateFound.log in the iSanteMergers\_todo folder

- hybrid\_datamerger.exe: executable file created by M2Sys that merges the local database with the destination database. It uses the hybrid\_datamerger.ini file for its configuration.

- hybrid\_datamerger.ini: configuration file for hybrid\_datamerger.exe.

- hybrid\_datamerger.log: log of the previous executions of hybrid\_datamerger.cmd

- dataMerge.txt: file created by the hybrid\_datamerger.exe (not used in our process)

- DuplicateIDFound.txt: file created by the hybrid\_datamerger.exe (not used in our process)

- MergedRecords.txt: file created by the hybrid\_datamerger.exe (not used in our process)