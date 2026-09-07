# Investigating an Intern's deployment on a Friday

## Scenario
A junior intern,with no prior experience to the company's governance standards, at Mad Hat Labs was given temporary contributor access to create a test environment. My job is to assess the damage, identify the governance failure that let this change go through, and document each step of the failure. 

## Environment
Live multi-user Azure training tenant, Reader access.

## Investigation
The core. Numbered steps IN YOUR OWN WORDS: what you looked at, what you found, what you concluded at each step. 6 to 12 screenshots of meaningful moments (portal views, query results, before/after).
1. Investigated the resource groups and found that the naming convention did not match the required naming scheme recommended by Microsoft. “testdeploy123” 
<img width="163" height="230" alt="image" src="https://github.com/user-attachments/assets/e3dbfc47-4aa8-407b-9e34-6da3b4698e6c" />

2. Looked at the tags within the storage account created by the intern, and saw that they created the Tag properly. No issues here.
<img width="518" height="209" alt="image" src="https://github.com/user-attachments/assets/22424e7f-1d9c-408a-b798-764c6ca6ee50" />

3. I went back to the deployments page of the testdeploy123 resource group and found that the intern also did not name the deployment properly.
<img width="532" height="181" alt="image" src="https://github.com/user-attachments/assets/1d64f6c2-f968-4b95-aceb-c594f32e66b4" />

4. I checked the testploy123 resource group’s policies and found that there were two policies that were non-compliant. 
<img width="524" height="173" alt="image" src="https://github.com/user-attachments/assets/0a75773e-4247-4d2b-bb09-a42ab7c6fa9c" />

5. I checked the Assignments tab and discovered that the “Naming Convention” Policy Assignment had a Parameter Value set to “Audit”. Since the Parameter Value was set to Audit, the Policy did not prevent the resource group from being created. The Naming Convention Policy is explicitly doing what it was told to do. The Parameter Value needed to be set to “Deny” for the “Naming Convention” policy to properly block the creation of the resource group “testdeploy123”.
<img width="537" height="338" alt="image" src="https://github.com/user-attachments/assets/9cecb52e-db08-4e69-9439-60688618b684" />

## What broke / what surprised me
What surprised me is that one wrong configuration that is not caught early could cause a huge snowball effect. 

## Findings and recommendations
1.Users must learn the required knowledge to properly set things up before being assigned tasks in Microsoft Azure and Entra ID. 
2.If the creator of the Naming Convention policy knows why the Parameter value was set to Audit, then it should be documented. However, if the creator didn’t know, then they need to also learn the differences of how each Parameter value functions.

## What I learned
1. In future deployments from this user, I would recommend going through the dev, staging, and prod development cycle to prevent these types of issues. These policies either block or audit the issue for you.
2. Security controls must be precise in what you need it to do.
