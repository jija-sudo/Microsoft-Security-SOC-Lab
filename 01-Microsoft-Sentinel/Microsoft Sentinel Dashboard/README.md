 ## Objective
 
I used Microsoft Sentinel workbooks to visualize authentication events from training logs. My goal was to identify accounts with many failed logons, examine when those failures occurred, and compare successful and failed logons.

### Lab Environment

- #### Platform: Microsoft Sentinel through the Microsoft Defender portal
- #### Workspace used for queries: lokoko-LAW
- #### Table: SecurityEvent
- #### Time range: Last 30 days
- #### Data: Training logs

### Panel 1: Top 10 Accounts with Failed Logons

```kusto
SecurityEvent
| where EventID == 4625
| extend AccountName = tolower(Account)
| summarize FailedLogons = count() by AccountName
| top 10 by FailedLogons desc
| render barchart
```

This query counts failed logons for each account and displays the ten accounts with the highest counts. The tolower() function combines account names that differ only in capitalization.

#### Findings:
- ##### \administrator had approximately 12,400 failed logons.
- ##### \admin had approximately 2,600 failed logons.
- ##### Other accounts had smaller counts.
   
These accounts would be useful starting points for investigation. However, a high number of failed logons alone does not prove an attack or account compromise.
Screenshot: Add the saved account bar chart here.

### Panel 2: Failed Logons Over Time

``` kusto
SecurityEvent
| where EventID == 4625
| summarize FailedLogons = count() by bin(TimeGenerated, 1h)
| order by TimeGenerated asc
| render timechart
```
The #### bin(TimeGenerated, 1h) function groups events into one-hour periods.
Finding: The chart displayed a single point representing approximately 18,200 failed logons. All returned events fell within one hourly group, so the chart could not show a trend across multiple hours.
Screenshot: Add the failed-logon time chart here.

### Panel 3: Successful vs Failed Logons

``` kusto
SecurityEvent
| where EventID in (4624, 4625)
| extend LogonResult = iff(EventID == 4624, "Successful", "Failed")
| summarize Events = count() by LogonResult
| render piechart
```
This query labels Event ID 4624 as a successful logon and Event ID 4625 as a failed logon. It then counts each category.
#### Finding: Sentinel displayed a donut chart with approximately 18,300 total logon events. Compared with the failed-logon total, failures represented most of the selected events. The displayed totals were rounded, so I did not calculate an exact percentage.
Screenshot: Add the successful-versus-failed logon chart here.

#### Troubleshooting

##### Selecting the Correct Workspace

The query returned data in Advanced Hunting, but the initial workbook query did not return useful results. After comparing the selected workspaces, I changed the custom panel’s query resource to lokoko-LAW, and the data appeared.
This taught me to check the query’s workspace before assuming the KQL is wrong.

##### Checking Which Fields Contained Data

Grouping by TargetAccount initially produced a blank account group. I inspected ten sample records using:
``` kusto
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Computer, Account,
          TargetAccount, TargetUserName, IpAddress
| take 10
```
In those ten records, Account contained values, while TargetAccount, TargetUserName, and IpAddress were blank. I used Account for the bar chart because it provided useful account information.

### Current Progress

| Task | Status |
|---|---|
| Render three different visualizations | Completed |
| Save the visualizations | Completed across two workbooks |
| Capture screenshots | Captured |
| Combine all three panels in one workbook | Pending verification |
| Create a test alert from sample logs | Pending |
| Bookmark a notable query result | Pending |
| Create a manual incident using the bookmark | Pending |
The bar chart was saved in Top 10 Accounts – Failed Logons. The time chart and donut chart were saved together in logon-attempts.

### Reflection

I learned that a field can exist in a table but still be empty in the records I am analyzing. Checking sample records helped me choose a useful field instead of repeatedly changing the query without understanding the data.
I also learned to separate observations from conclusions.

The training logs showed many failed logons, but I would need more evidence—such as source information and related successful logons—before concluding that an account was attacked or compromised.
