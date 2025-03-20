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

Investigate incoming emails in the Microsoft Defender portal to identify suspicious or malicious content. Requires Microsoft Defender for Office 365 Plan 2, which includes advanced features like Threat Explorer for threat hunting.

1.	In the Microsoft Security portal (security.microsoft.com), expand "Email & collaboration" in the left menu.
2.	Select "Explorer".
3.	On the "Explorer" page, adjust the time period to view all mail flow from the last 30 days, then click "Refresh". (This displays all incoming and outgoing emails in Threat Explorer.)

<p align="center">
  <img src="https://github.com/user-attachments/assets/d5d1fc6f-e807-4acb-a8d5-f46f11a3c409">
</p>

4.	Review the mail flow and subjects to identify suspicious emails.

<p align="center">
<img src="https://github.com/user-attachments/assets/b8be86da-75a5-4f1c-8baf-8fae51c7d049">
</p>

5.	Locate emails with the subject "You have tasks due today" (flagged as suspicious), then click the subject to investigate further.
6.	In the "You have tasks due today" pane, review the provided details, then click "View header".

<p align="center">
<img src="https://github.com/user-attachments/assets/2816811a-2096-4854-8a8c-2e92901c8604">
</p>

<p align="center">
<img src="https://github.com/user-attachments/assets/dea061c3-de10-4b32-ae51-58906285cc76">
</p>

7.	In the "Email message headers" pane, click "Copy message header".
8.	Open a new browser tab and navigate to the Microsoft Message Header Analyzer (https://mha.azurewebsites.net/).
9.	Paste the copied message header into the text box, then click "Analyze headers".

<p align="center">
<img src="https://github.com/user-attachments/assets/649bc6e4-c098-43bf-80c7-4c03a83836d3">
</p>
 
10.	Review the results. (To classify an email as phishing, check for failed SPF/DKIM/DMARC authentication, mismatched sender domains, or suspicious external server hops.)

## Task 2: Mass delete suspicious emails.

After confirming emails with the subject "You have tasks due today" are phishing attacks, perform a mass deletion using PowerShell. Assumes the Exchange Online PowerShell module is installed.

1.	Open a PowerShell window as Administrator.
2.	If the Exchange Online PowerShell module isn’t installed, enter the following cmdlet to install it:
Install-Module ExchangeOnlineManagement
    -  When prompted "NuGet provider is required to continue", type Y and press Enter.
    -  For the "Untrusted repository" security dialog, type A (Yes to All) and press Enter. (Installation may take a few minutes.)

<p align="center">
<img src="https://github.com/user-attachments/assets/e370826d-d4fb-43d7-80f8-a42472595844">
</p>

<p align="center">
<img src="https://github.com/user-attachments/assets/173a8bd1-a42e-4aee-83a6-d840f170e898">
</p>

3.	Open another PowerShell window (or reuse the existing one after installation) and enter: Connect-IPPSSession
4.	When the "Sign in" window appears, sign in with your Microsoft 365 tenant credentials.
5.	Create a compliance search to locate the phishing emails by entering: $Search = New-ComplianceSearch -Name "Purge phishing messages" -ExchangeLocation All -ContentMatchQuery '(Subject:"You have tasks due today")' (This searches all mailboxes for emails matching the specified subject.)
6.	Start the compliance search by entering: Start-ComplianceSearch -Identity $Search.Identity (This scans all Exchange locations for matching emails.)
7.	Perform a hard delete of the identified emails by entering: New-ComplianceSearchAction -SearchName "Purge phishing messages" -Purge -PurgeType HardDelete
    -  When prompted "Are you sure you want to perform this action?", type Y and press Enter. (This permanently deletes the emails, making them unrecoverable.)
8.	Wait for the deletion to complete. (You can verify removal by checking the Explorer page in the Microsoft Defender portal.)

<p align="center">
<img src="https://github.com/user-attachments/assets/62c5be1f-9939-42b4-b716-8d7b17d65c82">
</p>
