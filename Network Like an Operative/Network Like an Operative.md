# Network Like an Operative

## Scenario
The newly deployed service is able to talk with the customer-data storage account. An investigation has been started to discover how it happened and the journey it takes to go from the service to the storage account.

## Environment
Live multi-user Azure training tenant, Reader access

## Investigation
1. I started by tracing how the packet travels through the network. First, I went into the resource group housing the service and checked the subnet range that is applied. This tells me the packet can travel from 10.60.1.0 to 10.60.1.255 barring the 5 that Azure reserves.
<img width="899" height="506" alt="image" src="https://github.com/user-attachments/assets/753297e2-dcff-4c74-9d2d-d3f574b5e64e" />

\
\
2. Upon looking further into the subnet, I discover that this subnet is attached to a Network Security Group called nsg-lab-workload. I head on over to the Network Security Group tab and find the nsg-lab-workload.
<img width="316" height="23" alt="image" src="https://github.com/user-attachments/assets/9f251778-87bc-4338-bb9a-0e2823c1ba51" />


3. There is one rule that stood out to me and it was the allow-vpn-https rule. This rule only allows the ip address of 203.0.113.50 to reach this workload over port 443. 
<img width="1153" height="276" alt="image" src="https://github.com/user-attachments/assets/62105238-4743-41af-9c7c-5265e66f36d3" />


4. After gathering that information, I head back to the resource group's route table. This lets me know that the next hop IP address is 10.60.100.4. After seeing this, I can infer that the next private endpoint might be injecting its own route with a longer prefix which allows it to bypass previous rules set in order to reach that storage account.
<img width="781" height="89" alt="image" src="https://github.com/user-attachments/assets/b99a6283-61e5-4777-bfeb-fbb0cf0d0ca7" />


5. I head to the private endpoint and look at the DNS configuration and see which private ip address route got injected.
<img width="661" height="230" alt="image" src="https://github.com/user-attachments/assets/f72fad23-d295-4c04-99f4-0047f3f35908" />


6. Next, I head to the Private DNS Zone link provided in the above picture and check the record sets. There contains the ip address that points to the storage account.
<img width="664" height="142" alt="image" src="https://github.com/user-attachments/assets/c7f369fa-03ab-43f7-a610-ba47abe7d5fa" />


## What broke / what surprised me
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.
To be quite honest, I still don't understand completely how the hops inject their own route into the mix without seeing it first hand. I will need to study up more about that.

## Findings and recommendations
Networks are confusing which means I need to spend more time learning it. 

## What I learned
I learned that routes with longer prefixes can bypass the other rules set. 
I also learned that private endpoints can inject their own routes thus bypassing security set in place.
