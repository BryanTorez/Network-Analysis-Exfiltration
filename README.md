<h1>SOAR EDR Project | Intro</h1>

<p align="center">
<img src="https://snipboard.io/6rb2ue.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<h2>Description</h2>
<br />
<p align="center">
If you're trying to become a SOC analyst, network analysis is one of those skills that not many people have unfortunately and many people get pretty intimidated when presented with a pcap, a packet capture. 
  

Let's get started start by heading over to Cyber Defenders and create an account if you haven't already. Once you log in, you want to select "BlueYard Labs". Go over to practice. Then, you want to look for the lab called "Hawkeye". You will see the platform rules if this is your first time using Cyber Defenders. We can click on accept and proceed.

Let's head over to "Details". Scroll down, and you are given a scenario, "An accountant at your organization received an email regarding an invoice with a download link. Suspicious network traffic was observed shortly after opening the email. As a SOC analyst, investigate the network trace and analyze exfiltration attempts." Now with the tools provided, this is what we can use to go ahead and analyze this pcap.

In our case, I'm going to be only utilizing Wireshark and maybe even VirusTotal. From this scenario, we can already guess that there is going to be some sort of exfiltration attempt, maybe via email. Let's join the lab and then download lab files. You'll get a warning here that says that this is real malware, so just please be careful. Make sure that you utilize a virtual machine, just in case.

Once the file is finished downloading, we'll have to extract the pcap using the password "cyberdefender.org". Right-click and click on "Extract All...". Then, I'll type in the password "cyberdefenders.org". If for whatever reason you forget the password, you can find the password on the site itself. If you have Wireshark installed, you should see a pcap icon that we can just double-click.

Let's first start off with our statistics to see the duration of the capture. So we'll click on "Statistics". Click on "Capture File Properties". If we look at the time elapsed, we can see that it took around an hour and 3 minutes. Where the first packet was at '20:37:07', which is 8:37 p.m. UTC. The last packet was up until '21:40:48', which is 9:40 p.m.

The next thing we want to do is take a look at the protocol hierarchy. To do that, we need to click on "Statistics". Click on "Protocol Hierarchy" and we'll see a bunch of protocols here. Now there's SMB, SMTP, Kerberos, HTTP, and many many more. Let's go ahead and close that out and put that in our notes.

Now let's see the top talkers. Let's take a look at that by clicking on "Statistics" and click on "Conversations". Click on the ipv4 tab since we want to see the top talkers for ipv4 addresses. We'll sort it by bytes. At the very top, we see an external IP address that could be of interest. That is '217.182.138.150', so let's take note of that and also let's track the top three IPS.

Next is the TCP tab. I'm going to sort it by bytes, just like what I did before. Now we do see some port '80' traffic, which should be HTTP. That's not always the case, but typically it is. We see '445', which is SMB. Then, we see '443', which is HTTPS. Finally, we also see SMTP traffic on port '587'.

So '23.229.162.69' is likely our mail server. Scrolling a little bit down, we see port '389', which is LDAP. Then, we see port '88', which is Kerberos as well. I'll head over to UDP next. Sort it by bytes one more time. Now we see some multicast traffic with NetBIOS, NTP, DNS, DHCP and many more.

Go ahead and close that out. As a starting point, I'm interested in our top talker. So let's go ahead and filter for that. I'll type in "ip.addr == 217.182.138.150". Then, hit "Enter". We see that on packet number '210', there is a HTTP packet that occurred on '20:37:54'. 

Let's follow it by right-clicking. Click on "Follow". Select "HTTP stream". Now inside of this packet, we see a binary being downloaded as we can tell by the file header of "MZ" and the content type of "application/x-msdownload". We can extract this file later and perform some additional OSINT, open source intelligence, to hopefully get some additional context into what this file might be.

However, before we do that, let's determine how our internal IP address communicated with this external IP. So I'll go ahead and close this out. Let's take a look at the first packet, which is '207'. We want to see what happened prior to this packet. So let's go ahead and remove the TCP stream filter and hit "Enter". 

Looking at packet '206', we see a DNS response from our server back to our internal IP of '132'. Now if we were to just scroll over just a little bit, we can see that there was a standard query response for the domain of "proforma-invoices.com". This could be the suspicious download link that was hinted in the scenario. So let's keep note of this domain.

Scrolling up just a little bit, we see some SMB traffic. I am interested in the SMTP traffic since we know that this protocol exists and the scenario specifically mentioned email. So let's click on the filter and then type in "SMTP" and hit "Enter". Here we see a lot of SMTP traffic that begins on '20:38:16'. Which again is '8:38' PM. With the first packet being '3175'.

If you recall, the first activity with our top talk talker of '217.182.138.150' started on packet number '207'. So this SMTP traffic happened afterward. Looking back at our scenario, the accountant received the email regarding an invoice with a download link and unfortunately, this pcap does not seem to contain that email based on our search, but that is okay.

We will instead assume that the link was "proforma-invoices.com". Which was the domain that our internal IP requested and got a response from. Let's go ahead and right-click the first packet of '3175'. Click on "Follow". Select "TCP stream". We can see that the host name of the mail server is "secureserver.net", and the software version of "Exim 4.91".

We can also see that our client's machine is "Beijing-5cd1-PC" with a public IP of '173.66.146.112'. There is an authentication attempt using the user and password. Now these are likely encoded using "base64" and one quick way to tell is by looking at the padding at the end. If you see equal signs, that could be base64, but keep in mind that even if you do see an equal sign at the end it doesn't always mean it is base 64. However, you can always try and decode it.

To decode this, I'll go ahead and copy the base64 log-on. Then I'll head over to CyberChef. So in CyberChef, I'll go ahead and paste in that username, and then from the left, I'll select from base64 as I want to decode this text. So I'll just go ahead and double-click it and at the bottom of the output, we can see that the username is "sales.del@macwinlogistics.in".

Now let's go ahead and do the same for the password. So I'll select the password and copy it. Then, I'll paste it into CyberChef and I see that the password is "Sales@23". This is likely the account of our attacker. So what can do is perform some OSINT on the domain of our attacker. The last thing is to put that in our notes since that is an activity that we'll do later.

Let's take a look at the subject as well, since I notice that this is encoded with base64. So I'll copy the subject up to the question mark. Typically a question mark represents something else. I'll paste that in. So now we see our subject of "Hawkeye Keylogger - Reborn v9 - Passwords Logs". So we know that this is already a bad day...

It appears that our user of "roman.mcguire" was most likely the victim or the user account for "BEIJING-5CD1-PC". Probably, Roman's passwords were stolen based on the subject line. Now let's go back over to our pcap and look at the content itself. As we can see, it is also encoded using base64.

Another quick way of telling if it is encoded using base64, is looking at the context transfer encoding. It tells you that it is encoded using base64. So let's go ahead and copy out the contents. Head back over into CyberChef and paste that in. Look at that, nice. So we see a username as "roman.mcguire914@aol.com". Then, we see the password as well.

Now looking at the contents, we do know that all of the passwords stored on Roman's computer were scrapped, and these contents were apparently sent on '20:38:16'. Let's filter for SMTP and see when was the last activity. So let's scroll all the way down. So the last activity happened on '21:40:04', that is 9:40 p.m. UTC time.

I'll go ahead and right-click that. Click on "Follow". Select "TCP stream". The reason why I'm doing this is I want to see if any additional files had been exfiltrated. Now I'll copy the contents here and head back over to CyberChef to see if there's something different. By the looks of it, it seems to be the same. There are three passwords here or three credentials.

Now the next thing we can do is export the file we found in the beginning and generate a file hash to perform some additional OSINT. So let's go ahead and do that. Before we export the file, we will likely need to disable Defender just in case it detects and blocks the malware.

Click on "Manage Settings". I'll disable "Real-time Protection", and if you are following along this is a good time to remind you again. Make sure that you're using a virtual machine because mistakes do happen. So let's click on "File". Click on "Export Objects". Finally, select "HTTP...".

We want to look for the domain "proforma-invoices.com". It's file name should be under "tkraw_Protected99.exe". I'll save this with the extension ".malware". Again, we make mistakes and sometimes execute things we don't want to. Now this is also interesting. We also see "bot.whatismyipaddress.com" happening quite frequently. This gives me an idea that maybe, once executing this malware or this executable, it makes a scheduled query out to "whatismyIP" to determine if my IP changed.

So now that we have our file downloaded, let's open up Powershell. I'll type in "Get-FileHash" and then type in our file of interest. So that is our Shaw 256 hash. So let's go ahead and copy that out. We'll use the best tool ever, VirusTotal. Click on "Search" and then I'll paste in the file hash that we generated. Now we see the domain title as "trojan.autoit/gen8". Full of malware...

We see 15+ comments under the community, so let's go ahead and click on that. Scrolling down, we see 16 comments saying the verdict is "MALICIOUS 100/100". That's good to know. Let's scroll back up and then select "Relations" to look at the contacted domains. I wanted to know if this executable was responsible for querying "whatismyipaddress" since if you recall, under the Wireshark export we see a bunch of scheduled or at least I assume it's a scheduled query outbound to what is my IP address.com. 

Aside from that, looking at VirusTotal, we can tell that it does contact the domain bot, "whatismyipaddress.com". So this confirms my assumption that this executable does indeed call out to that domain. If we click on "Details" we can take a look at the creation time of this malware. The creation time of the malware was back on '2019-4-10 04:43:40 UTC'. Which was on the same day our accountant, or at least this accountant, reported this activity.

Next, let's start performing some open source intelligence on the top talker IP and domain. We'll start off with the IP address of '217.182.138.150'. I'll be using Abuseipdb.com. We see that the ISP, the internet service provider, is "OVH SAS" with the usage type of a "Data Center/Web Hosting/Transit". It's also located in the country France.

Abuseipdb.com did not find this IP in their database. If we were to look at VirusTotal, we see that four vendors have flagged this as malicious. Next, we'll take a look at the domain itself. For this I'll be using "whois.domaintools.com". I'll paste in the domain. Hit "Search". We see that performa-invoices.com" is for sale. Unfortunately, with a free account, we cannot see the history of this domain.

However, we can see two changes on two unique IP addresses over 5 years. Maybe that's something to note of. Back in VirusTotal, I'll do the same. We see that 12 vendors reported this as malicious, and some as phishing. The next thing I want to take a look at is the email domain of the attacker.

I'll type in "macwinlogistics.in". So there's no reports on VirusTotal. However there are at least 10 detected files communicating with this domain. Next, I'll utilize 'Whois Lookup' from DomainTools. We can see that this domain is for sale as well. Now we can start building our findings and report it to whoever needs to know.

On April 10th, 2019 at 20:37:54 (UTC) time, the user had accessed the site "proforma-invoices.com" and requested the file named "tkraw_protected99.exe". Based on open source intelligence, this file is classified as a trojan with a malware label of HawkEye. HawkEye is a Malware family that is known for its keylogger capabilities. Hawkeye is usually delivered, via phishing, and this file is known to query a site called "whatismyipaddress.com".

Evidence shows that the host "BEIJING-5CD1-PC" was seen querying the site "whatismyipaddress.com" at 20:38:15. Immediately, there was communication towards a mail server at '23.229.162.69'. Which is an account of "sales.del@macwinlogistics.in" was used to authenticate at 20:38:16. Contents containing passwords related to Roman Mcguire were seen being sent to the email address of "sales.del@macwinlogistics.in". The same content containing passwords was sent again to the same email at 21:38:43.

The last mail activity occurred on 21:40:04. Based on our evidence, the total duration of compromise appears to be between 20:37:54 to 21:40:04. It is important to take a step back and recap your investigation every now. Especially, if you are performing an investigation that involves multiple machines since it can get pretty overwhelming. Again, especially if you don't take any notes.

Now let's finish this up by completing the questions on Cyber Defenders.

For question one, "How many packets does the capture have?" Go to WireShark. Go to "statistics". Select "Capture File Properties". Under "Packets", we see 4003.


For question two, "At what time was the first packet captured?" So again, we can go back into our Capture File Properties and look under the first packet. 

For question three, "What is the duration of the capture?" Head back over to the File Properties and we see the duration of 1 Hour 3 minutes and 41 seconds.

For question four, "What is the most active computer at the link level?" Link level is layer two, so your MAC address. We can get this information by clicking on "Statistics". Select "Conversations". Now we can copy the value of address A.

For question five, "Manufacturer of the NIC of the most active system at the link level?" If we look at our layer 2 address. You want to take the first three bytes of the MAC address which in this case is 00:08:02. Search up "WireShark oui", as this will provide you with a lookup to see what the vendor is for this NIC. We see that it's "Hewlett Packard", AKA HP. 

For question six, "Where is the headquarter of the company that manufactured the NIC of the most active computer at the link level?" I'll type in "Hewlett Packard headquarters". We see the company was headquartered in Paulo Alto, California.

Question number seven, "The organization works with private addressing and netmask of /24. How many computers in the organization are involved in the capture?" Click on "Statistics". Click on "Endpoints". Go over to "ipv4". I'll sort it by address just to make sure all of the internal private IP addresses are at the top. We can see that there are three host starting from the top.

Question number eight, "What is the name of the most active computer at the network level?" If you recall, when we followed the SMTP traffic, we saw that the host name was "Beijing-5cd1-PC". 

Question number nine, "What is the IP of the organization's DNS server?" To find this answer we can go ahead and search up and filter DNS. We want to look at the responses, in this case is responding from the Source Port of '53'. We can see that this traffic here is responding to '132', which is our internal IP. So we can infer that '10.4.10.4' is our DNS server.

Question number ten, "What domain is the victim asking about in packet 204?". Looking at '204', we see that it is querying "proforma-invoices.com".

Question number eleven, "What is the IP of the domain in the previous question?" Looking at the response from that DNS, we see the address of '217.182.138.150'. So that is going to to be the IP address of our domain of interest.

Question number twelve, "Indicate the country to which the IP in the previous section belongs." We did some ENT earlier, and we found out that this was located in France.

Question number thirteen, "What operating system does the victim's computer run? So we can use user agents to find out. Filter for "HTTP". Click on a packet. Right-click. Follow. HTTP stream. We can see that it's using "Windows NT 6.1".

Question fourteen, "What is the name of the malicious file downloaded by the accountant?" So we know that the file was downloaded via http and we can see that it was requested. The file name is "tkraw_Protected99.exe".

Question fifteen, "What is the md5 hash of the downloaded file?"  We can do this, via Powershell since we did extract that file. We can type in "Get-FileHash", but this time we'll specifically mention the algorithm. Then we get our md5 hash. 

Question sixteen, "What software runs the webserver that hosts the malware?" Looking at our HTTP Stream, we see the server "LiteSpeed".


Question seventeen, "What is the public IP of the victim's computer?" Take a look at SMTP, we see a public IP of '173.66.146.112'.

Question eighteen, "Which country is the email server to which the store stolen information is sent?" I'm guessing this question is asking where is the email server located. We do know that the email server had an IP address of '23.229.162.69'. 

Question nineteen, "Analyzing the first extraction of information. What software runs the email server to which the stolen data is sent?" Taking a look at the SMTP traffic, we see that there is a software called "Exim 4.91". 

Question twenty, "To which email account is the stolen information sent?" Looking at our SMTP traffic, we see "sales.del@macwinlogistics.in".

Question twenty-one, "What is the password used by the malware to send the email?" Copy password. Head over to CyberChef. Paste password. We see "Sales@23".

Question twenty-two, "Which malware variant exfiltrated the data?" Head back to CyberChef. We see "HawkEye Keylogger - Reborn v9". 

Question twenty-three, "What are the bankofamerica access credentials? (username:password)" Look at our contents, we see that the username is "roman.mcguire" with the password.

Question twenty-three, "Every how many minutes does the collected data get exfiltrated?" We can filter for "SMTP" and check the "info" for how many "DATA fragments" there are every single minute. From the data log, we can see that it's every 10 minutes. 

Hopefully, you have learned a lot from this lesson and are becoming more confident in your ability to perform network analysis from a PCAP. That is it for this lesson and I hope you found this informative. Remember to stay curious and do things differently.
