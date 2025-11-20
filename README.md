# Splunk DNS Log Analysis

## Objective
This project demonstrates how to analyze DNS log files using Splunk Enterprise. The goal was to ingest DNS logs, parse them correctly, create custom fields, and identify any suspicious DNS activity.  

---

## Environment Setup
- Installed **Ubuntu** on Oracle VirtualBox.  
- Installed **Splunk Enterprise** on the Ubuntu VM.  
- Downloaded sample DNS log file (zipped).  
- Unzipped the log file via terminal:
- gunzip dns.log.gz

Splunk Configuration
props.conf

I created props.conf file to define how Splunk interprets the incoming data:

[dns_logs]
TIME_PREFIX = ^
TIME_FORMAT = %a %b %d %H %M %S %Y
MAX_DAYS_AGO = 36500
SHOULD_LINEMERGE = false

  Placed in:

/opt/splunk/etc/apps/search/local/

    Set proper ownership for Splunk:

sudo chown splunk:splunk /opt/splunk/etc/apps/search/local/props.conf
<img width="593" height="366" alt="Screenshot From 2025-11-20 02-44-32" src="https://github.com/user-attachments/assets/d1e1c945-80e1-4ea8-8cf3-0ae0bd2d91fc" />

Data Ingestion

    Opened Splunk > Settings > Add Data.

    Uploaded dns.log file with source type set to dns_logs.

    Ran a test search to verify data parsing:

index=main sourcetype=dns_logs

<img width="1843" height="715" alt="Screenshot From 2025-11-20 02-42-28" src="https://github.com/user-attachments/assets/03bc2bd9-e458-4af7-9ba3-4fdf4f061aef" />

Field Extraction

Custom fields were created to enhance analysis:

    src_id

    dst_id

    fqdn

    record_type

Analysis
Top DNS Queries

index=* sourcetype=dns_logs | stats count by fqdn
<img width="1839" height="346" alt="Screenshot From 2025-11-20 03-11-03" src="https://github.com/user-attachments/assets/69c701a9-1acc-41b4-92e5-c722e2d724db" />

    The FQDN with the highest count was:

44.206.168.192.in-addr.arpa

Investigating Suspicious Source IP

index=* sourcetype=dns_logs fqdn="44.206.168.192.in-addr.arpa" | table src_ip | dedup src_ip

    Suspicious source IP found: 102.168.202.83

Threat Assessment

    VirusTotal check: FQDN is clean.

    No evidence of attack or tampering.

    Observation: High number of DNS queries may indicate misconfiguration.

  Summary:
Field	Value
Suspicious FQDN	44.206.168.192.in-addr.arpa
Source IP	102.168.202.83
Query Count	679
Threat	Likely misconfiguration, no confirmed attack
