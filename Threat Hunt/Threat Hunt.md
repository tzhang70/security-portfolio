# Threat Hunt

## Scenario
A suspicious IP probing disabled account and Solorigate command-and-control beacon has been discovered in the threat queue. I have to find them, determine their attack radius, and stop them from doing more damage to the tenant.

## Environment
Live multi-user Azure training tenant, Reader access

## Investigation
1. I first investigated the suspicious IP probing account by going into Microsoft Sentinel Incident's blade. Within the blade, it showed me the IP that was responsible for causing all these sign in logs. All 10 alerts pointed to that same IP address.
<img width="770" height="652" alt="image" src="https://github.com/user-attachments/assets/3ac16a74-1248-47d7-977d-c03859dff3e1" />

2. With the IP address found, I went into Sentinel's Advanced Hunting blade and spun up a query to find which User Principal Name is attached to this IP address. I was able to stop that account from being used further to compromise information from the tenant. 
<img width="668" height="473" alt="image" src="https://github.com/user-attachments/assets/8813a6ef-c7d8-4cef-a8c0-a5641099f05e" />

3. The next ticket was the Solorigate command-and-control beacon ticket. There was 6 alerts that pointed out a specific IP address along with the server it was communicating to.
<img width="602" height="658" alt="image" src="https://github.com/user-attachments/assets/1c795717-a756-468e-808c-6b20b8291657" />

4. My next step is to find the blast radius of what got affected within the tenant. I headed over to the Advanced Hunting blade and fired up a query to see what has been affected so far. It seems to have infected 3 PCs.
<img width="737" height="233" alt="image" src="https://github.com/user-attachments/assets/4a18674f-e0d0-4c2f-b929-11d9bf08a9a7" />

5. In the Solorigate alert's raw events tab, I found where it was disguising itself as to hide from Sentinel's detection.
<img width="437" height="270" alt="image" src="https://github.com/user-attachments/assets/f5f6319d-756f-4d22-ad99-3791bbde9503" />


## What broke / what surprised me
1. I got a little confused at the part where I had to find the Solorigate alert's raw event tab. I wasn't able to find it within the query in Step 4. So I had to go do some digging in the other event blades and eventually found it.
2. The funniest part about the Solorigate event is that I also experienced a supply chain attack on my personal PC through a game's launcher recently. An entity was downloaded through the game's launcher and had been sitting there since September. It scared me for the day as I was panic changing my password and moving everything to a password manager. I later found out that the attackers never deployed their C2 onto my PC as the steps provided by people helped me to discover that fact.

## Findings and recommendations
1. I recommend disabling and deleting accounts that aren't in use anymore as that was the cause of the IP probing account.

## What I learned
1. I would start doing account audits and other security checks more often with the boom of AI actively searching for any holes to poke through.
