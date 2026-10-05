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
