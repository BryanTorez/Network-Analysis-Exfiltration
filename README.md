<h1>SOAR EDR Project | Intro</h1>

<p align="center">
<img src="https://snipboard.io/6rb2ue.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<h2>Description</h2>
<br />
<p align="center">
if you're trying to become a sock analyst network analysis is one of those skills that not many people have
0:06
unfortunately and many people get pretty intimidated when presented with a peap a packet capture I know this because well
0:13
I was the same way when I first started this is why I wanted to create this video so we can go over another exercise
0:21
of investigating peaps if you haven't already I would recommend you watch my
0:27
first video on investigating peaps as I will be using the same settings and wire
0:32
shark from that video also I created a sock roadmap video and in that video I
0:38
provided you with some labs to complete to obtain the skills required to become a sock analyst this lab that we will go
0:45
over today is one of the labs that I listed titled Hawkeye under network
0:50
analysis let's get started start by heading over to cyber Defenders and create an account if you haven't already
Walkthrough
0:57
once you log in you want to select blueyard left labs and then go over to practice and then you want to look for
1:03
the lab called Hawkeye or we can go ahead and search that up by typing in Haw and I and at the bottom we see
1:10
Hawkeye go ahead and click on that you will see the platform rules if this is your first time using cyber Defenders we
1:17
can click on accept and proceed and let's head over to details then when we scroll down we are given a scenario the
1:24
scenario is an accountant at your organization received an email regarding an invoice with a download link
1:31
suspicious Network traffic was observed shortly after opening the email as a sock analyst investigate the network
1:38
trace and analyze exfiltration attempts now with the tools provided this is what
1:44
we can use to go ahead and analyze this peap in our case I am going to be only
1:49
utilizing wire shark and maybe even virus total and from this scenario we can already guess that there is going to
1:56
be some sort of exfiltration attempt maybe via email now let's join the lab
2:02
and then download lab files you'll get a warning here that just says that hey this is real M so just please be careful
2:09
and make sure that you utilize a virtual machine just in case once the file is finished downloading we'll have to
2:15
extract the pcap using the password
2:21
cyberdeck this and click on extract all and then I'll type in the password cyber
2:27
defenders.org if for whatever reason you forget the password you can find the password on the site itself it will be
2:34
listed right here if you have wi shark installed you should see a pcap icon that we can just double click now again
2:40
I did mention this previously but before I dig into this peap if you haven't seen my previous video on how to investigate
2:48
pcaps I highly recommend you watch that first as I'll be using the same wire
2:53
shark settings let's first start off with our statistics to see the duration of the capture so we'll click on St
2:59
statistics click on capture file properties if we look at the time elapsed we can see that it took around
3:06
an hour and 3 minutes where the first packet was in 2019 April 10th
3:14
20377 which is 8:37 p.m. UTC time and
3:19
the last packet was up until 21448 which is 9:40 p.m. the next thing
3:24
we want to do is take a look at the protocol hierarchy and to do that we click on statistics click on protocol
3:30
hierarchy and we see a bunch of protocols here now there is SMB
3:37
SMTP Kerberos HTTP and many many more let's go ahead
3:43
and close that out and put that in our notes and then we'll see the top talkers let's take a look at that by clicking on
3:49
statistics and click on conversations click on the ipv4 tab because we want to see the top talkers for ipv4 addresses
3:57
and then I'll sort it by bytes at the very top we see an external IP address which could be of interest and that is
4:05
21718 2138 do15 so let's take note of that and
4:11
also let's track the top three IPS so that external IP the
4:16
104104 and 23229 16269 because these could be of interest
4:24
next is the TCP tab I'm going to sort it by bytes just like what I did before for
4:30
and we do see some Port 80 traffic which is HTTP or should be HTTP that's not
4:37
always the case but typically it is and then we see 445 which is SMB and
4:42
https and then we also see SMTP traffic on Port 587 so this is probably our mail
4:49
server the 23229 16269 is likely the male server
4:55
scrolling a little bit down we see ldap and kerros as well and then I'll head
5:00
over to UDP next sort of by bytes again and we see some multicast traffic with
5:06
net bios ntp DNS DHCP and many more go
5:11
ahead and close that out as a starting point I am interested in our top talker so let's go ahead and filter for that
5:18
I'll type in ip. addr equal equals and then I'll type in the external IP
5:24
address of 217.11 18238
5:30
.150 and then I'll hit enter we see that on packet number 210 which is this one
5:36
right here there is a HTTP packet that occurred on 20
5:42
3754 let's follow it by right clicking it and click on follow HTTP stream now
5:48
inside of this packet we do see a binary being downloaded as we can tell by the
5:53
file header of MZ and the content type of application SLX x- Ms download now we
6:02
can extract this file later and perform some additional ENT open source intelligence to hopefully get some
6:08
additional context into what this file might be but before we do that let's determine how our internal IP address
6:15
communicated with this external IP so I'll go ahead and close this out let's take a look at the first packet which is
6:23
207 we want to see what happened prior to this packet so let's go ahead and remove the TCP stream filter and hit
6:31
enter looking at packet 206 we see a DNS response from our server back to our
6:37
internal IP of 132 now if we were to just scroll over just a little bit we
6:43
can see that there was a standard query response for the domain of
6:48
pro-in voices.com this could be the suspicious download link that was hinted
6:54
in the scenario so let's keep note of this domain scrolling up just a little bit we do do see some SMB traffic I am
7:02
interested in the SMTP traffic because we know that this protocol exists and the scenario specifically mentioned
7:08
email so let's click on the filter and then type in SMTP and hit enter here we
7:14
see a lot of sntp traffic that begins on 203816 which again that is 8:38 p.m.
7:23
with the first packet being 3175 if you recall the first activity
7:28
with our top talk Walker of 21718 21381 15 started on packet number
7:36
207 so this SMTP traffic happened afterwards looking back at our scenario
7:42
the accountant received the email regarding an invoice with a download link and unfortunately this peap does
7:48
not seem to contain that email based on our search but that is okay we will instead assume that the link was
7:55
performa dasin voices.com which was the domain that our internal IP requested
8:00
and got a response from let's go ahead and right click the first packet of 3175 and then click on follow and then
8:09
select TCP stream we can see that the host name of the mail server is this
8:14
secure server.net and the software version of XM 4.91 we could also see that our client
8:21
machine is Beijing -5c D1 DPC with a
8:27
public IP of 173 66.1
8:32
14612 there is an authentication attempt using the user and password now these
8:37
are likely encoded using base 64 and one quick way to tell is looking at the padding at the end if you happen to see
8:43
equal signs that could be base 64 but do keep in mind that even if you do see an
8:49
equal sign at the end it doesn't always mean it is base 64 but you can always try and decode it to decode this I'll go
8:55
ahead and copy the space 64 log on and and then we will head over to cyberchef
9:01
which is a pretty cool tool that you should have in your toolkit so over in cyberchef I will go ahead and paste in
9:07
that username and then from the left I'll select from base 64 as I want to decode this text so I'll just go ahead
9:14
and double click it and at the bottom of the output we can see that the username is sales. Dell macwin
9:22
logistics. now let's go ahead and do the same for the password so I will select the password and copy it then I'll paste
9:30
it in cyberchef and I see that the password is sales with a capital S at 23
9:35
this is likely the account of our attacker so we can perform some ENT on the domain of our attacker put that in
9:42
our notes and that is an activity that we can do later let's take a look at the subject as well as I do notice that this
9:48
is encoded with B 64 so I'll copy the subject up to the question mark As
9:53
typically a question mark represents something else and then I'll paste that in and then we see our subject of
10:00
Hawkeye keylogger D reborn v9- password logs so we know that this is already a
10:06
bad day it appears that our user of roman. Maguire was likely the victim or
10:12
the user account for Beijing dvcd 1-pc and probably Roman's passwords were
10:20
stolen based on the subject line here now let's go back over to our pcap and look at the content itself and as we can
10:27
see it is also enod ened using B 64 another quick way of telling if it is
10:33
encoded using B 64 is looking at the context transfer encoding it literally tells you that hey it is encoded using
10:40
Bas 64 so let's go ahead and copy out the contents head back over into cyberchef and paste that in and look at
10:47
that nice so we see a username as roman. Maguire aol.com I don't know when was
10:53
the last time I saw aol.com but this is it and we see password pretty decent
10:59
right password strength is very strong ooh Bank of America look at that using
11:05
the same password H let's not do that yeah then we see
11:11
Outlook and here it is pizza jukebox I'm assuming this is the company and the
11:16
same password don't reuse the same password make sure you use the password manager that's why we got to use those
11:23
now looking at the contents we do know that it's scraped all of the passwords stored on Roman's computer and these
11:31
contents were apparently sent on 203816 so
11:36
8386 let's filter for SMTP and see when is the last activity so let's scroll all
11:42
the way down and the last activity happened on 21404 that is 9:40 p.m. UTC time I'll go
11:51
ahead and right click that click on follow TCP stream and the reason why I'm doing this is I want to see if any
11:58
additional f files had been exfiltrated now I'll copy the contents here and head
12:03
back over to cyberchef to see if maybe there's something different and by the looks of it it seems to be the same
12:11
there are three passwords here or three credentials now I guess the next thing we can do is export the file that we
12:17
found in the beginning and generate a file hash on it to perform some additional ENT so let's go ahead and do
12:24
that and now that I think about it actually before we export the file we will likely need to disable Defender
12:30
just in case it does detect the m and block it so I'll go ahead and disable the fender first click on manage
12:36
settings and then I'll disable realtime protection and if you are following along this is a good time to remind you
12:43
again to make sure that you're using a virtual machine because mistakes do happen and sometimes we do silly things
12:49
so let's click on file export objects and click on HTTP and then we want to
12:55
look for the domain performa Das invoices is and this is it TK raore
13:02
protected 99. exe that sounds like a legitimate file name let's go ahead and
13:07
select save and I'll save this with the extension ofm again we do make mistakes and we do
13:16
sometimes execute things we don't want to now this is also interesting we also see bot. what is my IP address.com
13:23
happening quite frequently this gives me an idea that maybe just maybe that once
13:29
executing this Mau or this executable it makes a scheduled query out to what is
13:34
my IP to determine hey if my IP changed at least now I know so now that we have
13:40
our file downloaded let's open up Powershell and then I will type in get-
13:45
file hash and then type in our file of interest and that is our Shaw 256 hash
13:51
so let's go ahead and copy that out and we'll use the best tool ever which is
13:56
virus total click on search and then I'll paste in our file hash that we generated and look at that that is a
14:03
super safe file with 57 vendors detected as Trojan Auto it/ gen 8 is the popular
14:12
threat label and the threat category is Trojan family label as Auto it gen 8 and
14:19
Hawkeye we see 15 plus comments under the community so let's go ahead and click on that and scrolling down we see
14:26
16 comments let's see verdict malicious okay uh malicious yeah malicious okay
14:35
yeah that's good to know let's scroll back up and then select relations I want to take a look at the contacted domains
14:42
and look at that and this is the reason why I wanted to know if this executable
14:47
was responsible for querying what is my IP address because if you recall under
14:54
the wi shark export we see a bunch of scheduled or at least I assume it's a scheduled query outbound to what is my
15:01
IP address.com and looking at virus total we can tell that it does actually
15:07
contact the domain bot. what is my IP address.com so this confirms my
15:13
assumption that this executable does indeed call out to that domain if we click on details we can take a look at
15:20
the creation time of this malware which is over here and the creation time of
15:25
the malware was back on 2019 April ail 10th at
15:30
4:43 UTC time which was on the same day our accountant or at least this
15:37
accountant reported this activity next let's start performing some open source Intelligence on the top talker IP and
15:45
domain we'll start off with the IP address of 21718
15:51
21381 15 I'll be using abuse ipdb we can see that the ISP the internet service
15:57
provider is over VH SAS with the usage type of a data center web hosting and
16:03
Transit it is also located in the country of France abuse ipdb did not
16:09
find this IP in their database now if we were to look at virus total we can see
16:16
that four vendors had flagged this as malicious next we'll take a look at the
16:21
Domain itself pro-in for this I will use a who is
16:27
lookup by domain tools and then I'll paste in the domain and hit search and we see that performa dasin voices.com is
16:35
for sale so unfortunately with a free account we cannot see the history of
16:40
this domain but we can see two changes on two unique IP addresses over 5 years
16:47
maybe something to note back in virus total I'll do the same and we can see 12 vendors reported this as malicious and
16:55
some as fishing the next thing I want to take a look at that is the email domain from the attacker if you recall their
17:03
email address was sales. Dell macwin
17:08
logistics. so I'll go ahead and type in macwin logistics. and perform some ENT on that
17:15
so there's no reports on virus total but there is at least 10 detected files communicating with this domain and
17:22
scrolling down we do see some Hawkeye writeups next I'll utilize who is lookup from domain tools and we can see that
17:28
this domain is for sale as well similar to the other one now we can start building our findings and report it to
17:35
whoever needs to know on April 10th 2019 at 20
Recap
17:41
3754 UTC time the user had accessed the site Prof
17:48
forino and requested the file named TK raore protected 99. exe based on open
17:56
source intelligence this file is classified as a Trojan with a Mau label
18:01
of Hawkeye Hawkeye is a Mau family that is known for its key logger capabilities
18:07
Hawkeye is usually delivered via fishing and this file is known to query a site
18:12
called what is my IP address.com and evidence shows that the host beijing--
18:19
5cd 1-pc was seen querying the site what is
18:24
my IP address.com at 2038 15 immediately
18:30
there was communication towards a mail server at 23229
18:36
16269 which an account of sales. Dell macwin
18:42
logistics. was used to authenticate at 203816 contents containing passwords
18:49
related to Roman Maguire were seen being sent to the email address of sales. Dell
18:56
mawin logistics. the same content containing passwords was sent again to
19:01
the same email at 23843 and the last male activity
19:07
occurred on 21404 based on our evidence the total duration of compromise appears to be
19:14
between 20 3754 to 2144 which is 1 Hour 2 minutes and 10
19:23
seconds it is important to take a step back and recap your investigation every
19:28
now and then especially if you are performing an investigation that involves multiple machines because it
19:35
can get pretty overwhelming especially if you don't take any notes now let's
19:41
finish this up by completing the questions on Cyber Defenders how many packets does the capture have we can do
Questions
19:48
this by going over to wire shark going to statistics capture file properties
19:53
and then under packets captured we see 403 so the answer answer to this would
19:58
be 403 at what time was the first packet captured so again we can go back into
20:06
our capture file properties and look under the first packet this is the time that we want to copy and just looking at
20:12
the format at the very end it has three characters so I'm assuming it is wanting
20:17
to include UTC the time zone what is the duration of the capture
20:23
head back over to the file properties and we see the duration of 1 Hour 3 minutes and 41 seconds copy that paste
20:31
that in there what is the most active computer at the link level link level is
20:36
Layer Two so your Mac address we can get this information by clicking on statistics conversations and then we can
20:43
copy the value of address a I'll paste that in manufacturer of the nick of the
20:50
most active system at the link layer now if we look at our layer 2 address you
20:56
want to take the first three bytes of the MAC address which in this case is
21:03
00802 and then search up wire shark oui as this will provide you with a lookup
21:09
to see what the vendor is for this Nick and then we see that it's hulet Packard
21:14
AKA HP and it seems that the format wants a dash so I'll type in a dash
21:22
where is the headquarter of the company that manufactured the nick of the most active computer at the link level I'll
21:29
just type in hulet Packard and I'll type in headquarters we see the company headquartered in Paulo Alto California
21:38
so that is likely the answer here Paulo Alto question number seven the
21:43
organization works with private addressing and netmask of sl24 how many computers in the
21:50
organization are involved in the capture we can find this out by click on
21:55
statistics click on end points go over to ipv4 and then I'll sort it by address
22:01
just to make sure all of the internal private IP addresses are at the top and
22:06
we can see that there is a 10. 4.10.2 so this is likely
22:12
1.4 2132 so three and then we see at 255 and
22:19
this is a broadcast address so that will not count so I am going to say that there are three host or at least three
22:26
computers in this case and that is correct nice what is the name of the most active computer at the
22:34
network level if you recall when we followed the SMTP traffic we saw that
22:39
the host name was Beijing Das something Beijing D5 cd1 DPC so I'll go ahead and
22:47
paste that in there what is the IP of the organization's DNS server to find this answer we can go ahead and search
22:53
up and filter DNS and we want to look at the responses is which in this case is
23:00
responding from The Source Port of 53 and we can see that this traffic here is
23:05
responding to 132 which is our internal IP so we can infer that
23:12
104104 is our DNS server 104104 what domain is the victim asking
23:19
about in packet 204 well let's go ahead and check that out I'll remove DNS and
23:25
look at 204 so this is the packet 20 204 and then scrolling right a bit we see
23:30
that it is querying pro-in voices.com
23:35
pro-in voices.com beautiful question 11 what is the IP of
23:42
the domain in the previous question now if we see the response from that DNS we
23:47
can just expand answers and then we see the address of
23:52
2171 1821 38150 so that is going to to be the IP
23:58
address of our domain of Interest question number 12 indicate the country
24:03
to which the IP in the previous section belongs now we did some ENT earlier and
24:09
we found out that this was located in France what operating system does the
24:14
victim's computer run on ooh this one's interesting so how to find this is we
24:20
can use user agents and to find that we got to look for HTTP traffic so we can
24:26
just go ahead and filter for HTTP right click follow HTTP stream and now we'll
24:32
look at the host request so what is it requesting to the server and that we can
24:38
see that it's using a user agent of Mozilla 4.0 and of course user agents
24:44
can be spoofed but in this case I don't think it will go that far we can see that it's using Windows NT
24:52
6.1 copy that and I'll paste that as the answer now you can go ahead and do some searches on Google Google for Windows NT
25:00
6.1 and just to see what operating system that is so if I do a control F NT
25:06
6.1 I see that it is under Windows 7 and we also have three more values here so
25:13
Windows Server 2008 R2 and Windows multipoint Server 2010 and 2011 what is
25:20
the name of the malicious file downloaded by the accountant so we know that the file was downloaded via http p
25:28
and we can see that it was requested up here and the file name is TK raore
25:34
protected 99 what is the md5 hash of the
25:41
downloaded file now we can do this two ways we can do this via Powershell because we did extract that file we can
25:47
type in get- file hash but this time we'll specifically mention the algorithm
25:53
algorithm as md5 and then we get our nd5 5 HH the second way of doing it is
25:59
looking at virus total let's copy the Sha 256 hash click on details I would
26:05
find the md5 hash right here so we can copy that out and paste that into to our
26:11
answers what software runs the web server that hosts the malware okay let's
26:17
go back into our HTTP traffic and we want to look for the software that the
26:23
server responded with and I see that there is a server light speed so we can
26:29
go ahead and copy that out and hit submit perfect if we don't know what light speed is we can type in light
26:35
speed web server and we see it is an Apache alternative what is the public IP
26:41
of the victim's computer this we can take a look at SMTP if you recall we did see a public IP of
26:49
17366 14612 and paste it in in which country
26:55
is the email server to which the store stolen information is sent I'm guessing
27:00
this question is asking where is the email server located we do know that the
27:06
email server had an IP address of 23. something so let's take a look at it so
27:13
23229 do1 16269 again if you were taking notes you can find that in your notes I'll use a
27:20
different resource called ipinfo.io and then I'll paste in the IP address and we
27:25
can see the country is located at in the US so the United States type in United
27:32
States question 19 analyzing the first extraction of information what software
27:39
runs the email server to which the stolen data is sent we can take a look at the SMTP traffic and then we see that
27:47
there is a software called XM 4.91 this is going to be it I believe
27:54
question 20 to which email account is the stolen information sent head back over to our sntp traffic and we see
28:02
sales. Dell macwin logistics. so I'll go ahead and copy that out what is the password used by
28:10
the Mau to send the email again to find this we can copy the password head over
28:15
to cyberchef I'll just add a new input for now and then throw that in there and
28:21
our password is sales at 23 with a capital s sales at 23
28:28
which maare variant exfiltrated the data ooh Mau variant so if we go back into
28:35
our cyberchef if you recall the subject line or at least in this content it
28:41
shows us as well we see hawkey key logger reborn v9 so this is likely the
28:47
variant of this Hawkeye key logger so I'll just type it in reborn
28:53
v9 what are the Bank of America access credentials username colon password
28:59
let's take a look at our contents scroll down over to Bank of America and we see
29:05
that the username is roman. Maguire with this super trusty password so I will go
29:11
ahead and paste that in and the password itself nice and the last question how
29:19
many minutes does the collected data get exfiltrated well we know that the same
29:25
contents were exfiltrated twice we can take the first time and then subtract it
29:31
from the second time the content was exfiltrated so we go back over to our peap file we can right click follow TCP
29:38
stream and then select the contents we see that the content was exfiltrated on packet
29:44
3196 at 20 3816 so then we can also see that it
29:49
shows a data fragment so let's go back and filter for
29:55
SMTP and then look for the next data fragment that is not under
30:01
203816 and then here is the next data fragment at 20
30:07
4821 so that's about 10 minutes and out of curiosity if I were to scroll down
30:14
just a bit we see another data fragment at 20
30:19
5825 so it seems to be happening every 10 minutes so let's go ahead and type in
30:25
10 and hit submit perfect we just completed this together
30:31
hopefully you have learned a lot from this video and are becoming more confident in your ability in performing
30:36
network analysis from a pcap if you aren't aware I am in the middle of
30:41
creating a course and if you enjoyed this level of detail and instructions
30:46
then you will absolutely love my course this course is going to be tailored for
30:51
those who are wanting to become a sock analyst and also for those that are already in the sock and just want to
30:58
level up their skills you can sign up for the wait list by clicking on the link down below that is it for the video
31:04
and I hope you found that informative if you did let me know by hitting that like button and subscribe if you want to
31:11
remember to stay curious and do things
31:17
differently
