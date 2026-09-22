# Privileged Audit
## Scenario
2 to 3 sentences. What was the situation and what question did the investigation answer? Frame it like a work ticket, not homework.
I was tasked with auditing all the Role Assignments in the current Azure Directory to check for redundancy and to adhere to the principal of least-privilege. 


## Environment
Live multi-user Azure training tenant, Reader access

## Investigation
1. My first step was to download the role assignments and export them into CSV or JSON format. Since I did not have the right access privilege to do this in Azure, I had to look through the Role Assignment.csv provided to me and discovered a lot of redundant Owner roles provided to Carl.
<img width="1261" height="752" alt="image" src="https://github.com/user-attachments/assets/5f11170a-588d-4f87-bdb9-b50e048ff529" />

2. Then I went into the Azure Portal to access the CLI to enumerate the rest of the unknown deleted accounts. Through my comparison with the CLI and provided JSON with the same information, I discovered that the User Principal Name was blank. This means that there is an unknonwn deleted account that I need to look more into.
Insert picture of CLI command with role export if it worked properly.

4. With the information I gathered above, I head to the Azure Resource Graph and use the KQL query below to scout for compromised or orphaned accounts. 
"authorizationresources
| where type =~ 'microsoft.authorization/roleassignments'
| extend principalId = tostring(properties.principalId)
| extend description = properties.description
| where principalId == '<insert orphaned principal ID>'
| project name, principalId, principalType = properties.principalType, scope = properties.scope, description"
Insert picture of query results that should have appeared.

5. There was another method to confirm who was the deleted person so I navigated to Privileged Identity Management to export assignments and was able to find the deleted resource named "SVC RBAC Delete"
<img width="1088" height="15" alt="image" src="https://github.com/user-attachments/assets/33ed1ca5-7bda-47f6-aaae-079b9d4a41be" />

6. For my final task, I was given a task to find the hidden owner of a hidden resource group. Since I was already on the PIM page from the previous investigation, I decided to just look more into the role assignments page and activated the Operative role for the Resource group of "rg-madhatlabs-rbac".
<img width="706" height="149" alt="image" src="https://github.com/user-attachments/assets/2ae79f18-8549-41ad-945e-43191aed466a" />

7. The activation of the Operative role "rg-madhatlabs-rbac" also popped up a secret Operative role for the Resource group of "rg-madhatlabs-hidden-pim".
<img width="721" height="173" alt="image" src="https://github.com/user-attachments/assets/8fb6b5f6-6d48-4f8e-b0a4-0cddcbf6473f" />

8. With the hidden role given to me, I went back to Resource Groups and navigated into the "rg-madhatlabs-hidden-pim" resource group. I clicked on the Access Control(IAM) blade and downloaded the role assignments and discovered that the hidden owner was none other than Carl.
<img width="433" height="13" alt="image" src="https://github.com/user-attachments/assets/ae6a9cb0-2af0-4a7d-a732-931c37c80607" />


## What broke / what surprised me
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.
There were a few moments in the lab where I could not find what I was looking for. In step 2, the Azure CLI did not provide the same information as described in the lab. In step 3, the same thing happened again where the role export did not match with the KQL query. Though those things didn't match, I could still learn from it.

## Findings and recommendations
There are many ways to do audits and it is up to the person to cover every possible corner to discover hidden roles like I did with Carl.

## What I learned
3 to 5 bullets. At least one technical, one "what I'd do differently."
