# Microsoft Sentinel KQL Fundamentals

## Objective

The goal of this lab is to practice using Kusto Query Language (KQL) to search, filter, organize, and analyze security data in Microsoft Sentinel.

Instead of only running queries, I focused on understanding what question each query answers from a SOC analyst's point of view.

## Environment

- Microsoft Sentinel
- Log Analytics Workspace
- Microsoft Sentinel training data
- Kusto Query Language (KQL)

## Skills Practiced

- Identifying available security tables
- Filtering security events
- Selecting important fields
- Sorting events by time
- Counting and summarizing events
- Identifying systems generating security events
- Investigating authentication activity
- Building queries around an investigation question

## Investigation Approach

For each query, I followed this process:

1. Ask a security question.
2. Identify the table that may contain the answer.
3. Write a simple KQL query.
4. Review the results.
5. Add filters to reduce unnecessary data.
6. Identify anything unusual.
7. Document what the results mean.

## KQL Investigations

The following sections document the KQL queries I used and what I learned from the results.

### Investigation 1: Identifying Systems with High Failed Logon Activity

#### Security Question

Which computers generated the most failed Windows logon events during the last 7 days?

#### KQL Query


![Failed Logons by Computer](screenshoots/02-FailedLogins.png)

```kusto
SecurityEvent
| where EventID == 4625
| summarize FailedLogins=count() by Computer
| sort by FailedLogins desc
```

#### Summary

I used Windows Security Event ID 4625 to identify systems with failed logon activity during the last 7 days.

The results showed that `SOC-FW-RDP` had the highest number of failed logons with 11,970 events, followed by `SHIR-Hive` with 5,704 and `SHIR-SAP` with 489.

The large number of failed logons on `SOC-FW-RDP` stood out, but this alone does not confirm malicious activity. I decided to investigate this system further to determine which accounts were involved and where the failed logon attempts were coming from.

### Investigation 2: Identifying Targeted Accounts

#### Security Question

Which accounts generated the failed logon attempts on `SOC-FW-RDP`?

#### KQL Query

```kusto
SecurityEvent
| where EventID == 4625
| where Computer == "SOC-FW-RDP"
| summarize FailedLogins=count() by Account
| sort by FailedLogins desc
```

![Failed Logons by Account](screenshoots/03-failed-logons-by-account.png)

#### Findings

The results showed failed authentication attempts against many different account names.

The account `\ADMINISTRATOR` had the highest number of failed logons with 9,997 attempts. Other account names included `\ADMIN`, `\USER`, `\TEST`, `\SERVER`, `\BACKUP`, and `\VEEAM`.

#### Analyst Thinking

The large number of failures against `\ADMINISTRATOR` stood out. I also noticed that many different generic or administrative account names were attempted.

This pattern could be consistent with automated account or password guessing. However, the account names alone are not enough to confirm an attack.

I needed to determine where these authentication attempts originated.

#### Next Question

Which source IP addresses generated the failed logon attempts against `SOC-FW-RDP`?
