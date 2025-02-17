---
title: Learn the advanced hunting query language in Microsoft Threat Protection
description: Create your first threat hunting query and learn about common operators and other aspects of the advanced hunting query language
keywords: advanced hunting, threat hunting, cyber threat hunting, microsoft threat protection, microsoft 365, mtp, m365, search, query, language, learn, first query, telemetry, events, telemetry, custom detections, schema, kusto, operators, data types, powershell download, query example
search.product: eADQiWindows 10XVcnh
search.appverid: met150
ms.prod: microsoft-365-enterprise
ms.mktglfcycl: deploy
ms.sitesec: library
ms.pagetype: security
f1.keywords:
- NOCSH
ms.author: lomayor
author: lomayor
ms.localizationpriority: medium
manager: dansimp
audience: ITPro
ms.collection: M365-security-compliance 
ms.topic: article
---

# Learn the advanced hunting query language

**Applies to:**
- Microsoft Threat Protection

Advanced hunting is based on the [Kusto query language](https://docs.microsoft.com/azure/kusto/query/). You can use Kusto syntax and operators to construct queries that locate information in the [schema](advanced-hunting-schema-tables.md) specifically structured for advanced hunting. To understand these concepts better, run your first query.

## Try your first query

In Microsoft 365 security center, go to **Hunting** to run your first query. Use the following example:

```kusto
// Finds PowerShell execution events that could involve a download
union DeviceProcessEvents, DeviceNetworkEvents
| where Timestamp > ago(7d)
// Pivoting on PowerShell processes
| where FileName in~ ("powershell.exe", "powershell_ise.exe")
// Suspicious commands
| where ProcessCommandLine has_any("WebClient",
 "DownloadFile",
 "DownloadData",
 "DownloadString",
"WebRequest",
"Shellcode",
"http",
"https")
| project Timestamp, DeviceName, InitiatingProcessFileName, InitiatingProcessCommandLine, 
FileName, ProcessCommandLine, RemoteIP, RemoteUrl, RemotePort, RemoteIPType
| top 100 by Timestamp
```

This is how it will look like in advanced hunting.

![Image of Microsoft Threat Protection advanced hunting query](../../media/advanced-hunting-query-example-2.png)

### Describe the query and specify the tables to search
A short comment has been added to the beginning of the query to describe what it is for. This helps if you later decide to save the query and share it with others in your organization. 

```kusto
// Finds PowerShell execution events that could involve a download
```

The query itself will typically start with a table name followed by a series of elements started by a pipe (`|`). In this example, we start by creating a union of two tables,  `DeviceProcessEvents` and `DeviceNetworkEvents`, and add piped elements as needed.

```kusto
union DeviceProcessEvents, DeviceNetworkEvents
```
### Set the time range
The first piped element is a time filter scoped to the previous seven days. Keeping the time range as narrow as possible ensures that queries perform well, return manageable results, and don't time out.

```kusto
| where Timestamp > ago(7d)
```

### Check specific processes
The time range is immediately followed by a search for process file names representing the PowerShell application.

```
// Pivoting on PowerShell processes
| where FileName in~ ("powershell.exe", "powershell_ise.exe")
```

### Search for specific command strings
Afterwards, the query looks for strings in command lines that are typically used to download files using PowerShell.

```kusto
// Suspicious commands
| where ProcessCommandLine has_any("WebClient",
    "DownloadFile",
    "DownloadData",
    "DownloadString",
    "WebRequest",
    "Shellcode",
    "http",
    "https")
```

### Customize result columns and length 
Now that your query clearly identifies the data you want to locate, you can add elements that define what the results look like. `project` returns specific columns, and `top` limits the number of results. These operators help ensure the results are well-formatted and reasonably large and easy to process.

```kusto
| project Timestamp, DeviceName, InitiatingProcessFileName, InitiatingProcessCommandLine, 
FileName, ProcessCommandLine, RemoteIP, RemoteUrl, RemotePort, RemoteIPType
| top 100 by Timestamp
```

Click **Run query** to see the results. Select the expand icon at the top right of the query editor to focus on your hunting query and the results. 

![Image of the Expand control in the advanced hunting query editor](../../media/advanced-hunting-expand.png)

>[!TIP]
>You can view query results as charts and quickly adjust filters. For guidance, [read about working with query results](advanced-hunting-query-results.md)

## Learn common query operators for advanced hunting

Now that you've run your first query and have a general idea of its components, it's time to backtrack a little bit and learn some basics. The Kusto query language used by advanced hunting supports a range of operators, including the following common ones.

| Operator | Description and usage |
|--|--|
| `where` | Filter a table to the subset of rows that satisfy a predicate. |
| `summarize` | Produce a table that aggregates the content of the input table. |
| `join` | Merge the rows of two tables to form a new table by matching values of the specified column(s) from each table. |
| `count` | Return the number of records in the input record set. |
| `top` | Return the first N records sorted by the specified columns. |
| `limit` | Return up to the specified number of rows. |
| `project` | Select the columns to include, rename or drop, and insert new computed columns. |
| `extend` | Create calculated columns and append them to the result set. |
| `makeset` |  Return a dynamic (JSON) array of the set of distinct values that Expr takes in the group. |
| `find` | Find rows that match a predicate across a set of tables. |

To see a live example of these operators, run them from the **Get started** section in advanced hunting.

## Understand data types and their query syntax implications

Data in advanced hunting tables are generally classified into the following data types.

| Data type | Description and query implications |
|--|--|
| `datetime` | Data and time information typically representing event timestamps |
| `string` | Character string |
| `bool` | True or false |
| `int` | 32-bit numeric value  |
| `long` | 64-bit numeric value |

## Use sample queries

The **Get started** section provides a few simple queries using commonly used operators. Try running these queries and making small modifications to them.

![Image of advanced hunting window](../../media/advanced-hunting-get-started.png)

>[!NOTE]
>Apart from the basic query samples, you can also access [shared queries](advanced-hunting-shared-queries.md) for specific threat hunting scenarios. Explore the shared queries on the left side of the page or the GitHub query repository.

## Access query language documentation

For more information on Kusto query language and supported operators, see [Kusto query language documentation](https://docs.microsoft.com/azure/kusto/query/).

## Related topics
- [Advanced hunting overview](advanced-hunting-overview.md)
- [Work with query results](advanced-hunting-query-results.md)
- [Use shared queries](advanced-hunting-shared-queries.md)
- [Hunt for threats across devices and emails](advanced-hunting-query-emails-devices.md)
- [Understand the schema](advanced-hunting-schema-tables.md)
- [Apply query best practices](advanced-hunting-best-practices.md)
