### pfsense setup for quad nine dns. From [Linuxincluded.com](https://linuxincluded.com/configuring-quad9-on-pfsense/)


### Enable Quad 9 DNS
First things first, after logging into your firewall, go to System -> General Setup so you can change your primary and secondary DNS servers to those of Quad9. While you are there, make sure the “DNS Server Override” and “Disable DNS Forwarder” options are not checked (as shown below). Don’t forget to click ‘Save’ at the bottom.
IPv4
Primary DNS: 9.9.9.9
Secondary DNS: 149.112.112.112
IPv6
Primary DNS: 2620:fe::fe
Secondary DNS: 2620:fe::9

### Enable DNS resolver
Go to the Services -> DNS Resolver and put a checkmark in the “DNS Query Forwarding” if it isn’t there already. Make sure both “DNSSEC” and “Use SSL/TLS for outgoing DNS Queries to Forwarding Servers” are checked too.


### Enable DNSSEC and DNS over TLS
DNSSEC and DNS over TLS are security enhancements Quad9 offers that many other DNS providers do not. DNS over TLS, for example, forces your pfSense firewall (unbound resolver) to encrypt the DNS transaction as it traverses the internet; what that means is a man-in-the-middle on the internet (or a nosy upstream network provider) can’t see which hostnames you are querying and as important, no one can modify the DNS responses. If you want to double-check whether your DNS over TLS configuration is working, you can follow the “Testing DNS over TLS on pfSense” section below.

### Log Config – NXDOMAIN

What’s the best way to troubleshoot? Get familiar with your logs. Better yet, configure them properly when you first set this up. Go to Services -> DNS Resolver -> Advanced Settings and switch the default log level from 1 to 2 (or higher). Don’t forget ‘Save’ and ‘Apply Changes’ once you are done.
Now, you can go to your system logs (Status -> System Logs -> System -> DNS Resolver) and see every DNS query/response. Keep in mind the logs will also show which domains are returning as NXDOMAIN.

### Log Config – Add requesting/source IP to NXDOMAIN response

Because of how Quad9 responds to malicious domain queries, you can see first-hand if any devices on your network are trying to contact known bad guys on the internet. By default though, the originating/source IP address is not recorded with the DNS NXDOMAIN response which would make tracking down the offending client/IP a little difficult. To correct this, add “server:” and “log-replies: yes” to the “Custom options” section of the general settings page of your DNS Resolver configuration (Services -> DNS Resolver) as shown in the first image below (highlighted). You can also copy/paste the code section in the gray box. Note: If you have other custom options there (such as the one added by pfBlockerNG), then add the log-replies option on a new line below it as shown in the second image (highlighted). The “server:” directive only needs to be specified once. Thus, if “server:” is specified on a line with pfBlockerNG already, you don’t need to add it before the “log-replies” line. Don’t forget to hit Save then Apply after you’ve finished adding it. It’s also worth mentioning in unbound documentation, it states “Note that it takes time to print these lines which makes the server (significantly) slower.” I have enabled “log-replies” multiple times on various shapes and sizes of pfSense boxes and I’ve never seen a discernible increase in disk I/O, CPU, memory, etc. but your mileage may vary.


### Testing/Troubleshooting

Below are some various ways to test your configuration whether in Linux/FreeBSD, Windows, or from the browser using the isitblocked.org domain.

#### Linux command line – Not working

`# dig +short isitblocked.org
74.208.236.124`

#### Linux command line – Working

`# dig +short isitblocked.org`

Nothing is returned above.

#### Windows command line – Not working

`C:\Users\User>nslookup isitblocked.org`
`Non-authoritative answer:
Name:    isitblocked.org
Addresses:  2607:f1c0:100f:f000::2d1
          74.208.236.124`

#### Windows command line – Working

`C:\Users\User>nslookup isitblocked.org
*** can't find isitblocked.org: Server failed`

#### Browser

Assuming your configuration is correct, from a browser the isitblocked.org domain should appear as though it is down (below). If you see anything else or your browser gets re-directed to http://www.isitblocked.org/default, then something isn’t working right.


### More Troubleshooting – Present & Future

Either your configuration isn’t working or you realized that at some point, you’re going to need to troubleshoot a domain getting blocked and you wanted to read ahead. 😉 One way to test your new DNS configuration is via the web GUI by going to Diagnostics -> DNS Lookup. Once again, type in isitblocked.org and see the response. If you see a “could not be resolved” as shown in yellow above, congrats! The only issue? That doesn’t necessarily mean everything is working. The DNS lookup via the web GUI performs a sequential lookup of DNS servers. Remember the red warning above? Nonetheless, this can still be a useful step in your troubleshooting endeavors.
