<!-- img/a0e3200eb4774d689beed9338446efbb -->
# Overview
I have seen situations recently where Management Reporter maintenance is required in order to provide the correct and normal functioning of not only Management Reporter, but also the SQL Server installations for Dynamics GP.  A little-known feature of Management Reporter is that it keeps every single version of any report that has ever been run, and over time these can collect in your environment, consuming valuable space and resources for very little benefit.  This article is intended to provide the steps for periodic maintenance, as well as technical steps for your support personnel.

# User Maintenance
## Version Histories
### How to Find Historical Reports
On a server on which the Management Reporter client is installed, the user can go to the start menu in Windows and search for "Report Viewer".  Any workstation that has the Report Designer installed should have the Report Viewer installed.

![image.png](/img/a0e3200eb4774d689beed9338446efbb/01%20-%20Report%20Viewer%20Menu.png)

The Report Viewer is a library viewer for the management reports that have been run.  It also allows for the annotation of these reports, and various drill-in / drill-down activities.  In general, this functionality has also been incorporated into the web client version of these reports, rendering the Viewer's utility a matter of user preference.

On the left is the grouping of reports; your specific installation will show different folder structures, etc. depending on your configuration.  The pane on the right is used to navigate these reports in a fashion similar to the navigation of a file system.  (i.e. one can double-click and bring up the most recent version of the report, or drill into folders, etc.)

When the user right clicks on a report, the menu items contain "Show Versions":

![image.png](/img/a0e3200eb4774d689beed9338446efbb/03%20-%20MR%20Viewer%20Rich%20Click%20Menu.png)

![image.png](/img/a0e3200eb4774d689beed9338446efbb/02%20-%20MR%20Viewer%20Screen.png)

### Version History Maintenance
In this screenshot, one can see that the last time this report was run was 2015.  Unless there is a specific reason to keep this historical report, it is highly recommended that the user select a version (hold the CTRL key to select multiple lines, or hold the SHIFT key while clicking another line to select all reports in the range between the first and second clicks) and hit the "Delete" button on the right.

![image.png](/img/a0e3200eb4774d689beed9338446efbb/04%20-%20MR%20Report%20History%20List.png)

The system will request confirmation that it should delete these reports; click "Yes":

![image.png](/img/a0e3200eb4774d689beed9338446efbb/05%20-%20MR%20Confirm%20Delete.png)

## Considerations Regarding Deletion

**NOTE: this does not delete any data in your system; this only affects the actual _version_ of the report in question.  The only data loss risk is if there are annotations that are required to be kept on these reports.**

- The system may not be able to handle a large number of deletions at once.  When this happens, the program will provide an error message that says the connection timed out.  When this happens, it appears that no deletions actually occurred, and the user should select a smaller number of report histories to delete.
- There is no automated mechanism provided by Microsoft for this process.  
- When all historical runs of a report have been deleted, the list will refresh, and the report will disappear from the list.
- If there is a short list of runs for a specific report, the user can right click on the report itself and delete all versions at once, without the selection screen.
- **If this is the first time in a lengthy time frame that the version histories have been removed - i.e. if hundreds of version histories have been removed - ensure that you alert your SQL administrator, as there are technical steps that should be undertaken.**

## Recommended Maintenance Cycle

I recommend that this be done annually in most cases.  If your company uses Management Reporter heavily and produces many versions of reports, one may consider performing this maintenance quarterly.  

Note that if a previous version of a report is deleted and the company wishes to run it again, the report can be regenerated through Report Designer at any time, with the current information.

# Technical Maintenance

## Overview

This section is intended for SQL administrators.  The Management Reporter database comes in two variants: the Management Reporter database itself, and the Management Reporter Datamart database.  Since SQL administrators have almost no control or maintenance requirements for the ManagementReporterDM database, this article focuses on the ManagementReporter database proper.

Microsoft has not provided any automated tools, scripts or otherwise that will allow for the automated maintenance of these report histories.  Scripts are available online from reputable sources but use them at your own risk.  Before implementing any such automation, one should ensure that the company is not using the annotation features in Management Reporter, or data loss may result.

## Management Reporter Database Maintenance

### Recommended Backups

The main consideration about Management Reporter is that it contains all of the specifications that the company uses to produce financial statements.  These represent the core of the intellectual property that should be defended against data corruption, as the reports themselves (except for the annotation, if any) can be regenerated at any time.

Inside the Report Designer, there is an export function that will allow the user to save these building blocks of the financial reports to a ```.tdbx``` file.  This file should be kept in your source control system (i.e. Git, if you have one) and / or backed up as part of the disaster recovery strategy.  This backup should also be updated on a regular basis when changes are made to the __structure__ of financial reports.

The ManagementReporter database itself should be backed up as part of the normal database backup strategy, though personally I find it appropriate to set it to ```Simple``` recovery.  There shouldn't be so many changes to the financial statement structure that one would need to recover to a specific time of day.

### When Version Histories are Removed

If a user has undertaken the steps above, it is recommended to perform a file shrink on the ManagementReporter database, as the size requirement of the database will have changed dramatically.  I have seen situations where a 200**GB** database can come down to 100**MB**.  SQL Server will continue to reserve that space unless it is directly instructed to release it.

In the SQL Server Management Studio, these are the commands to shrink the database file sizes (both .log and .mdf are shown:)

***Data (MDF) File:***
```
USE [ManagementReporter]
GO
DBCC SHRINKFILE (N'ManagementReporter' , 0, TRUNCATEONLY)
GO
```

***Log (Log) File:***
```
USE [ManagementReporter]
GO
DBCC SHRINKFILE (N'ManagementReporter_log' , 0, TRUNCATEONLY)
GO
```

- Note that these scripts assume that the default SQL file references are in use.
- To be sure, always take a backup prior to execution of the above commands, and/or perform these in TEST first.

The system will show an output that indicates the action taken including the current size:

![image.png](/img/a0e3200eb4774d689beed9338446efbb/06%20-%20MR%20SQL%20Response.png)

(This particular example does not show that any action was taken - it is illustrative only.)

The effect of this is to reduce the database size on the file system and return some of that space to free and available space.  This will cause normal database maintenance routines to execute more quickly and will help to relieve any resource pressures in your environment.

## Maintenance Routines

As indicated prior, this database should not have many changes; of course, your mileage may vary.  My general recommendation is that the ManagementReporter database proper can be on ```Simple``` recovery, and included in the normal course of SQL database maintenance plans for the Dynamics GP databases.  This includes index optimization, update statistics, etc.  

<span style="font-size:0.75em;">
<end>
</span>
<br /><br />

<span style="font-size:0.5em;">
© 2024 Evergrowth Consulting - This article is provided ​“AS IS”. Evergrowth Consulting makes no other warranties, express or implied, and hereby disclaims all implied warranties, including any warranty of merchantability and warranty of fitness for a particular purpose.  
</span>
