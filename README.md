# StrictBlockPAllebone
Manually curated IP Blocklist. Recommended only to update your firewalls weekly with this list. Please read how to use before implementing.

Direct link to list:

https://raw.githubusercontent.com/pallebone/StrictBlockPAllebone/master/BlockIP.txt


# How to use

# Overview:

Note: This guide will show you how to setup the blocklist using a popular firewall, OPNSense. If using a different firewall, simply take the logical steps as approprate for your own firewall.

Before I started this blocklist, I was using the spamhaus ip blocklists (https://www.spamhaus.org/drop/) drop, and edrop.
I found that unfortunatly while 90% or so of malicious IP's were caught, quite a lot still got through.
As I wanted a greater net of blocked IP's, ie around 99%+ of malicious IP's blocked, I started creating my own list manually each day after manually reviewing the logs on my firewall.

This means that the list is only updated, manually, on days I check my logs. Typically this is each day during the week, not at all on weekends, and if I go on holiday there may be a week when its not updated. For this reason, there is no real point in updating the IP blocklist on your firewall more often than around every 7 days. This is the value I am expecting you to use in production.

The below guide shows how to implement the blocklist, and optionally, create an allowlist for any IP's that you personally find are blocked, but want to allow access to services without having to remove the entire blocklist to do so.

# Implementing:

As the list expects you to already have spamhaus's lists blocked, and is supplementing that list, you should begin by adding the spamhaus blocklists to your firewall.

Step 1: Create the Aliases for the blocklists we will be using, and allowlist if you desire:
<img src="./Alias.png">
(please note my image shows an internal IP that I am updating my list from as I create the lists. You will however NOT use this internal IP obviously. I copy this altered list up to github when I am finished modifying it.

Each item created as follows:

##### FirewalledServices	Port(s)	 	22,80,443,3389,19132

This is the services on the firewall that are open to the outside world and forwarded to various different internal computers behind the firewall.
In my own case, I have an ssh server, a web server, an rdp server and a minecraft server.
These ports must be specified as an alias so that we can add an allow rule later on to these allowed ports, from IP's we want to allow.


##### StrictBlockPAllebone	URL Table (IPs)	 	https://raw.githubusercontent.com/pallebone/StrictBlockPAllebone/master/BlockIP.txt

This is a URL table to the blocklist.


##### AllowlistedIPs	Host(s)	 	

This is the aliases you will add IP's you want to allow into. In the screenshot you see a random test IP I used to check it worked. You will only add your own list of IP's you deem relevant, nothing else. They will not be blocked once you add them.


##### spamhaus_drop	URL Table (IPs)	 	https://www.spamhaus.org/drop/drop.txt

The IP drop list from spamhaus.


##### spamhaus_edrop	URL Table (IPs)	 	https://www.spamhaus.org/drop/edrop.txt

The IP edrop list from spamhaus


##### spamhaus_group	Host(s)	 	spamhaus_drop,spamhaus_edrop

An alias that contains the 2 spamhaus lists in one single alias so we can add just this alias to a firewall rule.



Step 2:
Create your firewall rules under "firewall - wan" in order to allow and block the relevant traffic as follows:
<img src="./Rules.png">

##### IPv4 TCP/UDP 	AllowlistedIPs  	* 	* 	FirewalledServices  	* 	* 	Allowlist 

This simple rule allows Allowlist IP's to access the ports listed in alias "FirewalledServices" and tags the label "Allowlist" 
