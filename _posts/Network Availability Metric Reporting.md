---
Date: May-21-2025 17:05 pm
tags: Weekly Tasks, Training, How-To
---
[[DCO]] is responsible for the weekly generation of network metrics for [[UMnet]].  These reports are a list of all [[CORE]], [[BIN]], [[DL]], and [[AL]] devices that have disconnected from the network and how long they have been disconnected for.  These reports are compiled and generated into a monthly report for the ITS Leadership Team.  Accurately identifying and reporting this data is crucial.  Inaccurate data could either overestimate the impact of specific outages and causes, or obscure network issues that need to be addressed. 

This process is broken into two main steps:  report generation and identifying outage causes.  Separate reports are generates for each device type listed above using [[Jasper]].  [[DCO]] takes these lists and consolidates them into a single sheet.  DCO then uses several different tools to identify the root cause of a specific outage.  This information is then added to a specific [Google Sheet](https://docs.google.com/spreadsheets/d/1tKXoAElJ-chZ76gs-6F5Fp3yPmeKEv3up6eNeplUCzc/edit?gid=1105537622#gid=1105537622) that compiles the data into a pie chart and monthly report.

---

# Generating Jasper Reports
## Tools/Environments

- [Jasper](https://spect-rm-vm.miserver.it.umich.edu:8443/jasperserver-pro/login.html;jsessionid=CF7C3C75D9FB9AF17B5A0F0DA19EF688)
## Jasper Reports

There are two types of reports that we need to generate in Jasper:  Weekly Availability reports, and outage details reports.  You will need to generate these reports for each device type[^1].  To find the specific report to run, Go to `Library` and ;LiSearch; search: `Availability Group`, then follow the steps below

---
### Weekly Availability Report

It is important that this report is generated each Monday, even if the data is not investigated until later in the week.

1. Click `Availability: Group`
2. Input Controls:
	1. Global Collection:  This is where you select the collection for each device type[^2]
	2. Time Interval: Select `Previous Week`
	3. Use Business Day Hours?:  Select the `False` Checkbox
	4. Click `Apply` to generate the Report, and `OK` to view the report
	5. Copy the percentage listed under `Actual Availability for Group` and paste it in the appropriate cell in the [Availability Report Sheet](https://docs.google.com/spreadsheets/d/1tKXoAElJ-chZ76gs-6F5Fp3yPmeKEv3up6eNeplUCzc/edit?gid=467694330#gid=467694330)

> [!warning]
> Currently the jasper report for the BIN do not have the reporting devices listed in them.  We are not running this report for now, and we will remove this note once the report are working.


### Outage Detail Reports

It is important that this report is generated each Monday, even if the data is not investigated until later in the week.

1. Click `Top-N Least Available: Group`
2. Input Controls:
	1. Global Collection:  This is where you select the collection for each device type [^2]
	2. Time interval: Select `Previous Week`
	3. Show Top N?:  Enter `100`
	4. Click `Apply` to generate the Report, and `OK` to view the report
	5. Click ;LiArrowRightSquare; `Export` and `Export as Excel`

---
## Formatting Data

There are additional steps DCO needs to take to format and organize the data from the excel document for outage detail reports.  It is easiest to make this formatting changes in Excel, and then move the data over to Google sheets.  Google sheets allows for easier collaboration between team members on the report.

### Tools/Environments

- Microsoft Excel
- Google Sheets
### Excel Formatting Changes

- Select the data for all devices with a total downtime longer than 15 minutes.  Copy the data and paste without formatting in a separate sheet
- Expand your columns, then delete the empty columns `B, C, E, F, G`
- Select column A and select the Find and Replace tool
- Leave the `Find what:` box empty, and put a dash in the `Replace with` box, then select `Replace All`
- Select column A and then select `Sort and Filter`>`Filter`
- Select the ;LiFilter; filter button on column A and uncheck the `-` from the filter list

This data is now ready to be transferred to Google Sheets for Collaborative investigation.
### Exporting to Google Sheets

- Open the [DCO Network Availability Investigation Sheet](https://docs.google.com/spreadsheets/d/1vrqnG6TxewFWJ-PncfRLaJnY7NQKo9IfSn5VxIKbFUU/edit?gid=0#gid=0)
- create a duplicate of the `template` sheet in this file, and rename the sheet to the current date
- Copy the data from the Excel Spreadsheets and paste without formatting into the new sheet
- Copy and paste the data from the sheet to the `Work Area` Sheet

> [!info]- Method to the Madness
>  - We choose to use google sheets because it is the easiest tool for multiple team members to collaborate at the same time.  If you are completing this list by yourself, feel free to use any tool you prefer.
>  - We also use this google sheet to keep a history of devices that went down.  This is useful because there may be devices that are already accounted for in the main availability sheet.  We can look at previous weeks to see if there are any duplicate devices that were down during both weeks.
>  - The work area sheet is used to keep us from accidentally deleting the weekly historical data that we rely on.

---
## Investigating Cause of Outage

We need to identify what the root cause of the outage occurring for each device by finding the associated Ticket/Reason for the outage. We then assign a specific cause for the outage, and a comment relaying the ticket/reason we found in our investigation.  This data is then used to create a summary of the outages for the [[ITS Leadership Team]].

### Tools/Environments

- [[spectrum]]
- [Bluecat](https://bluecat.umnet.umich.edu/app)
- [Teamdynamix](https://teamdynamix.umich.edu/)
- [Intermapper](http://intermapper.umnet.umich.edu/)
- [ITS Status Pages](https://status.its.umich.edu/)
- [EquipmentDB](https://equip.umnet.umich.edu/)
- its-opscon emails

### Data Parsing

There are some general rules that can be made about the data to parse down the information in each report.  We should start working on our reports in a top-down manner (CORE/BIN>DL>AL).  For example, if a DL shows as down in our DL report, it is very likely that there 
will be several AL switches connected to the DL that shows as down as well.  In these circumstances, we would only add the DL to the Metric list, to avoid adding several AL entries for the same event that occurred.

We should also exclude our results to devices that were down for longer than 15 minutes.  This aligns with DCO's general alarm response time.  if a device is down for less than 15 minutes, it's highly likely that there isn't an associated ticket, meaning no cause for the downtime can be contributed.

We have specific instructions regarding devices that are down for up to an hour, and have no associated ticket, or a ticket that shows no work was completed by Umnet to resolve the issue.  DCO should mark the cause for the outage on these device as power failure.  

Finally, there are some specific guidelines for devices that are showing down for the whole week.  If a device shows it has been down for 168 hours, we should take the following steps in the following order:

- Check if this device is already listed in the availability report from a previous week
- Check Intermapper if there is a note on the device listed why it's down
- Check EquipDB do see if the device is active
- Check Teamdynamix if there is a ticket attached the the Device Asset ID that states the device is down for construction or removed from the network.

If a device is down for this long, it is very likely that it was purposely taken of the network for construction, or to be decommissioned.

### Outage Causes

- **Configuration Error**: Outage related to the configuration/setup of the switch
- **Hardware Failure**: A physical component in the switch failed
- **Link Failure**: The connection between two network devices failed, or a link from a vendor failed (Merit, I2, ESnet)
- **Maint Project Work**: A switch upgrade or pre-determined ITS project window causes the device to go offline
- **Maint UMnet**:  A switch goes down due to maintenance work being completed by UMnet
- **Maint Unit**: A switch goes down due to maintenance work being completed by the Unit using the ITS switch
- **Power Failure**:  Input power to the device fails
- **Power Maintenance**: A scheduled Electrical Maintenance
- **Power UPS Issue**: The UPS connected to the switch returns an error that causes an outage to the switch
- **Software Issue**: 
- **Power Related**:  When power to the building and UPS is confirmed, but an alternative reason that power was lost to the device caused the outage
- **Undefined**: no known reason, or the reason is not listed as an outage cause.  We should only use this cause after all other potential causes have been exhausted
- **Unit Owned Equipment**:  A device connected to a network switch that is owned by a unit causes an outage (i.e. broadcast storms)
- **Vendor Config Error**:  A configuration error on the switch specifically relating to a change/configuration made by the switch vendor
- **Network Security Related**: A firewall or another network security system causes an outage to a router/switch
- **No Outages to Report**: 
- **Physical Damage**: Physical damage 
- **Water Leak**: Water damage

### Determining Cause

#### Spectrum

Spectrum is the easiest way to find a ticket/cause for a device being down.  This is because DCO will assign a ticket number, or provide a reason that a ticket was created, when an alarm is generated that last longer that 15 minutes.  to find historical data on devices, use the instructions for spectrum below:

1. Enter in the device name to the `Device Search` box and click `Go`.  
2. Click the `Cleared Alarm History` Tab.
3. Click the ;LiFilter; Filter button, set the date and time range to `Show Cleared Alarms History for a Time Range`, set the start time to the Sunday of the previous week, and then press `OK`

This will show you any alarms that occurred during since the previous week, and in many cases will list the specific alarm that caused the device to go down.  A ticket or reason for no ticket being created should be listed in the `trouble ticket ID` field.  If no reason or ticket is listed, additional investigation will be needed.

>[!Tip] Tips and Tricks
> During an outage that takes several devices offline, some devices may suppress themselves in spectrum.  This will cause the device to no longer generate an alarm.  If you notice several devices that have the same location in the metrics report that do not have an alarm, check the next device layer up with the same searching parameters.  For example, several West Quad Switches show that they were down for the same amount of time, but no alarms were generated.  Check the device dl-wquad-1 to see if there is an associated alarm, and subsequent attached ticket/note.

#### Intermapper

Intermapper Can be used to confirm the specific date and time of an outage on a device.  You can use this information to look for a ticket, or status page that could be related to the outage time.

#### Teamdynamix

When we create tickets for Tier 3 teams, we normally add the Asset ID to the ticket.  This allows us to retroactively look up a device under `ITS INF Assets` tab and review any related tickets.  If we find an associated ticket for one of the outages this week, we can generally find more information on the cause of the outage in the ticket feed.

#### EquipmentDB

EquipmentDB will show any recent comments regarding work completed by UMnet team members.  Additionally, it will state if the device is still active.  This is especially useful for determining if a device has been removed from the network, but has not been removed from spectrum yet.

#### ITS Status Pages

Using the search bar on the ITS status page is a good way to check if there have been any reported outages or degradation for a specific area on campus.  This status page should include more information on the outage, along with technical comments on the fix.  If we do find an associated status page, copy the status page number from the URL, and paste it at the end of the link below:

`https://status.its.umich.edu/admin/modify-form.php?id=`

This will take you to the editor login for this specific status page, where a ticket number may or may not be listed as well.
#### Bluecat and High range IP's

If a devices IP ends in a higher IP range, there is a change that this is a Temporary or ZTP port.  you can confirm this by Searching the IP in Bluecat and comparing the DHCP ranges.

## Final Touches

Once all the data has been investigated, you are almost ready to add it to the availability metrics spreadsheet.  There are a few formatting and clerical changes that we need to make to keep our data organized.  Follow the final steps below:

1. In google sheets convert your final list to a table, and organize by device name.
2. Copy and paste without formatting your data into the `Switch` Column with a couple of rows between the previous weeks data.
3. Mark the layer of the switch type for each device.
4. Mark the cause of outage for each device
5. Add the date to each device
6.  Extend the formatting of the duration column using the circle in the bottom of the selected cell
7. Use the ;LiPaintRoller; paint format tool on each column to have your new data match the current formatting.'

[^1]: CORE, BIN, DL, AL

[^2]: UMnet - Core Routers, UMnet - BIN Routers, UMnet - DL Routers, UMnet - AL Switches

