# Find the Anomaly

## Scenario
The Security Operation Center(SOC) Team handed me a ticket to scour the Log Analytics workspace to find the odd activity within it. I need to find the 5 flags and report it back to the team.

## Environment
Live multi-user Azure training tenant, Reader access

## Investigation
**1**. I have been handed a ticket titled "SOC-1058" with no context, so in the Log Analytics workspace, I run the KQL query search "SOC-1058". The result is what the night shift team left behind as useful information.
<img width="364" height="168" alt="image" src="https://github.com/user-attachments/assets/4fd2e5a9-40ab-4034-a229-e4319a2defe8" />

**2**. The SOC team provided information that there was a bunch of sign-in failures across users, but there is one specific user that stood out amongst the others. From the query I used, I was able to produce a list of the sign in failures and found the one specific user that had the most sign-in failures.
<img width="374" height="246" alt="image" src="https://github.com/user-attachments/assets/c7f1bf0b-fd4d-4429-a567-36e8d6222684" />

**3**. With that user's UPN, I then used another KQL query to check for any successful sign-ins after their failures. There was 60 failures and one successful login.
<img width="838" height="674" alt="image" src="https://github.com/user-attachments/assets/ab0febe0-7d94-45d9-a4f1-21f9234af3b8" />

**4**. Now that we have found the user that managed to sneak their way in, I proceeded to look at what resources they decided to rummage through. I went into the storage account in the resource group to check if there is any suspicious activity and found that someone has disabled the Diagnostic Settings for this storage account. This means I need to go check the logs in AzureActivity to see when they changed the values.
<img width="639" height="151" alt="image" src="https://github.com/user-attachments/assets/e891dadb-ff74-4454-a54b-7951cba732bf" />

**5**. The AzureActivity showed that the OperationName had been destroyed so I had to look for the ResourceID. This pretty much confirms that the attacker breached and destroyed the diagnostics setting which led to the SOC team not able to have visibility in this storage account. 
<img width="751" height="332" alt="image" src="https://github.com/user-attachments/assets/66fc109f-de40-4180-b156-b371c24535f7" />

**6**. Next, I wanted to figure out why no one was alerted of this breach. I move over to the alert rules and looked at the conditions set. The picture below shows why it failed. The conditions to catch failures was set way too high when this attacker got in with 60.
<img width="161" height="14" alt="image" src="https://github.com/user-attachments/assets/1e39c866-b625-4a2a-b210-c726f4819836" />

**7**. I reported all this information to the SOC team so that they can adjust the sign-in failure rule so that this type of issue doesn't happen again. They can also monitor the Workbook titled "Sign-in Anomaly" to catch the spike of sign-in failures.
<img width="827" height="412" alt="image" src="https://github.com/user-attachments/assets/94ee2dd1-fd68-4807-8b0d-b22a2f62c271" />

## What broke / what surprised me
1. I had a little bit of trouble working towards the correct KQL query to find the AzureActivity table, but once I figured it out, my SQL knowledge helped pull me through. 

## Findings and recommendations
Fine tuning and testing alert rules is very helpful to identify gaps of attackers that can get through easily.

## What I learned
1. Once again, it really is just small mistakes that lead up to a big mistake like a snowball rolling down a hill.
2. There are other ways to monitor such as using the Workbooks. It seems like whoever was on the SOC team decided to ignore it or they didn't know about it.
