# Splunk Exploration on THM

### **Introduction**

Splunk is a powerful Security Information and Event Management (SIEM) platform that enables analysts to search, analyze, visualize, and investigate log data using its Search Processing Language (SPL).&#x20;

This room explores the core features of Splunk's Search Processing Language (SPL), including log filtering, command chaining, data visualization, and anomaly detection, demonstrating how analysts extract actionable insights from large log datasets.

### Search & Reporting

The Splunk Search & Reporting App is the default interface for searching and analyzing indexed data, providing features such as the Search Head, Time Picker, Search History, Data Summary, and Fields Sidebar to help analysts efficiently query, explore, and interpret log data.

#### Questions

To reinforce these concepts, the following exercises involved querying and analyzing the Windows logs dataset using the Splunk Search & Reporting App.

1. Executing the query `index=windowslogs` over the **All Time** range returned a total of **12,256 events.**

**Figure 1.** _Search results showing all events in the `windowslogs` index._

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

2. The following figure depicts the **SourceIP** field that showed that **172.90.12.11** generated the highest number of events in the dataset.

**Figure 2.** _Distribution of `SourceIp` values within the `windowslogs` dataset._

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

3. Filtering the logs for **04/15/2022** between **08:05 AM and 08:06 AM** returned **134 events.**

**Figure 3.** _Time range filter configured for 15 April 2022 between 08:05 AM and 08:06 AM._

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

**Figure 4.** _Filtered search results returned for the selected time range._

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

### Search Operators

Splunk's Search Processing Language (SPL) enables users to search, filter, transform, and analyze ingested log data, with free-text searches providing a simple way to locate events containing specific keywords when field names are unknown.

After exploring free-text searches, the task demonstrated the use of relational operators for targeted log filtering. The query `index=windowslogs AccountName!=SYSTEM` demonstrated the use of the `!=` relational operator to retrieve all log events where the `AccountName` field did not match `SYSTEM`.

**Figure 5.** _Search results excluding events associated with the `SYSTEM` account._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 143315.png" alt=""><figcaption></figcaption></figure>

The task further demonstrated logical operators such as `AND`, `OR`, `IN`, and `NOT` for constructing compound search conditions. Executing the queries `index=windowslogs AccountName!=SYSTEM AND AccountName=James` and `index=windowslogs AccountName!=SYSTEM AccountName=James`, demonstrated how the `AND` operator filters events to display only those associated with the `James` account while excluding `SYSTEM,` as captured below.

**Figure 6.** &#x52;_&#x65;sults of the query using the explicit `AND` operator._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 144405.png" alt=""><figcaption></figcaption></figure>

**Figure 7.** _Results of the equivalent search using implicit `AND` behaviour._

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Splunk supports wildcards (`*`) for searching partial text matches, as illustrated by the query `index=windowslogs DestinationIp=172*`, which retrieved events with destination IP addresses beginning with `172`.

**Figure 8.** _Wildcard search returning destination IP addresses beginning with `172`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 151036.png" alt=""><figcaption></figcaption></figure>

Further, Splunk uses **quotation marks (`""`)** to search for exact phrases or treat multiple words as a single search value, while **parentheses (`()`)** group search conditions and control the order of evaluation of logical operators, ensuring complex queries return the intended results.

#### Questions

Subsequently, I applied the search operators in a practical exercise to filter and analyze Windows event logs.

1. Firstly, I executed the query `index=windowslogs EventID=4624 | stats count` to retrieve events with the `EventID` value `4624`.

**Figure 9.** _Event count for Windows logs with `EventID` 4624._

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

2. Secondly, I executed the query `index=windowslogs DestinationIp="172.18.39.6" DestinationPort=135 | stats count` to determine the number of events with the destination IP address `172.18.39.6` and destination port `135`, which returned a total of **4** events.

**Figure 10.** _Events matching destination IP `172.18.39.6` and destination port `135`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 153323.png" alt=""><figcaption></figcaption></figure>

3. Thirdly, I ran the query `index=windowslogs Hostname=Salena.Adam DestinationIp=172.18.38.5` and found that the source IP address with the highest event count was **`172.90.12.11`**.

**Figure 11.** _Search results identifying the most frequent `SourceIp` value._

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

4. Fourthly, I applied wildcard searching by executing the query `index=windowslogs cyber*`, which returned **12,256** matching events from the `windowslogs` index.

**Figure 12.** _Wildcard search results for events containing the term `cyber*`._

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

5. Lastly, I identified the `AND` operator as having the lowest priority in Splunk search evaluation, as captured below

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 154927.png" alt=""><figcaption></figcaption></figure>

### Filtering Results

Splunk's Search Processing Language (SPL) uses **pipe (`|`) commands** to chain multiple operations, allowing analysts to progressively filter, refine, and organize large volumes of log data into focused and meaningful search results for efficient analysis.

The `fields` command filters search results by including or excluding specific fields to simplify log analysis, as illustrated by the query `index=windowslogs | fields host User SourceIp`, which displayed only the `host`, `User`, and `SourceIp` fields.

**Figure 13.** _Output of the `fields` command displaying selected log fields._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 160928.png" alt=""><figcaption></figcaption></figure>

Building on field selection, the `dedup` command removes duplicate field values to return only unique results, as demonstrated by the query:

`index=windowslogs | fields EventID User Image Hostname SourceIp | dedup SourceIp`,&#x20;

which returned **7 events**, each representing a unique `SourceIp`.

**Figure 14.** _Results of the `dedup` command showing unique `SourceIp` values._

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

The `rename` command enhances the clarity of search results by modifying field names, as shown in the query:

&#x20;`index=windowslogs | fields EventID User Image Hostname SourceIp | rename User as Employee`,&#x20;

where the `User` field was renamed to `Employee`.

**Figure 15.** _Output of the `rename` command renaming the `User` field to `Employee`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 143113.png" alt=""><figcaption></figcaption></figure>

To demonstrate the practical application of the `rename` command, I executed the query `index=windowslogs | rename Hostname AS Computer SourceIp AS Source_IP EventID AS Event_ID | table Computer Source_IP Event_ID User`,&#x20;

which renamed `Hostname` to `Computer`, `SourceIp` to `Source_IP`, and `EventID` to `Event_ID` to improve the readability and presentation of the search results, as captured below.

**Figure 16.** _Renamed Hostname as Computer_

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144521.png" alt=""><figcaption></figcaption></figure>

**Figure 17.** _Renamed SourceIp as Source\_IP_

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144537.png" alt=""><figcaption></figcaption></figure>

**Figure 18.** _Renamed EventID as Event\_ID_

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144530.png" alt=""><figcaption></figcaption></figure>

To perform pattern matching instead of exact-value searching, the query:

`index=windowslogs | regex Image="\.exe$"` was used to retrieve events with `Image` values ending in the `.exe` extension.

**Figure 19.** _Regular expression search returning executable images ending with `.exe`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 145535.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. To demonstrate the practical application of the `fields` command, I executed the query `index=windowslogs | fields + Domain SourceProcessId TargetProcessId`, which displayed the selected fields and showed that the highest `SourceProcessId` value was **9496**.

**Figure 20.** _Selected `Domain`, `SourceProcessId`, and `TargetProcessId` fields._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 123320 (1).png" alt=""><figcaption></figcaption></figure>

2. Furthermore, using the query:

`index=windowslogs | regex TargetObject="Manager$" | stats count by TargetObject`,&#x20;

pattern matching was applied to the `TargetObject` field, revealing that `HKLM\SOFTWARE\Microsoft\SecurityManager` had the highest event count.

**Figure 21.** _Results of the regular expression query matching `TargetObject` values ending in `Manager`_.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 125558.png" alt=""><figcaption></figcaption></figure>

### Structuring Results

Splunk provides several SPL commands for structuring and organizing search results, enabling analysts to present log data in a clearer and more meaningful format.&#x20;

The `table` command organizes search results into a structured and readable format by displaying only selected fields, as illustrated by the query `index=windowslogs | table _time EventID Hostname SourceName`, which presented the specified fields in a tabular view.

**Figure 22.** _Output of the `table` command displaying selected event fields._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 131015.png" alt=""><figcaption></figcaption></figure>

In addition to the `table` command, Splunk provides structuring commands such as `head`, `tail`, `sort`, and `reverse` to limit, order, and reorganize search results, making log analysis more efficient and focused.

To reconstruct the sequence of system activities, the query `index=windowslogs Hostname=Salena.Adam | table _time Hostname EventID Category | reverse` organized events for the `Salena.Adam` host into a chronological timeline.

**Figure 23.** _Chronological timeline of events for the `Salena.Adam` host._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 133848.png" alt=""><figcaption></figcaption></figure>

Splunk **subsearches** use the `join` command to correlate events from multiple data sources based on a common field, enabling analysts to enrich search results with related information from separate logs. In the example below, Sysmon process creation events (`EventID=1`) were linked to Security logon events (`EventID=4624`) through the `LogonId` field, producing a unified view containing process details alongside the corresponding logon type and IP address.

**Figure 24.** _Correlated Sysmon and Security events using a Splunk subsearch and `join`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 134155.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. Applying the `table` command in the practical task, the query `index=windowslogs | table EventID AccountName AccountType` generated a structured table and revealed `SYSTEM` as the first `AccountName`.

**Figure 25.**_Structured table showing `EventID`, `AccountName`, and `AccountType`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 172141.png" alt=""><figcaption></figcaption></figure>

2. Building on the previous task, the query `index=windowslogs | table EventID AccountName AccountType | reverse` was used to reverse the order of the tabulated results, with `EventID` **800** appearing first.

**Figure 26.** _Reversed table output with `EventID` 800 appearing first._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 172835.png" alt=""><figcaption></figcaption></figure>

3. A chronological timeline of process creation events was generated using the query `index=windowslogs EventID=1 | table _time ParentProcessId ProcessId ParentCommandLine CommandLine | reverse`, which revealed from the `ParentCommandLine` field that the command `net user /add A1berto paw0rd1` was executed, revealing that the password assigned to the user `A1berto` was `paw0rd1`.

_Figure 27. Timeline revealing the `net user /add` command used to create the `A1berto` account._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 173400.png" alt=""><figcaption></figcaption></figure>

### Transforming commands

Transforming commands in Splunk convert raw log events into summarized statistics and visual insights.

#### General Transformational Commands

Commands such as `top` and `rare` identify the most and least frequent field values to facilitate pattern analysis.

The `highlight` command is used to visually emphasize specified fields or text within raw search results, as demonstrated by the query `index=windowslogs | highlight User EventID Image "Process accessed"`, which highlighted the selected values to make relevant log information easier to identify during analysis.

**Figure 28.** _Highlighted search results emphasizing selected fields and text._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 132636.png" alt=""><figcaption></figcaption></figure>

The `stats` command summarizes search results by performing statistical calculations, with the query `index=windowslogs | stats count by EventID | sort EventID` grouping events by `EventID`, counting their occurrences, and displaying the results in ascending order.

**Figure 29.** _Statistical summary of Windows events grouped by `EventID`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 132829.png" alt=""><figcaption></figcaption></figure>

The `chart` command aggregates search results into a format suitable for visualization, with the query `index=windowslogs | chart count by User` summarizing the number of events associated with each user.

**Figure 30.** _Chart showing the distribution of events by user._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 133001.png" alt=""><figcaption></figcaption></figure>

#### Data Enrichment and Field Manipulation

To identify trends in process activity, the query `index=windowslogs Image!="" | timechart span=30m count by Image limit=5` employed the `timechart` command to visualize the five most common process images over successive 30-minute periods.

**Figure 31.** _Timechart visualizing the five most common process images over time._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 230526.png" alt=""><figcaption></figcaption></figure>

The `iplocation` command enriches search results by adding geographic information to IP addresses, as demonstrated by the query `index=windowslogs | iplocation SourceIp | stats count by Country`, which grouped events according to the country associated with each source IP address.

**Figure 32.** _Geographic distribution of source IP addresses using the `iplocation` command._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 134912.png" alt=""><figcaption></figcaption></figure>

The `lookup` command enriches search results by matching fields with data stored in external lookup tables, as illustrated by the query `index=windowslogs | lookup user_roles Hostname OUTPUT UserRole | stats count by Hostname UserRole`, which associated each `Hostname` with its corresponding `UserRole` and summarized the results.

**Figure 33.** Lookup results associating hostnames with corresponding user roles.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 135909.png" alt=""><figcaption></figcaption></figure>

Moreover, the `eval` command enables the creation of new fields and the modification of existing ones through calculations and conditional expressions, as illustrated by the query `index=windowslogs | eval LogonTypeDesc=case(LogonType==3,"Network Logon",LogonType==5,"Service") | stats count by LogonType LogonTypeDesc`, which converted numeric `LogonType` values into descriptive labels before summarizing the results.

**Figure 34.** _Descriptive logon types generated using the `eval` command._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 140328.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. I applied the `top` command by running the query `index=windowslogs | top Image`, which identified `C:\windows\system32\svchost.exe` as the most common `Image` value.

**Figure 35.** _Top executable images ranked by occurrence._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 141233.png" alt=""><figcaption></figcaption></figure>

2. I executed the query `index=windowslogs | iplocation SourceIp | stats count by Region` to enrich the source IP addresses with geographic information, identifying **California** as the region from which the events originated.

**Figure 36.** _Source IP addresses grouped by geographic region._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 012045.png" alt=""><figcaption></figcaption></figure>

3. The query `index=windowslogs | lookup image_riskscore Image OUTPUT RiskScore | stats count by Image RiskScore | sort - RiskScore` enabled me to correlate executable images with an external risk score lookup table, revealing `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` as the highest-risk image.

**Figure 37.** _Executable images enriched with external risk scores using a lookup table._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 012641.png" alt=""><figcaption></figcaption></figure>

### Anomaly Detection

Anomaly detection in Splunk helps identify unusual or suspicious events that deviate from normal patterns, enabling analysts to detect potential security threats that may not be apparent through standard field statistics alone.

To identify anomalous login locations, the query used `eventstats` to calculate login frequencies, `eval` to compute the proportion of logins from each country per user (`country_freq = logins_by_user_country / logins_by_user`), and `where` to filter rare user-country combinations below a threshold of 0.1 (10%).&#x20;

For example, `kbrown` had 200 total login events, but only one originated from Australia, giving a country frequency of `1/200 = 0.005` (0.5%). This indicates that Australian logins are extremely uncommon for this user, causing the event to fall below the 10% threshold and be flagged as a potential anomaly for further investigation.

**Figure 38.** _Detection of anomalous login locations using frequency-based analysis._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 120622.png" alt=""><figcaption></figcaption></figure>

To detect anomalous login hours, the query calculated each user's average login time (`typical_hour`) and its standard deviation (`stdev_hour`), then computed a z-score (`zscore = |hour − typical_hour| / stdev_hour`) to measure how unusual each login was. For example, `jsmith` logged in at 18:40 (18.67 hours) compared with a typical login time of 13:29 (13.48 hours), resulting in a z-score of approximately 3.01, meaning the login occurred about three standard deviations later than normal and was therefore flagged as anomalous.

**Figure 39.** _Detection of anomalous login times using z-score analysis._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 123425.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. I executed the query `index=vpnlogs | eventstats count as logins_by_user by user | eventstats count as logins_by_user_country by user src_country | eval country_freq=logins_by_user_country/logins_by_user | where country_freq < 0.1 | table _time user src_ip src_country country_freq`, which calculated the login frequency for each user-country pair as `country_freq = logins_by_user_country / logins_by_user`.&#x20;

The query identified **`jsmith`** as the additional outlier, with **Japan (JP)** as the anomalous login location because the user recorded **1 login from Japan out of 200 total logins**, resulting in a frequency of **1/200 = 0.005 (0.5%)**, which fell below the 10% threshold and was therefore flagged as anomalous.

**Figure 40.** _Frequency-based anomaly detection identifying `jsmith` as an outlier._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 124949.png" alt=""><figcaption></figcaption></figure>



2. I executed the query `index=vpnlogs | eval hour=tonumber(strftime(_time,"%H")) + tonumber(strftime(_time,"%M"))/60 | eventstats avg(hour) as typical_hour stdev(hour) as stdev_hour by user | eval zscore=abs(hour-typical_hour)/stdev_hour | where zscore>3 | eval hour=round(hour,2), typical_hour=round(typical_hour,2) | eval stdev_hour=round(stdev_hour,2), zscore=round(zscore,2) | table _time user src_ip src_country hour typical_hour stdev_hour zscore | sort - hour_zscore`, which identified **`njackson`** as the user who suspiciously logged in at **03:17 AM**.&#x20;

The query calculated a **z-score** of **5.49** using the formula **(13.50 − 3.28) ÷ 1.86 = 5.49**, indicating that the login occurred approximately **5.5 standard deviations** earlier than the user's typical login time of **13:30**, thereby classifying it as a significant anomaly.

**Figure 41.** _Z-score analysis identifying `njackson` as having an anomalous login time._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 125648.png" alt=""><figcaption></figcaption></figure>

### Conclusion&#x20;

This room strengthened my understanding of Splunk's Search Processing Language (SPL) by demonstrating how analysts search, filter, transform, enrich, visualize, and correlate log data to support security investigations. I also gained practical experience applying statistical techniques such as frequency analysis and z-scores to detect anomalous user behaviour. From a defensive perspective, these findings reinforce the importance of investigating flagged accounts, validating unusual login patterns, enforcing multi-factor authentication (MFA), and continuously monitoring user activity for signs of compromise.



