# XAM SMTP Internet - Enterprise (EXO + EOP)

Monday, October 10, 2016

10:28 AM

------

**Contents**

[Monitor Goals](#monitor-goals)

[What Does The Probe Do?](#what-does-the-probe-do)

[What Triggers The Alert?](#what-triggers-the-alert)

[Possible Root Causes](#possible-root-causes)

[Link To OSP](#link-to-osp)

[Diagnose and Recover](#diagnose-and-recover)

[Diagnose EOP Issues Only](#diagnose-eop-issues-only)

------

## Monitor Goals

Monitor the path of mail transmission through internet submission of mail to EOP FDs through EXO HUBs. This probe runs/alerts on the following scopes:

- **EXO Dag scoped**  
  This alerts when Dag-level availability declines.

- **FOREST-level RED ALERT**  
  A **system-wide Incident/red alert** is issued when across a forest availability dips for any reason.

- **EOP FOREST scoped**  
  This alerts on a EOP Forest-level instead of conventional EXO focused scope. It includes issues faced until EOP FD successfully proxies the incoming mail to EXO HUB.

## What Does The Probe Do?

![XAM_SMTP](images/XAM_SMTP.png)

Each probe executes the following steps:

1.  Resolve MX of monitoring tenant.

2.  Connect to monitoring tenant MX host (this should resolve to an EOP FD).

3.  Negotiate an SSL connection.

4.  Submit email to monitoring mailbox.

## What Triggers The Alert?

This monitor targets a specific **Dag/EXO forest/EOP orest**. An alert is raised when the percentage of successful probes in the last hour **drops below 97.5%** (monitoring profile specific threshold).

## Possible Root Causes

- **GLS issue**  
    Cannot attribute mail because GLS is not accessible or monitoring tenant cannot not be found in GLS.

- **Networking issue**  
    Cannot resolve tenant MX (DNS++ issue), VIP not configured correctly, connectivity issues between Azure instance, EOP FD, EXO Café and/or EXO hub, etc.

- **Certificate issues**  
    Wrong or expired certificate, monitoring mailbox not provisioned/configured correctly.

- **Transport issues**  
    EOP FD, EXO Café or hub are too busy or in a bad state (i.e., crashing/hanging), or there is a  code bug in any of these transport components.

## Link To OSP

[EXO Dag Scoped (XAM SMTP - Internet)](https://o365pulse.office.net/enterprisedashboard?probe=SMTP%20Internet%20-%20Enterprise&environment=Prod&scope=*.*.*>)

[EOP Forest Scoped (XAM SMTP EOP - Internet)](https://o365pulse.office.net/enterprisedashboard?probe=SMTP%20Internet%20-%20Enterprise%20EOP&environment=Prod&scope=*.*.*)

## Diagnose and Recover

1. Determine prominent failure reason (Error Type) and Smtp error responses.

   - Check the bottom of the alert mail table to find the major cause of failure (prominent error type) or OSP page link in email
   - Identify the components involved in the failure. for example:
     - UnableToConnect - Azure and EOP FD
     - ReplicationFailure - EXO Hub
     - ProxyDnsFailure - EOP FD, EXO Café
     - ProbeTimeout - Check if majority of last response is \"**250 Recipient OK**\" in OSP - EOP FD, EXO Hub
       - Use **`> Get-NetworkConfig.ps1 -Server CY1USG02FT013 -PortConnectivity \[2001:489a:2200:408::3\]:25 -ShowConnectionResponse`** to see if the connection can be established and if the banner can be read successfully.  
         **Port 25 Connectivity Status: Passed uses 12.803 ms  
         Cannot get remote side answer**.

   - Prominent failure reasons can be found in the body of the alert email or in OSP.  
See [Outside-In probe prominent failure reason](https://msft.spoppe.com/collab/transportalerts/SiteAssets/Transport%20Alert%20Pulse%20Notebook/On-Call%20Notes.one#Outside-In%20probe%20prominent%20failure%20reason&section-id={C84CBF30-BD89-4D02-A63C-D66A3C8403E0}&page-id={215D26BD-9788-4816-8DE1-04294A12552C}&end) for more information.
     
   
   **Mitigation steps and description of each failure reason / error type / smtp response**.  
   Once you\'ve determined the failure reason / error type, see the following pages for added information on the error type:  

   [Common Outside In SMTP Probe Failures](https://msft.spoppe.com/collab/transportalerts/SiteAssets/Transport%20Alert%20Pulse%20Notebook/E15%20Alert%20Playbook.one#Common%20Outside%20In%20SMTP%20Probe%20Failures&section-id={24B57828-2A06-42C5-99C0-37E103E281E0}&page-id={BA12E5C0-D8F3-49F8-ACB4-D55F08B53513}&end)
   
   [Understand Outside-In Class of Failures](https://msft.spoppe.com/collab/transportalerts/SiteAssets/Transport%20Alert%20Pulse%20Notebook/On-Call%20Notes.one#Understand%20Outside-In%20Class%20of%20Failures&section-id={C84CBF30-BD89-4D02-A63C-D66A3C8403E0}&page-id={080E02F8-A55A-4EAC-8701-C909A5D90CA4}&end)
   
2. Retreive probe smtp sessions from protocol logs.

   - After identifying the major failure areas, choose one error (from OSP) to get the protocol logs
     - Use Ehlo domain used for the probe
     - Pick up time of the probe
     - Get Protocol logs

   - Use On-call script to diagnose the logs

   - Help for using the script: [Get a XAM Probe Protocol logs](onenote:https://msft.spoppe.com/collab/transportalerts/SiteAssets/Transport%20Alert%20Pulse%20Notebook/On-Call%20Notes.one#Get%20a%20XAM%20Probe's%20Protocol%20logs&section-id={C84CBF30-BD89-4D02-A63C-D66A3C8403E0}&page-id={E7C6A793-74A3-42BB-94CA-D07C3FD96425}&end)

## Diagnose EOP Issues Only

Smtp Internet - Enterprise workflow starts an SMTP conversation with EOP Fd in a sequence. EOP FD sets up a proxy connection to EXO Café and proxies the SMTP commands on BDAT.

Issues **pre** BDAT command are specifically EOP only issues.

Issues **post** BDAT command can be differentiated between EOP, and EXO related:

-   **EOP related issues**  
    Setting up a proxy (Error Type PROXYDNSFAILURE)

-   **EXO related issues**  
     Hub server busy/replication failure

-   **EOP/EXO only issues**  
    To further identify EOP/EXO only issues, do the following steps

    1. Go to OSP.

    2. Look at the EOP Forest-level failure scope, and check for Dag-level availability on the right-hand column of the  OSP health page.

    3. If errors are concentrated on a Dag, check SMTP Internet availability - Enterprise (Dag).

    4. If errors are distributed over multiple Dags, verify that the prominent error type happens before the **BDAT** command, or if it is a proxy connection failure.
