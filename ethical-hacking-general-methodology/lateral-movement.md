# Lateral Movement

Lateral movement is one of the essential components for moving through a corporate network. We can use it to overlap with other internal hosts and further escalate our privileges within the current subnet or another part of the network. However, just like Pillaging, the Lateral Movement stage requires access to at least one of the systems in the corporate network. In the Exploitation stage, the privileges gained do not play a critical role in the first instance since we can also move through the network without administrator rights.  

Lateral movement describes movement within the internal network of our target company to access additional hosts at the same or a higher privilege level. It is often an iterative process combined with post-exploitation activities until we reach our goal. For example, we gain a foothold on a web server, escalate privileges and find a password in the registry. We perform further enumeration and see that this password works to access a database server as a local admin user. From here, we can pillage sensitive data from the database and find other credentials to further our access deeper into the network. In this stage, we will typically use many techniques based on the information found on the exploited host or server.
*Source HTB Academy*

## Possible paths after this step

|Path|Description|
|----|-----------|
|Vulnerability Assessment|If the penetration test is not finished yet, we can jump from here to the Vulnerability Assessment stage. Here, the information already obtained from pillaging is used and analyzed to assess where the network services or applications using an authentication mechanism that we may be able to exploit are running.|
|Information Gathering / Pillaging|After a successful lateral movement, we can jump into Pillaging once again. This is local information gathering on the target system that we accessed.|
|Proof-of-Concept|Once we have made the last possible lateral movement and completed our attack on the corporate network, we can summarize the information and steps we have collected and perhaps even automate certain sections that demonstrate vulnerability to the vulnerabilities we have found.|  

*Source HTB Academy*

## Resources

{% embed url="https://academy.hackthebox.com/login" %} Hackthebox Academy Penetration Tester Job Path {% endembed %}  
