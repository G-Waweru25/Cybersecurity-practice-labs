# Splunk Exploration on THM

### **Introduction**

Splunk is a powerful Security Information and Event Management (SIEM) platform that enables analysts to search, analyze, visualize, and investigate log data using its Search Processing Language (SPL).

This room will cover the fundamentals of Splunk's Search Processing Language (SPL) and how analysts use it to derive actionable insights from massive amounts of log data, such as log filtering, command chaining, data visualization, and anomaly detection.

### Search & Reporting

The Splunk Search & Reporting App is the default interface for searching and analyzing indexed data, including the Search Head, Time Picker, Search History, Data Summary, and Fields Sidebar, which enable analysts to efficiently search, explore, and interpret log data.

#### Questions

To reinforce these concepts, the following exercises involved querying and analyzing the Windows logs dataset using the Splunk Search & Reporting App.

1. To view all indexed Windows log events, the following search was performed:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs
```
{% endcode %}

The search returned a total of **12,256** events.

**Figure 1.** _Search results showing all events in the windowslogs index._

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

2. The following figure depicts the SourceIP field that showed that 172.90.12.11 generated the highest number of events in the dataset.

**Figure 2.** _The SourceIp field distribution in the windowslogs dataset._

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

3. Filtering the logs for **04/15/2022** between **08:05 AM and 08:06 AM** returned **134 events.**

**Figure 3.** _Time range filter configured for 15 April 2022 between 08:05 AM and 08:06 AM._

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

**Figure 4.** _Filtered search results were returned for the selected time period._

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

### Search Operators

Splunk's Search Processing Language (SPL) enables users to search, filter, transform, and analyze ingested log data, with free-text searches providing a simple way to locate events containing specific keywords when field names are unknown.

After exploring free-text searches, The task illustrated the use of relational operators for targeted log filtering. The query below demonstrated the use of the `!=` relational operator to retrieve all log events where the `AccountName` field did not match `SYSTEM`.

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs AccountName!=SYSTEM
```
{% endcode %}

**Figure 5.** _Search results excluding events associated with the `SYSTEM` account._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 143315.png" alt=""><figcaption></figcaption></figure>

The task further also illustrated logical operators such as `AND`, `OR`, `IN`, and `NOT` for constructing compound search conditions. The following searches were done to compare explicit and implicit use of the AND operator:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs AccountName!=SYSTEM AND AccountName=James
```
{% endcode %}

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs AccountName!=SYSTEM AccountName=James
```
{% endcode %}

Both searches yielded the same results, showing that adjacent search terms are seen as an implicit AND.

**Figure 6.** &#x52;_&#x65;sults of the query using the explicit `AND` operator._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 144405.png" alt=""><figcaption></figcaption></figure>

**Figure 7.** _Results of the equivalent search using implicit `AND` behaviour._

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Splunk supports wildcards (`*`) for searching partial text matches, as illustrated by the query below, which retrieved events with destination IP addresses beginning with `172`.

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs DestinationIp=172*
```
{% endcode %}

**Figure 8.** _Wildcard search returning destination IP addresses beginning with `172`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 151036.png" alt=""><figcaption></figcaption></figure>

Additionally, Splunk uses quotation marks (`""`) to search for exact phrases or to treat multiple words as a single search value, and parentheses (`()`) are used to group search conditions, or to control the order of evaluation of logical operators, so that complex queries return the desired results.

#### Questions

Subsequently, I did a practical exercise using the search operators to filter and analyze Windows event logs.

1. Firstly, I executed the query:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs EventID=4624 | stats count 
```
{% endcode %}

to retrieve events with the `EventID` value `4624`.

**Figure 9.** _Event count for Windows logs with `EventID` 4624._

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

2. Secondly, I executed the query:&#x20;

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs DestinationIp="172.18.39.6" DestinationPort=135 | stats count 
```
{% endcode %}

to determine the number of events with the destination IP address `172.18.39.6` and destination port `135`, which returned a total of **4** events.

**Figure 10.** _Events matching destination IP `172.18.39.6` and destination port `135`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 153323.png" alt=""><figcaption></figcaption></figure>

3. Thirdly, I ran the query:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs Hostname=Salena.Adam DestinationIp=172.18.38.5
```
{% endcode %}

and found that the source IP address with the highest event count was **`172.90.12.11`**.

**Figure 11.** _Search results identifying the most frequent `SourceIp` value._

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

4. Fourthly, I used wildcard searching by issuing the following query, which produced 12,256 matching events from the `windowslogs` index.

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs cyber*
```
{% endcode %}

**Figure 12.** _Wildcard search results for events containing the term `cyber*`._

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

5. Lastly, I identified the `AND` operator as having the lowest priority in Splunk search evaluation, as captured below

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 154927.png" alt=""><figcaption></figcaption></figure>

### Filtering Results

Analysts can use a series of pipe (`|`) statements to execute multiple operations on a large volume of log data, which can help them progressively filter, refine, and organize the data into focused and meaningful search results for efficient analysis.

The `fields` command makes search results easier to read by only showing the necessary fields.

```spl
index=windowslogs | fields host User SourceIp
```

TThe results displayed only the `host,` `User,` and `SourceIp` fields.

**Figure 13.** _Output of the `fields` command displaying selected log fields._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-24 160928.png" alt=""><figcaption></figcaption></figure>

Building on field selection, the `dedup` command removes duplicate field values to return only unique results, as demonstrated by the query:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | fields EventID User Image Hostname SourceIp | dedup SourceIp, 
```
{% endcode %}

which returned 7 events with a unique `SourceIp`.

**Figure 14.** _Results of the `dedup` command showing unique `SourceIp` values._

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

The `rename` command can make search results more readable by changing the field names, as shown in the query:

{% code overflow="wrap" expandable="true" %}
```spl
 index=windowslogs | fields EventID User Image Hostname SourceIp | rename User as Employee
```
{% endcode %}

where the `User` field was renamed to `Employee`.

**Figure 15.** _Output of the `rename` command renaming the `User` field to `Employee`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 143113.png" alt=""><figcaption></figcaption></figure>

To show the use of the rename command, I ran the query:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs
| rename Hostname AS Computer SourceIp AS Source_IP EventID AS Event_ID
| table Computer Source_IP Event_ID User
```
{% endcode %}

that renamed Hostname to Computer, SourceIp to Source\_IP, and EventID to Event\_ID in order to make the search results readable and presentable, as captured below.

**Figure 16.** _Renamed Hostname as Computer_

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144521.png" alt=""><figcaption></figcaption></figure>

**Figure 17.** _Renamed SourceIp as Source\_IP_

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144537.png" alt=""><figcaption></figcaption></figure>

**Figure 18.** _Renamed EventID as Event\_ID_

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 144530.png" alt=""><figcaption></figcaption></figure>

To perform pattern matching instead of exact-value searching, the query:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | regex Image="\.exe$" 
```
{% endcode %}

was used to retrieve events with `Image` values ending in the `.exe` extension.

**Figure 19.** _Regular expression search returning executable images ending with `.exe`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-26 145535.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. To demonstrate the practical application of the `fields` command, the query

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | fields + Domain SourceProcessId TargetProcessId, 
```
{% endcode %}

displayed the selected fields and showed that the highest `SourceProcessId` value was **9496**.

**Figure 20.** _Selected `Domain`, `SourceProcessId`, and `TargetProcessId` fields._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 123320 (1).png" alt=""><figcaption></figcaption></figure>

2. Furthermore, using the query:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | regex TargetObject="Manager$" | stats count by TargetObject, 
```
{% endcode %}

pattern matching was applied to the `TargetObject` field, revealing that&#x20;

{% code overflow="wrap" expandable="true" %}
```reg
HKLM\SOFTWARE\Microsoft\SecurityManager 
```
{% endcode %}

had the highest event count.

**Figure 21.** _Results of the regular expression query matching `TargetObject` values ending in `Manager`_.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 125558.png" alt=""><figcaption></figcaption></figure>

### Structuring Results

Splunk offers several commands for structuring and organizing search results, which help analysts present log data in a more meaningful and clear way.

The `table` command formats the output of the search results in a structured and readable way, showing only selected fields, as illustrated by the query:&#x20;

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | table _time EventID Hostname SourceName
```
{% endcode %}

which presented the specified fields in a tabular view.

**Figure 22.** _Output of the `table` command displaying selected event fields._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 131015.png" alt=""><figcaption></figcaption></figure>

In addition to the `table` command, Splunk also has structuring commands like `head, tail, sort`, and `reverse` that are used to restrict, order, and reorganize the search results to improve log analysis.

To reconstruct the sequence of system activities, the query:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs Hostname=Salena.Adam | table _time Hostname EventID Category | reverse 
```
{% endcode %}

organized events for the Salena.Adam host into a chronological timeline.

**Figure 23.** _Chronological timeline of events for the `Salena.Adam` host._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 133848.png" alt=""><figcaption></figcaption></figure>

Splunk subsearches utilize the join command to correlate events across multiple data sources based on a common field, so that analysts can add related information from disparate logs to their search results. The example below shows how Security logon events (EventID=4624) were joined to Sysmon process creation events (EventID=1) to create a single data view of Security logon events with process details as well as logon type and IP address.

**Figure 24.** _Correlated Sysmon and Security events using a Splunk subsearch and `join`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 134155.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. The query below when used with the table command in the practical task returned the table structure and `SYSTEM` as the first `AccountName`.

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | table EventID AccountName AccountType
```
{% endcode %}

**Figure 25.**  _Structured table showing `EventID`, `AccountName`, and `AccountType`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 172141.png" alt=""><figcaption></figcaption></figure>

2. Building on the previous task, the query:&#x20;

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | table EventID AccountName AccountType | reverse 
```
{% endcode %}

was used to reverse the order of the tabulated results, with `EventID` **800** appearing first.

**Figure 26.** _Reversed table output with `EventID` 800 appearing first._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 172835.png" alt=""><figcaption></figcaption></figure>

3. This query produced a chronological list of process creation events:

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs EventID=1 | table _time ParentProcessId ProcessId ParentCommandLine CommandLine | reverse
```
{% endcode %}

which revealed from the `ParentCommandLine` field that the command&#x20;

{% code overflow="wrap" expandable="true" %}
```cmd
net user /add A1berto paw0rd1 
```
{% endcode %}

was executed, revealing that the password assigned to the user `A1berto` was `paw0rd1`.

_Figure 27. Timeline revealing the `net user /add` command used to create the `A1berto` account._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 173400.png" alt=""><figcaption></figcaption></figure>

### Transforming commands

Transforming commands in Splunk transform raw log events into summarized stats and visualization.

#### General Transformational Commands

Commands such as `top` and `rare` identify the most and least frequent field values to facilitate pattern analysis.

The `highlight` command is used to visually emphasize specified fields or text within raw search results, as demonstrated by the query,&#x20;

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | highlight User EventID Image "Process accessed"
```
{% endcode %}

which highlighted the selected values to make relevant log information easier to identify during analysis.

**Figure 28.** _Highlighted search results emphasizing selected fields and text._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 132636.png" alt=""><figcaption></figcaption></figure>

The `stats` command summarizes search results by performing statistical calculations, with the query

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | stats count by EventID | sort EventID 
```
{% endcode %}

grouping events by `EventID`, counting their occurrences, and displaying the results in ascending order.

**Figure 29.** _Statistical summary of Windows events grouped by `EventID`._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 132829.png" alt=""><figcaption></figcaption></figure>

The `chart` command aggregates search results into a format suitable for visualization, with the query&#x20;

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | chart count by User 
```
{% endcode %}

summarizing the number of events associated with each user.

**Figure 30.** _Chart showing the distribution of events by user._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 133001.png" alt=""><figcaption></figcaption></figure>

#### Data Enrichment and Field Manipulation

To determine trends in process activity, the query,

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs Image!="" | timechart span=30m count by Image limit=5 
```
{% endcode %}

employed the `timechart` command to visualize the five most common process images over successive 30-minute periods.

**Figure 31.** _Timechart visualizing the five most common process images over time._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-27 230526.png" alt=""><figcaption></figcaption></figure>

The `iplocation` command enriches search results by adding geographic information to IP addresses, as demonstrated by the query:

{% code overflow="wrap" expandable="true" %}
```spl
 index=windowslogs | iplocation SourceIp | stats count by Country, 
```
{% endcode %}

which grouped events according to the country associated with each source IP address.

**Figure 32.** _Geographic distribution of source IP addresses using the `iplocation` command._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 134912.png" alt=""><figcaption></figcaption></figure>

The `lookup` command enriches search results by matching fields with data stored in external lookup tables, as illustrated by the query,

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | lookup user_roles Hostname OUTPUT UserRole | stats count by Hostname UserRole
```
{% endcode %}

which associated each `Hostname` with its corresponding `UserRole` and summarized the results.

**Figure 33.** Lookup results associating hostnames with corresponding user roles.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 135909.png" alt=""><figcaption></figcaption></figure>

Furthermore, the eval command allows to add new fields and to modify existing ones by calculation and using conditional expressions as explained in the query.

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | eval LogonTypeDesc=case(LogonType==3,"Network Logon",LogonType==5,"Service") | stats count by LogonType LogonTypeDesc
```
{% endcode %}

which converted numeric `LogonType` values into descriptive labels before summarizing the results.

**Figure 34.** _Descriptive logon types generated using the `eval` command._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 140328.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. I applied the `top` command by running the query&#x20;

{% code overflow="wrap" expandable="true" %}
```
index=windowslogs | top Image
```
{% endcode %}

&#x20;where I identified&#x20;

{% code overflow="wrap" expandable="true" %}
```cmd
C:\windows\system32\svchost.exe 
```
{% endcode %}

as the most common `Image` value.

**Figure 35.** _Top executable images ranked by occurrence._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-14 141233.png" alt=""><figcaption></figcaption></figure>

2. I leveraged the query,&#x20;

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | iplocation SourceIp | stats count by Region 
```
{% endcode %}

to infuse geographic information into the source IP addresses, which revealed California as the region of origin for the events.

**Figure 36.** _Source IP addresses grouped by geographic region._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 012045.png" alt=""><figcaption></figcaption></figure>

3. The query,

{% code overflow="wrap" expandable="true" %}
```spl
index=windowslogs | lookup image_riskscore Image OUTPUT RiskScore | stats count by Image RiskScore | sort - RiskScore 
```
{% endcode %}

allowed me to map executable images to an external risk score look-up table which identified

{% code overflow="wrap" expandable="true" %}
```ps1
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe 
```
{% endcode %}

as the highest-risk image.

**Figure 37.** _Executable images enriched with external risk scores using a lookup table._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 012641.png" alt=""><figcaption></figcaption></figure>

### Anomaly Detection

Anomaly detection in Splunk can recognize unusual or suspicious events that deviate from normal patterns, helping analysts detect potential security threats that may not be evident from the usual field statistics.

To identify anomalous login locations, the query used `eventstats` to calculate the number of logins per IP address, `eval` to compute the percentage of logins for each country per user (`country_freq = logins_by_user_country / logins_by_user`), and where to filter infrequent country-user combinations (country\_freq < 0.1, or 10%).

For instance, kbrown had 200 login events in total, with one event from Australia, so the country frequency is `1/200 = 0.005` (0.5%). This indicates that Australian logins are extremely uncommon for this user, causing the event to fall below the 10% threshold and be flagged as a potential anomaly for further investigation.

**Figure 38.** _Detection of anomalous login locations using frequency-based analysis._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 120622.png" alt=""><figcaption></figcaption></figure>

To detect anomalous login hours, the query computed the average time a user logs in (`typical_hour`) and the standard deviation of their login time (`stdev_hour`) and then calculated a zscore (`zscore = |hour − typical_hour| / stdev_hour`) that measures the deviation from the average logon time compared to the standard deviation. For instance, `jsmith` logged in at 18:40 (18.67 hours) compared to typical login time is 13:29 (13.48 hours), resulting in a z-score of approximately 3.01, meaning the login occurred about three standard deviations later than normal and was therefore flagged as anomalous.

**Figure 39.** _Detection of anomalous login times using z-score analysis._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 123425.png" alt=""><figcaption></figcaption></figure>

#### Questions

1. I used the query

{% code overflow="wrap" expandable="true" %}
```spl
index=vpnlogs | eventstats count as logins_by_user by user
| eventstats count as logins_by_user_country by user src_country
| eval country_freq=logins_by_user_country/logins_by_user
| where country_freq < 0.1
| table _time user src_ip src_country country_freq
```
{% endcode %}

which computed the number of logins by country per user, country\_freq = logins\_by\_user\_country / logins\_by\_user.

The query identified jsmith as the additional outlier, with **Japan (JP)** as the anomalous login location ,since the user logged in 1 time in Japan (JP) out of 200 logins a frequency of 0.005 (0.5%) was below the 10% threshold, which was flagged as the anomalous.

**Figure 40.** _Frequency-based anomaly detection identifying `jsmith` as an outlier._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 124949.png" alt=""><figcaption></figcaption></figure>



2. I issued the query

{% code overflow="wrap" expandable="true" %}
```spl
index=vpnlogs
| eval hour=tonumber(strftime(_time,"%H")) + tonumber(strftime(_time,"%M"))/60
| eventstats avg(hour) as typical_hour stdev(hour) as stdev_hour by user
| eval zscore=abs(hour-typical_hour)/stdev_hour | where zscore>3
| eval hour=round(hour,2), typical_hour=round(typical_hour,2)
| eval stdev_hour=round(stdev_hour,2), zscore=round(zscore,2)
| table _time user src_ip src_country hour typical_hour stdev_hour zscore
| sort - hour_zscore
```
{% endcode %}

which identified **njackson** as the user who suspiciously logged in at **03:17 AM.**

The query calculated a z score of 5.49 units, based on the formula **(13.50 − 3.28) ÷ 1.86 = 5.49**, which means that the login took place about 5.5 standard deviations earlier than the typical login time 13:30, therefore considered as a significant anomaly.

**Figure 41.** _Z-score analysis identifying `njackson` as having an anomalous login time._

<figure><img src="../.gitbook/assets/Screenshot 2026-07-28 125648.png" alt=""><figcaption></figcaption></figure>

### Conclusion&#x20;

This room deepened my knowledge of Splunk's Search Processing Language (SPL), and how analysts search, filter, transform, enrich, visualize and correlate log data to aid in security investigations. Additionally, I have had hands-on experience in implementing statistical methods like frequency analysis and z-scores to identify unusual user activity. On the defensive side, the results highlight the need to perform deep dives on flagged accounts, validate suspicious logins, implement multi-factor authentication (MFA), and stay vigilant for user activity that may indicate compromise.



