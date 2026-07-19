# Splunk Exploration on THM

### **Introduction**

Splunk is a powerful Security Information and Event Management (SIEM) platform that enables analysts to search, analyze, visualize, and investigate log data using its Search Processing Language (SPL).&#x20;

This room covers the core features of SPL, including log filtering, command chaining, data visualization, and anomaly detection to help extract valuable insights from large datasets.

### Search & Reporting

The Splunk Search & Reporting App is the default interface for searching and analyzing indexed data, providing features such as the Search Head, Time Picker, Search History, Data Summary, and Fields Sidebar to help analysts efficiently query, explore, and interpret log data.

To reinforce these concepts, the following exercises involved querying and analyzing the Windows logs dataset using the Splunk Search & Reporting App.

* Executing the query `index=windowslogs` over the **All Time** range returned a total of **12,256 events**.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

* The **SourceIP** field showed that **172.90.12.11** generated the highest number of events in the dataset.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* Filtering the logs for **04/15/2022** between **08:05 AM and 08:06 AM** returned **134 events**.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
