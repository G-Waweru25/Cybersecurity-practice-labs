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

Additionally, the `dedup` command removes duplicate field values to return only unique results, as demonstrated by the query:

`index=windowslogs | fields EventID User Image Hostname SourceIp | dedup SourceIp`,&#x20;

which returned **7 events**, each representing a unique `SourceIp`.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

The `rename` command enhances the clarity of search results by modifying field names, as shown in the query:

&#x20;`index=windowslogs | fields EventID User Image Hostname SourceIp | rename User as Employee`,&#x20;

where the `User` field was renamed to `Employee`.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 143113.png" alt=""><figcaption></figcaption></figure>

To demonstrate the practical application of the `rename` command, I executed the query `index=windowslogs | rename Hostname AS Computer SourceIp AS Source_IP EventID AS Event_ID | table Computer Source_IP Event_ID User`,&#x20;

which renamed `Hostname` to `Computer`, `SourceIp` to `Source_IP`, and `EventID` to `Event_ID` for improved readability, as captured below.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144521.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144537.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144530.png" alt=""><figcaption></figcaption></figure>

To perform pattern matching instead of exact-value searching, the query:

`index=windowslogs | regex Image="\.exe$"` was used to retrieve events with `Image` values ending in the `.exe` extension.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 145535.png" alt=""><figcaption></figcaption></figure>

To demonstrate the practical application of the `fields` command, I executed the query `index=windowslogs | fields + Domain SourceProcessId TargetProcessId`, which displayed the selected fields and showed that the highest `SourceProcessId` value was **9496**.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 123320 (1).png" alt=""><figcaption></figcaption></figure>

Furthermore, using the query:

`index=windowslogs | regex TargetObject="Manager$" | stats count by TargetObject`,&#x20;

pattern matching was applied to the `TargetObject` field, revealing that `HKLM\SOFTWARE\Microsoft\SecurityManager` had the highest event count.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 125558.png" alt=""><figcaption></figcaption></figure>

### Structuring Results

Splunk provides several SPL commands for structuring and organizing search results, enabling analysts to present log data in a clearer and more meaningful format.&#x20;

The `table` command organizes search results into a structured and readable format by displaying only selected fields, as illustrated by the query `index=windowslogs | table _time EventID Hostname SourceName`, which presented the specified fields in a tabular view.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 131015.png" alt=""><figcaption></figcaption></figure>

Splunk provides structuring commands such as `head`, `tail`, `sort`, and `reverse` to limit, order, and reorganize search results, making log analysis more efficient and focused.

To reconstruct the sequence of system activities, the query `index=windowslogs Hostname=Salena.Adam | table _time Hostname EventID Category | reverse` organized events for the `Salena.Adam` host into a chronological timeline.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 133848.png" alt=""><figcaption></figcaption></figure>

Splunk **subsearches** use the `join` command to correlate events from multiple data sources based on a common field, enabling analysts to enrich search results with related information from separate logs. In the example below, Sysmon process creation events (`EventID=1`) were linked to Security logon events (`EventID=4624`) through the `LogonId` field, producing a unified view containing process details alongside the corresponding logon type and IP address.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 134155.png" alt=""><figcaption></figcaption></figure>

Applying the `table` command in a practical task, the query `index=windowslogs | table EventID AccountName AccountType` generated a structured table and revealed `SYSTEM` as the first `AccountName`.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 172141.png" alt=""><figcaption></figcaption></figure>

Building on the previous task, the query `index=windowslogs | table EventID AccountName AccountType | reverse` was used to reverse the order of the tabulated results, with `EventID` **800** appearing first.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 172835.png" alt=""><figcaption></figcaption></figure>

A chronological timeline of process creation events was generated using the query `index=windowslogs EventID=1 | table _time ParentProcessId ProcessId ParentCommandLine CommandLine | reverse`, which revealed from the `ParentCommandLine` field that the command `net user /add A1berto paw0rd1` was executed, showing that the password assigned to the user `A1berto` was `paw0rd1`.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 173400.png" alt=""><figcaption></figcaption></figure>

### Transforming commands

Transforming commands in Splunk convert raw log events into summarized statistics and visual insights.

#### General Transformational Commands

Commands such as `top` and `rare` identifythe most and least frequent field values to facilitate pattern analysis.

The `highlight` command is used to visually emphasize specified fields or text within raw search results, as demonstrated by the query `index=windowslogs | highlight User EventID Image "Process accessed"`, which highlighted the selected values to make relevant log information easier to identify during analysis.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 132636.png" alt=""><figcaption></figcaption></figure>

The `stats` command summarizes search results by performing statistical calculations, with the query `index=windowslogs | stats count by EventID | sort EventID` grouping events by `EventID`, counting their occurrences, and displaying the results in ascending order.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 132829.png" alt=""><figcaption></figcaption></figure>

The `chart` command aggregates search results into a format suitable for visualization, with the query `index=windowslogs | chart count by User` summarizing the number of events associated with each user.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 133001.png" alt=""><figcaption></figcaption></figure>

#### Data Enrichment and Field Manipulation

To identify trends in process activity, the query `index=windowslogs Image!="" | timechart span=30m count by Image limit=5` employed the `timechart` command to visualize the five most common process images over successive 30-minute periods.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 230526.png" alt=""><figcaption></figcaption></figure>

The `iplocation` command enriches search results by adding geographic information to IP addresses, as demonstrated by the query `index=windowslogs | iplocation SourceIp | stats count by Country`, which grouped events according to the country associated with each source IP address.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 134912.png" alt=""><figcaption></figcaption></figure>

The `lookup` command enriches search results by matching fields with data stored in external lookup tables, as illustrated by the query `index=windowslogs | lookup user_roles Hostname OUTPUT UserRole | stats count by Hostname UserRole`, which associated each `Hostname` with its corresponding `UserRole` and summarized the results.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 135909.png" alt=""><figcaption></figcaption></figure>

Progressively, the `eval` command enables the creation of new fields and the modification of existing ones through calculations and conditional expressions, as illustrated by the query `index=windowslogs | eval LogonTypeDesc=case(LogonType==3,"Network Logon",LogonType==5,"Service") | stats count by LogonType LogonTypeDesc`, which converted numeric `LogonType` values into descriptive labels before summarizing the results.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 140328.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. I applied the `top` command by running the query `index=windowslogs | top Image`, which identified `C:\windows\system32\svchost.exe` as the most common `Image` value.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 141233.png" alt=""><figcaption></figcaption></figure>

2. I executed the query `index=windowslogs | iplocation SourceIp | stats count by Region` to enrich the source IP addresses with geographic information, identifying **California** as the region from which the events originated.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 012045.png" alt=""><figcaption></figcaption></figure>

3. The query `index=windowslogs | lookup image_riskscore Image OUTPUT RiskScore | stats count by Image RiskScore | sort - RiskScore` enabled me to correlate executable images with an external risk score lookup table, revealing `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` as the highest-risk image.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 012641.png" alt=""><figcaption></figcaption></figure>

### Anomaly Detection

Anomaly detection in Splunk helps identify unusual or suspicious events that deviate from normal patterns, enabling analysts to detect potential security threats that may not be apparent through standard field statistics alone.

To detect anomalous login activity, the query used `eventstats` to calculate login frequencies, `eval` to compute the proportion of logins from each country per user (`country_freq = logins_by_user_country / logins_by_user`), and `where` to filter rare user-country combinations below a threshold of 0.1 (10%).&#x20;

For example, `kbrown` had 200 total login events, but only one originated from Australia, giving a country frequency of `1/200 = 0.005` (0.5%). This indicates that Australian logins are extremely uncommon for this user, causing the event to fall below the 10% threshold and be flagged as a potential anomaly for further investigation.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 120622.png" alt=""><figcaption></figcaption></figure>

To detect anomalous login hours, the query calculated each user's average login time (`typical_hour`) and its standard deviation (`stdev_hour`), then computed a z-score (`zscore = |hour − typical_hour| / stdev_hour`) to measure how unusual each login was. For example, `jsmith` logged in at 18:40 (18.67 hours) compared with a typical login time of 13:29 (13.48 hours), resulting in a z-score of approximately 3.01, meaning the login occurred about three standard deviations later than normal and was therefore flagged as anomalous.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 123425.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. I executed the query `index=vpnlogs | eventstats count as logins_by_user by user | eventstats count as logins_by_user_country by user src_country | eval country_freq=logins_by_user_country/logins_by_user | where country_freq < 0.1 | table _time user src_ip src_country country_freq`, which calculated the login frequency for each user-country pair as `country_freq = logins_by_user_country / logins_by_user`.&#x20;

The query identified **`jsmith`** as the second outlier, with **Japan (JP)** as the anomalous login location because the user recorded **1 login from Japan out of 200 total logins**, resulting in a frequency of **1/200 = 0.005 (0.5%)**, which fell below the 10% threshold and was therefore flagged as anomalous.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 124949.png" alt=""><figcaption></figcaption></figure>



2. I executed the query `index=vpnlogs | eval hour=tonumber(strftime(_time,"%H")) + tonumber(strftime(_time,"%M"))/60 | eventstats avg(hour) as typical_hour stdev(hour) as stdev_hour by user | eval zscore=abs(hour-typical_hour)/stdev_hour | where zscore>3 | eval hour=round(hour,2), typical_hour=round(typical_hour,2) | eval stdev_hour=round(stdev_hour,2), zscore=round(zscore,2) | table _time user src_ip src_country hour typical_hour stdev_hour zscore | sort - hour_zscore`, which identified **`njackson`** as the user who suspiciously logged in at **03:17 AM**.&#x20;

The query calculated a **z-score** of **5.49** using the formula **(13.50 − 3.28) ÷ 1.86 = 5.49**, indicating that the login occurred approximately **5.5 standard deviations** earlier than the user's typical login time of **13:30**, thereby classifying it as a significant anomaly.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 125648.png" alt=""><figcaption></figcaption></figure>



