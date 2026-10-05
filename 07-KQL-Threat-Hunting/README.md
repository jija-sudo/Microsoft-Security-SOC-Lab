# 07-KQL-Threat-Huntin

This section documents my hands-on practice using Kusto Query Language (KQL) to investigate security telemetry in Microsoft Sentinel and Log Analytics.

The goal of this section is not only to practice KQL syntax, but also to develop a security analyst mindset by starting with a security question, identifying the correct data source, analyzing the results, and deciding what should be investigated next.

## Skills Practiced

- Navigating Microsoft Sentinel and Log Analytics
- Identifying available security tables
- Writing and modifying KQL queries
- Filtering security events using `where`
- Selecting useful fields using `project`
- Aggregating events using `summarize`
- Counting unique values using `dcount()`
- Sorting results to identify unusual activity
- Investigating Windows Security Event logs
- Working with Event IDs such as 4624, 4625, and 4688
- Identifying telemetry limitations
- Using findings to guide the next investigation step

## Threat Hunting Approach

For my investigations, I use the following workflow:

1. Ask a security question or create a hypothesis.
2. Identify which table may contain the required telemetry.
3. Write a basic KQL query.
4. Review the results.
5. Narrow the query using additional filters.
6. Identify unusual activity.
7. Investigate the activity further.
8. Document the query, evidence, findings, and conclusion.

## Current Investigations

### Sentinel KQL Fundamentals

The `01-Sentinel-KQL-Fundamentals` section contains my first investigations using the `SecurityEvent` table.

One investigation focused on Windows failed logon activity using Event ID 4625. I first identified which systems generated the most failed logons and then investigated the accounts and timing associated with the activity.

I also explored Windows Event ID 4688 to investigate process creation activity. Although 4688 events were available, some process-related fields were not populated in the available telemetry. This demonstrated an important part of threat hunting: understanding the limitations of the available data and avoiding conclusions that cannot be supported by evidence.

## Key Lesson

Threat hunting is not simply searching for malicious activity. A query may return unusual behavior, incomplete information, or no results at all. Each result should guide the next question.

I am using this section to practice moving from:

**Security Question → KQL Query → Observation → Investigation → Evidence → Conclusion**
