# CONFERENCE open source application project for EurOmnis 2025

## Dependencies

[Omnis Studio 11.2](https://www.omnis.net) for development

ArtsMan's [TMobjs](http://www2.artsman.com/omnis/Software/TMObjs259.zip) xcomp

ArtsMan's [jsoncpp](http://www2.artsman.com/omnis/Software/jsoncpp150.zip) xcomp

ArtsMan's [ExcelFormat](http://www2.artsman.com/omnis/Software/Omnis-XL394.zip) xcomp

A PostgreSQL v16 or later installation with two databases named conf_obf and stb, plus some required roles. 

### Recommended

[GitTools](https://github.com/van-beek-nl/GitTools) for managing the JSON import and export of the libraries

A Git GUI such as [Github Desktop](https://desktop.github.com/download/) or similar to assist with managing commits, branches, and so forth


## Installation and setup

1. Clone this repository
2. Connect to your Postgres instance and run the [roles.sql](db/roles.sql) script to create the required login and group roles.
3. Create a database called conf_obf
4. Restore the backup [conf_obf](db/conf_obj.backup) into it (you may get an error reporting transaction_timeout, but it can be ignored)
5. Create a database called stb
6. Restore the backup [stb](db/stb.backup) into it
7. Assign role _developer to your own login id to access everything in stb and conf_obf
8. Open (or import) infra.lbs, then CONFERENCE.lbs (order is important). You may need to update database connection parameters (see [Libraries](#libraries) for full details on startup)


### Databases
The "conf_obf" database holds the data for the conference application and the "stb" holds the Omnis string table entries for the application.

*NB. conf_obf is an obfuscated version of the proper application database.*

Note, the application has been updated since conf_obf was originally created, and as such expects extra columns to exist. conf_obf has been updated, but if there are any columns missing they should be added to the Postgres database definition manually (as there is no automatic conversion tool). 

### Libraries

Both libraries, infra and CONFERENCE, are included [here](/libs) as well as exported JSON [here](/src).

[infra.lbs](libs/infra.lbs) is our infrastructure library, common to all of our applications. It has schema and table and UI classes to provide functionality for user access (logins), permissions and roles to assign to users, "reference" tables (a trio of them which you can ignore for now - if you want an explanation just ask) amongst other things. Of note it includes infra.tkSuper and infra.rtSuper which contain most of the common code to all of our applications. tkSuper (for fat UI) and rtSuper (for remote tasks) are pretty much identical. There is also infra.tMaster, our super class containing all our generic code for table classes.

infra must be open in Omnis before [CONFERENCE](libs/CONFERENCE.lbs) is opened. You must open CONFERENCE.Startup_Task for the remote forms to work. If you have any difficulty connecting to the database stop execution and use the menu "Connection configuration -> db connections" to inspect the sqllite database. Make sure the connection parameters are correct (port is the biggest culprit). If you want to modify these settings once the application is correctly running, use the menu "Conference console -> Configuration (INI)" then select "database connection info" from the droplist.

[CONFERENCE.libini](libs/CONFERENCE.libini) is a sqllite database where the database connection and other variables are stored. Very briefly the Startup_Task in CONFERENCE.lbs will read CONFERENCE.libini to get the Host, database name, etc to automatically login to stb (to get stringtable entries) and conf_obf.

There are two faces to the application: the administration of the conference and the public face. The public face is the primary concern of the project. To identify the public face classes look for classes beginning rjs... and ending in ...ForSite. There is no login required when using these classes.


** NOT RECOMMENDED **
To begin using the remote form application locate CONFERENCE.rjsBusinessApp and test that form. This is to access the administration part of the application. As the mission is to improve the public face of the application (not the administration of the application), you may not need to use this at all. A User record has been setup for user devuser with password euromnis. If you do not have marquee installed (from Arts Management) there will be an error message after login that you can ignore.
