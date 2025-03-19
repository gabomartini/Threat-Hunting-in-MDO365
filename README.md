<p align="center">
  <img src="https://github.com/user-attachments/assets/e74f1503-3da6-4730-b467-902bfa130178" width="150" height="auto">
  <h1 align="center">Threat Hunting in Microsoft Defender for Office 365</h1>
</p>

#### *In this tutorial, we will:*
*•	Search for and locate malicious emails using Microsoft Defender.*

*•	Utilize PowerShell to mass delete malicious emails from an organization.*

#### Tasks:
 1. Examine suspicious emails.
 2. Mass delete suspicious emails.

## Task 1: Examine suspicious emails.

Investigate incoming emails in the Microsoft Defender portal to identify suspicious and malicious content. To use Threat Hunting in Microsoft Defender for Office 365, your organization needs to have Microsoft Defender for Office 365 Plan 2. This plan includes advanced threat protection features such as Threat Explorer and Real-time detections, which are essential for threat hunting activities.

1.	In the Microsoft Security portal expand Email & collaboration then select Explorer.
2.	On the Explorer page adjust the time period of the query to view all mail-flow of the last 30 days and select Refresh

<p align="center">
  <img src="https://github.com/user-attachments/assets/d5d1fc6f-e807-4acb-a8d5-f46f11a3c409">
</p>

3.	The result shows you all incoming and outgoing mails. Investigate the mail-flow and the subject of the mails.

<p align="center">
<img src="https://github.com/user-attachments/assets/b8be86da-75a5-4f1c-8baf-8fae51c7d049">
</p>

4.	Mails with the subject You have tasks due today appear suspicious, select the subject to do further investigation.
5.	On the new appeared You have tasks due today pane view provided information and select View header.

<p align="center">
<img src="https://github.com/user-attachments/assets/2816811a-2096-4854-8a8c-2e92901c8604">
</p>

<p align="center">
<img src="https://github.com/user-attachments/assets/dea061c3-de10-4b32-ae51-58906285cc76">
</p>

6.	On the Email message headers pane select Copy message header and select Microsoft Message Header Analyzer, a new tab will open in your browser.
7.	On the Message Header Analyzer page paste the message Header and select Analyze headers.

<p align="center">
<img src="https://github.com/user-attachments/assets/649bc6e4-c098-43bf-80c7-4c03a83836d3">
</p>
 
8.	Review the outcome (to classify an email as phishing, look for failed SPF/DKIM/DMARC, mismatched sender domains, or suspicious external server hops in the header).

## Task 2: Mass delete suspicious emails.

In this case we conclude that emails with the subject "You have tasks due today" are phishing attacks. We will remedy by performing a mass deletion of these emails using PowerShell (Exchange Online PowerShell module must already be installed).

1.	Open a PowerShell window as Admin and enter the following cmdlet to install the latest Exchange Online PowerShell module version: **Install-Module ExchangeOnlineManagement** (This module allows you to manage Exchange Online settings and perform various administrative tasks using PowerShell).

<p align="center">
<img src="https://github.com/user-attachments/assets/e370826d-d4fb-43d7-80f8-a42472595844">
</p>

2.	After running this command, you will be prompted with: NuGet provider is required to continue Type Y to proceed.
3.	Confirm the untrusted repository security dialog with A for "Yes to All" and press Enter. This process may take some time to complete.

<p align="center">
<img src="https://github.com/user-attachments/assets/173a8bd1-a42e-4aee-83a6-d840f170e898">
</p>

4.	Open an another PowerShell window and enter the following cmdlet to to establish a remote session to the Security & Compliance Center in Microsoft 365: **Connect-IPPSSession**
5.	When the Sign in window is displayed, sign in with your tenant credentials and enter the following cmdlet to perform content search: **$Search=New-ComplianceSearch -Name "Purge phishing messages" -ExchangeLocation All -ContentMatchQuery '(Subject:"You have tasks due today")'** (The command creates a compliance search named "Purge phishing messages" that searches all mailboxes in the organization for emails with the subject "You have tasks due today.").
6.	Now enter this cmdlet: **Start-ComplianceSearch -Identity $Search.Identity** (The search will begin scanning all specified Exchange locations for emails that match the content match query defined earlier).
7.	Finally enter the followng cmdlet to hard delete messages: **New-ComplianceSearchAction -SearchName "Purge phishing messages" -Purge -PurgeType HardDelete** (This action will remove the emails with the subject "You have tasks due today" from all mailboxes in the organization, and they will be permanently deleted and unrecoverable).
8.	After running this command, you will be prompted with: Are you sure you want to perform this action? Type Y to proceed.

<p align="center">
<img src="https://github.com/user-attachments/assets/62c5be1f-9939-42b4-b716-8d7b17d65c82">
</p>
