# Looting Buckets

## Scenario
A curious developer wants to know the process of how their application reaches the customer data. I am tasked with tracing the path and explaining in detail how the data travels from beginning to end.

## Environment
Live multi-user Azure training tenant, Reader access.

## Investigation
1. For my first step in the trace, I needed to make sure that the storage account is correctly pointing to the right blob service endpoint. Upon closer inspection, it is the correct blob.
<img width="1232" height="532" alt="image" src="https://github.com/user-attachments/assets/8faebc00-4967-40e4-83a2-b390880be747" />

2. Next, I want to take a look at the key's posture. I checked the configuration settings of "Allow storage account key access" and see that it is disabled. This is a good thing to check as it means only identity will be the only way to get in. 
<img width="369" height="108" alt="image" src="https://github.com/user-attachments/assets/8a56ce91-9ca5-4c73-a001-d0f69b5f1597" />

3. I checked the Storage Account's Access Control settings to see that a certain user managed identity currently has the role of Storage Blob Data Contributor. 
<img width="921" height="402" alt="image" src="https://github.com/user-attachments/assets/eb548be2-7d1c-4b2b-bb43-f50ecd3ea974" />

4. After that, I go into the Networking tab and see that Public network access is set to disabled which is great. In the Private endpoints tab, I see a "pe-lab-storage" endpoint has been set up. This Virtual Network is what the storage account uses to reach the data. 
<img width="1008" height="426" alt="image" src="https://github.com/user-attachments/assets/ce626154-e7c7-4fe4-8151-7db46a8cb459" />

5. Inside the pe-lab-storage, the DNS configuration is set to a particular private IP address which points to the blob from step 1.
<img width="2001" height="1205" alt="image" src="https://github.com/user-attachments/assets/b180cbc6-d501-436a-9452-fed80d658967" />

6. Once all of that is confirmed, I go to the Key Vault and inspect the Secrets that the User Managed Entra Identity needs to read in order to gain access to the customer data. I've confirmed the only way in is through the Private Endpoint set up in Step 4 and the application does not have any exposed weak points that attackers may use to get in.

7. For a fun little exercise, I looked at another storage account in the same Resource Group. To nobodies surprise, this storage account is basically the backup of the storage account I investigated earlier. However, this time the public network access was set to Enabled from all networks. The bucket contained an XML that displays a JSON link which contains all the customer data.
<img width="1115" height="493" alt="image" src="https://github.com/user-attachments/assets/98435b66-ed30-4139-ab6c-a3eb8af6186e" />
<img width="1966" height="334" alt="image" src="https://github.com/user-attachments/assets/9ed2973f-d3c6-450f-87c6-bdc16364401a" />


## What broke / what surprised me
I'm still so very surprised people make these type of silly mistakes. Surely their work colleagues make sure these types of things don't happen.

## Findings and recommendations
I would let the resource owner know that while the person did a great job setting up the storage account. Whomever created the backup, definitely needs a lesson to not do that again.

## What I learned
I learned that people can't really check everything, which explains auditors are so important to check for these types of misconfigurations and correct them.
