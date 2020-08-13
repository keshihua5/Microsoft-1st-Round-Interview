# Disabling IsInteg Check

Tuesday, September 11, 2018

9:05 AM

------

**Contents**

[Problem(s)](#problems)

[Solution(s)](#solutions)

------

## Problem(s):

- Customer migrations stalled due to **IsInteg not completing** and **times out** after four hrs.

  **IsInteg** is an automatic repair code that fixes corruption in large mailboxes. Auto repair could take a long time. 

  IsInteg was added for **MidSet Corruptions** to ensure missing items do not occur when running migrations. Without using IsInteg, the migrations continue and fail due to missing items during finalization. 

   If there are no midset corruptions, skipping IsInteg may help improve performance.

  Run the repair as follows:

  **`New-MailboxRepairRequest -CorruptionType MessageId`**

  This runs the repair beforehand and not cause it to be hit during migrations.

## Solution(s):

The problem was resolved as follows:

- Added the following workaround to disable this

  `Set-ExchangeSettings MRS -CreateSettingsGroup -GroupName UnblockAlphaVilleUrbanismo -ConfigPairs @(\"DisableAutomaticRepair=true\") -ScopeFilter \"(MailboxGuid -eq \'938ba0f8-9a45-432c-886b-9554bfe232aa\')\" -reason \"CFL 876974 Fatal error JobStuckPermanentException has occurred\" -ExpirationDate 10/01/2018`

  `Set-ExchangeSettings MRS -CreateSettingsGroup -GroupName 1484159UnblockISInteg -ConfigPairs @(\"DisableAutomaticRepair=true\") -ScopeFilter \"(OrganizationName -like \'emiratesfoundationae0.onmicrosoft.com\')\" -reason \"CFL 1484159 Store IsInteg task is pending completion\" -ExpirationDate 06/06/2020`

- Asked the customer to restart the move. See the following log:

  **9/10/2018 12:39:55 PM \[SC1P152MB0910\]**  
  Setting up ISInteg repair run up front for this mailbox since it\'s a large mailbox. \"Primary mailbox size = 14533048984, Archive mailbox size = 0, Large mailbox size threshold config value = 10737418240

  **9/10/2018 12:39:55 PM \[SC1P152MB0910\]**  
  \'MigrationService (on behalf of \'\')\' created move request.

  **9/10/2018 12:39:55 PM \[SC1P152MB0910\]**  
  \'MigrationService (on behalf of \'\')\' allowed a large amount of data loss when moving the mailbox (100 bad items, 100 large items).

  **9/10/2018 12:40:01 PM \[SC1P152MB0509\]**  
  The Microsoft Exchange Mailbox Replication service \'SC1P152MB0509.LAMP152.PROD.OUTLOOK.COM\' (15.20.1122.18 ServerCaps:01FFFFFF, ProxyCaps:1FFFC7FD6DFDBF5FFFFFCB07EFFF, MailboxCaps:, legacyCaps:01FFFFFF) is examining the request.

  **9/10/2018 12:40:01 PM \[SC1P152MB0509\]**  
  Content from the Shard mailbox (Mailbox Guid: 22220007-8bcb-4f6a-b023-168157c847b7, Database: eeabd74b-395d-4a2f-9a7e-2ea59000de3a) will be merged into the target mailbox.

   **9/10/2018 12:40:01 PM \[SC1P152MB0509\]**  
  Connected to target mailbox \'alphavilleurbanismo.onmicrosoft.com\\938ba0f8-9a45-432c-886b-9554bfe232aa (Primary)\', database \'LAMP152DG032-db055\', Mailbox server \'SC1P152MB0509.LAMP152.PROD.OUTLOOK.COM\' Version 15.20 (Build 1122.0).

  **9/10/2018 12:40:03 PM \[SC1P152MB0509\]**  
  Connected to source mailbox \'alphavilleurbanismo.onmicrosoft.com\\938ba0f8-9a45-432c-886b-9554bfe232aa (Primary)\', database \'Q1GB\', Mailbox server \'BRSPDTC0015.alphacorp.local\' Version 15.0 (Build 1263.0), proxy server \'BRSPDTC0015.alphacorp.local\' 15.0.1263.5 ServerCaps:, ProxyCaps:, MailboxCaps:, legacyCaps:0400001F7FFFFFCB07FFFF.

  **9/10/2018 12:40:04 PM \[SC1P152MB0509\]**  
  Started Store IsInteg task for mailbox \'938ba0f8-9a45-432c-886b-9554bfe232aa\'. IsInteg RequestGuid =\'daca1077-2ab7-44bd-bab8-0b5a642b1c59\'.

  **9/10/2018 12:40:35 PM \[SC1P152MB0509\]**  
  Store IsInteg task is pending completion for mailbox \'938ba0f8-9a45-432c-886b-9554bfe232aa\'. IsInteg RequestGuid = \'daca1077-2ab7-44bd-bab8-0b5a642b1c59\'. Percentages complete: 0 .

  **9/10/2018 12:41:05 PM \[SC1P152MB0509\]**  
  Store IsInteg task is pending completion for mailbox \'938ba0f8-9a45-432c-886b-9554bfe232aa\'. IsInteg RequestGuid = \'daca1077-2ab7-44bd-bab8-0b5a642b1c59\'. Percentages complete: 0 .

  **9/10/2018 12:41:35 PM \[SC1P152MB0509\]** 
  Store IsInteg task is pending completion for mailbox \'938ba0f8-9a45-432c-886b-9554bfe232aa\'. IsInteg RequestGuid = \'daca1077-2ab7-44bd-bab8-0b5a642b1c59\'. Percentages complete: 0 .

  **9/10/2018 12:42:06 PM \[SC1P152MB0509\]**  
  Store IsInteg task is pending completion for mailbox \'938ba0f8-9a45-432c-886b-9554bfe232aa\'. IsInteg RequestGuid =

 

 

 
