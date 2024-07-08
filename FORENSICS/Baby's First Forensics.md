# Baby's First Forensics

Category: Forensics (Beginner)

Points: 100

Solves: 920

>They've been trying to breach our infrastructure all morning! They're trying to get more info on our covert kangaroos! We need your help, we've captured some traffic of them attacking us, can you tell us what tool they were using and its version?

### Solution

Opening up the pcap file in Wireshark, we see that we are trying to flood a website with a ton of HTTP requests to scan for directories:

![Wireshark](/images/BabyForenWireshark.png)

If we follow the TCP stream we can see exactly what requests are happening:

![TCP Steam](/images/BabyForenTCPStream.png)

Looking at the clients `User-Agent`, we can see the system-information right after `Mozilla/5.00`. It looks like the client is using `Nikto v2.1.6` to scan the website.

### Flag

```DUCTF{Nikto_2.1.6}```