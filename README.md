Azure SQL Database - Scale using scheduled autoscaling
======================================================

#Change
This script it orginaly from Jorg Klein (jorgklein.com).
However this version is update with the new AZ Powershell commands instead of the soon deprecated AzureRm powershell ones.
Besides this the param azureRunAsConnectionName is removed in favor of using the default system assinged identity.
            

This Azure Automation runbook enables vertically scaling of an Azure SQL Database according to a schedule. Autoscaling based on a schedule allows you to scale your solution according to predictable resource demand. For example you could require a high capacity
 (e.g. P2) on Monday during peak hours, while the rest of the week the traffic is decreased allowing you to scale down (e.g. P1). Outside business hours and during weekends you could then scale down further to a minimum (e.g. S0). This runbook can be scheduled
 to run hourly. The code checks the scalingSchedule parameter to decide if scaling needs to be executed, or if the database is in the desired state already and no work needs to be done. The script is Timezone aware.


jorgklein.com for more information and a step-by-step setup guide




<#   

.SYNOPSIS   

    Vertically scale an Azure SQL Database up or down according to a 

    schedule using Azure Automation.    

   

.DESCRIPTION   

    This Azure Automation runbook enables vertically scaling of 

    an Azure SQL Database according to a schedule. Autoscaling based 

    on a schedule allows you to scale your solution according to 

    predictable resource demand. For example you could require a 

    high capacity (e.g. P2) on Monday during peak hours, while the rest

    of the week the traffic is decreased allowing you to scale down 

    (e.g. P1). Outside business hours and during weekends you could then 

    scale down further to a minimum (e.g. S0). This runbook 

    can be scheduled to run hourly. The code checks the 

    scalingSchedule parameter to decide if scaling needs to be 

    executed, or if the database is in the desired state already and 

    no work needs to be done. The script is Timezone aware.



.PARAMETER resourceGroupName

    Name of the resource group to which the database server is 

    assigned.


 .PARAMETER serverName  

    Azure SQL Database server name.

       

.PARAMETER databaseName   

    Azure SQL Database name (case sensitive).



.PARAMETER scalingSchedule

    Database Scaling Schedule. It is possible to enter multiple 

    comma separated schedules: [{},{}]

    Weekdays start at 0 (sunday) and end at 6 (saturday).

    If the script is executed outside the scaling schedule time slots

    that you defined, the defaut edition/tier (see below) will be 

    configured.



.PARAMETER scalingScheduleTimeZone

    Time Zone of time slots in $scalingSchedule. 

    Available time zones: [System.TimeZoneInfo]::GetSystemTimeZones().



.PARAMETER defaultEdition

    Azure SQL Database Edition that wil be used outside the slots 

    specified in the scalingSchedule paramater value.

    Example values: Basic, Standard, Premium RS, Premium.

    For more information on editions/tiers levels, 

    http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx 

 

.PARAMETER defaultTier   

    Azure SQL Database Tier that wil be used outside the slots 

    specified in the scalingSchedule paramater value.

    Example values: Basic, S0, S1, S2, S3, PRS1, PRS2, PRS4, 

    PRS6, P1, P2, P4, P6, P11, P15.



.EXAMPLE

        -resourceGroupName myResourceGroup

        -serverName myserver

        -databaseName myDatabase

        -scalingSchedule [{WeekDays:[1], StartTime:'06:59:59', 

            StopTime:'17:59:59', Edition: 'Premium', Tier: 'P2'},

            {WeekDays:[2,3,4,5], StartTime:'06:59:59', 

            StopTime:'17:59:59', Edition: 'Premium', Tier: 'P1'}]

        -scalingScheduleTimeZone W. Europe Standard Time

        -defaultEdition Standard

        -defaultTier S0

   

.NOTES   

    Original Author: Jorg Klein
    New Author: Ferron Nijland      
    Last Update: 19/01/2023   

