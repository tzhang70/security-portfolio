# [The Friday Deploy]

## Scenario
An intern shipped a new notification service on Friday at 4:57pm and it needs to be reviewed before it reaches the production environment. I will need to compare it to the baseline standards set by the company.

## Environment
Live multi-user Azure training tenant, Reader access.

## Investigation
1. Since we are a United States based company, I had to first check where the new service is deployed into. As seen in the image below, the intern deployed it into Australia East. This means that it would cost us a lot of money if data needs to be moved to our services in the United States.
<img width="407" height="196" alt="image" src="https://github.com/user-attachments/assets/9ca5ca9d-ef44-4647-bcfa-c392019cb41d" />

2. My next step is to check where the service authenticates from. Upon checking the app's Identity section, there is no system or user assigned identity.
<img width="570" height="233" alt="image" src="https://github.com/user-attachments/assets/cd570bb1-21b1-42ed-a05a-58923b26d3b0" />
<img width="433" height="272" alt="image" src="https://github.com/user-attachments/assets/145b137e-59ec-4779-af00-4c1c1642c5e8" />

3. I compared the intern's deployment to an already deployed service in production and it has a user assigned identity. I can deduce that the intern is most likely using a stored secret somewhere for their application.
<img width="595" height="264" alt="image" src="https://github.com/user-attachments/assets/f862c302-9a5a-48c0-9650-a81f45d4cb86" />

4. I went back into the intern's storage account and found they left behind some notes in text form. To test if it was readable to the world, I took the URL of the text file and opened it in incognito. To nobody's surprise, it is readable to everyone.
<img width="506" height="272" alt="image" src="https://github.com/user-attachments/assets/c256bf72-46b9-41be-97f0-b8a1f9e84295" />
<img width="569" height="110" alt="image" src="https://github.com/user-attachments/assets/9a59f152-c113-4219-b8c1-8ef5c6539b12" />

5. Since the text file was readable for everyone, I decided to go into the Networking blade of the function app. The intern has set the public network access setting to "Enabled with no access restrictions". This means anyone who can get their hands on it will have unrestricted access. 
<img width="578" height="207" alt="image" src="https://github.com/user-attachments/assets/07de5e36-0c33-459a-890e-277a70275aeb" />
<img width="830" height="611" alt="image" src="https://github.com/user-attachments/assets/6d3b7c7e-6e4f-4a05-a678-49110d56ea22" />

6. I compared it with a functioning app in the production environment, and that app has all its access restrictions set up properly.
<img width="844" height="676" alt="image" src="https://github.com/user-attachments/assets/027c5911-cab5-4c5f-ab21-08b4361c1b46" />

7. With the investigation complete, the intern-notes container that is holding the important information has to be closed as soon as possible to prevent continued data leakage.


## What broke / what surprised me
I was surprised by how easy it is to mess up in the configurations which will lead to money loss, data leakage, and headaches.

## Findings and recommendations
The container containing the container holding the client secret needs to be removed. The intern also needs to be educated on not making deployments on Friday to prevent data leakage over the weekend. The intern also needs to be educated on how to properly do configurations before making deployments. 

## What I learned
I would have deployed the application into the staging environment and tested all the ways of accessing the file as an outsider.
