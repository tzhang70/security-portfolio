# [The Stolen Identity]

## Scenario
2 to 3 sentences. What was the situation and what question did the investigation answer? Frame it like a work ticket, not homework.
Someone was successful in their phishing campaign against one of the employees at Mat Hat Labs. Carl entered his credentials and multifactor authentication codes into a website that looked like the employee portal. With this access, the attacker created a new app registration called Mad-hat-Labs-App and provisioned it to expose APIs to continue collecting tokens from real users in the Mad-Hat-Labs tenant allowing the attacker to rotate credentials.

## Environment
Live multi-user Azure training tenant, Reader access.

## Investigation
1. I started by investigating into the Mad-Hat-Legacy-Sync Service enterprise app. This application showed traces of the attacker enumerating the directories for a way to escalate access.
   
2. The certificates & secrets blade showed that the attacker created a new client secret and left it there to be used for future credential tokens harvesting.
<img width="949" height="464" alt="image" src="https://github.com/user-attachments/assets/1f54b7e4-14d6-4aa0-adb5-782620ed8379" />

3. For the next part of the investigation, I looked into the API permissions and discovered the attacker configured new admin permissions into Microsoft Graph with the Directory.ReadAll and User.ReadAll permissions enabled. These two permissions grant the attacker the ability to read the directories and users which increase the attack vector. I noticed the status of the permissions say "Granted to Mad-Hat-Labs" which confirms this is connected to the newly created Mad-Hat-Labs-App.
<img width="1005" height="480" alt="image" src="https://github.com/user-attachments/assets/e581cf84-eaa0-4b29-a821-068e1db12168" />

4. I reviewed the Owners tab to confirm that the Mad-Hat-Labs-App is registered as an owner of the Mad-Hat-Legacy-Sync-Service app.
<img width="990" height="490" alt="image" src="https://github.com/user-attachments/assets/9a0c8884-1169-4ab9-a396-7ab01a9b8c7d" />

5. Another place I checked was the Expose an API blade. This is a backup plan set up by the attacker in case their client secrets get removed from the Mad-Hat-Legacy-Sync-Service app. This API lets the attacker walk in through the back-door again whenever a Mad Hat employee clicks on the link to login to the fake portal. 
<img width="959" height="474" alt="image" src="https://github.com/user-attachments/assets/5a70283e-22fc-4d18-91fd-b4b63c964892" />

## What broke / what surprised me
I was surprised by how much an attacker can do once they get their hands on one credential. If things are not secured down properly, there are so many ways for an attacker to traverse and enumerate to find their next attack vector.

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.
1. I highly recommend employees get trained on phishing campaigns. It may seem pointless at first, but constantly sending practice phishing emails to employees can go a long way in preventing that issue as it is much easier to get a person to share their credentials than it is to break through the security walls.
2. I would also recommend audits to deprovision legacy/unused apps like the Mad-Hat-Legacy-Sync-Service. This would have stopped one of the attack vectors. 

## What I learned
1. It is hard to secure everything from an attacker's crutches. The person in charge of creating these safeguards against attackers could do everything right. One wrong step from a careless employee breaks all that down.
2. Never stop trying to educate your employees on phishing campaigns. 
