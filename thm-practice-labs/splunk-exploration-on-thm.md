# Splunk Exploration on THM

### **Introduction**

Splunk is a powerful Security Information and Event Management (SIEM) platform that enables analysts to search, analyze, visualize, and investigate log data using its Search Processing Language (SPL).&#x20;

This room covers the core features of SPL, including log filtering, command chaining, data visualization, and anomaly detection to help extract valuable insights from large datasets.

### Search & Reporting

The Splunk Search & Reporting App is the default interface for searching and analyzing indexed data, providing features such as the Search Head, Time Picker, Search History, Data Summary, and Fields Sidebar to help analysts efficiently query, explore, and interpret log data.

To reinforce these concepts, the following exercises involved querying and analyzing the Windows logs dataset using the Splunk Search & Reporting App.

* Executing the query `index=windowslogs` over the **All Time** range returned a total of **12,256 events**.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

* The **SourceIP** field showed that **172.90.12.11** generated the highest number of events in the dataset.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

* Filtering the logs for **04/15/2022** between **08:05 AM and 08:06 AM** returned **134 events**.

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

### Search Operators

Splunk's Search Processing Language (SPL) enables users to search, filter, transform, and analyze ingested log data, with free-text searches providing a simple way to locate events containing specific keywords when field names are unknown.

After exploring free-text searches, the task demonstrated the use of relational operators for targeted log filtering. The query `index=windowslogs AccountName!=SYSTEM` demonstrated the use of the `!=` relational operator to retrieve all log events where the `AccountName` field did not match `SYSTEM`.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 143315.png" alt=""><figcaption></figcaption></figure>

Additionally, the task showed logical operators such `AND` ,`OR`, `IN` and `NOT`. Executing the queries `index=windowslogs AccountName!=SYSTEM AND AccountName=James` and `index=windowslogs AccountName!=SYSTEM AccountName=James`, demonstrated how the `AND` operator filters events to display only those associated with the `James` account while excluding `SYSTEM,` as captured below.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 144405.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Splunk supports wildcards (`*`) for searching partial text matches, as illustrated by the query `index=windowslogs DestinationIp=172*`, which retrieved events with destination IP addresses beginning with `172,`.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 151036.png" alt=""><figcaption></figcaption></figure>

Further, Splunk uses **quotation marks (`""`)** to search for exact phrases or treat multiple words as a single search value, while **parentheses (`()`)** group search conditions and control the order of evaluation of logical operators, ensuring complex queries return the intended results.

I subsequently applied the search operators in a practical exercise to filter and analyze Windows event logs.

Firstly, I executed the query `index=windowslogs EventID=4624 | stats count` to retrieve events with the `EventID` value `4624`.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Secondly, I executed the query `index=windowslogs DestinationIp="172.18.39.6" DestinationPort=135 | stats count` to determine the number of events with the destination IP address `172.18.39.6` and destination port `135`, which returned a total of **4** events.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 153323.png" alt=""><figcaption></figcaption></figure>

Thirdly, I ran the query `index=windowslogs Hostname=Salena.Adam DestinationIp=172.18.38.5` and found that the source IP address with the highest event count was **`172.90.12.11`**.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Fourthly, I applied wildcard searching by executing the query `index=windowslogs cyber*`, which returned **12,256** matching events from the `windowslogs` index.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Lastly, I identified the `AND` operator as having the lowest priority in Splunk search evaluation.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 154927.png" alt=""><figcaption></figcaption></figure>

### Filtering Results

Splunk's Search Processing Language (SPL) uses **pipe (`|`) commands** to chain multiple operations, allowing analysts to progressively filter, refine, and organize large volumes of log data into focused and meaningful search results for efficient analysis.

The `fields` command filters search results by including or excluding specific fields to simplify log analysis, as illustrated by the query `index=windowslogs | fields host User SourceIp`, which displayed only the `host`, `User`, and `SourceIp` fields.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 160928.png" alt=""><figcaption></figcaption></figure>

The `dedup` command removes duplicate field values to return only unique results, as demonstrated by the query `index=windowslogs | fields EventID User Image Hostname SourceIp | dedup SourceIp`, which returned **7 events**, each representing a unique `SourceIp`.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
