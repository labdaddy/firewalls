## Sophos UTM 9 DNS
This information is copied directly from a Sophos official knowledgebase [article.](https://support.sophos.com/support/s/article/KB-000034974?language=en_US)


Overview
Solid DNS performance is integral to achieving maximum performance with your UTM. It is therefore important to optimize the DNS resolution process.  This article provides a set of general guidelines for configuring DNS on the Sophos UTM to provide fast, reliable and redundant DNS services. This will also enable internal resolution of hostnames for the UTM Web Reports.

The following sections are covered:

    What to do
    Related information
    Feedback and contact

Applies to the following Sophos products and versions
Sophos UTM

#### What to do
The steps below are suggestions for a generic setup and as such your company or network requirements may differ from these examples.

These suggested best practices provide the following benefits:

    DNS requests for users are cached both on the AD DNS server and the UTM, improving DNS performance.
    By pointing your AD DNS server to the UTM, you will protect your internal DNS servers from DNS Poisoning.
    If you assign the AD DNS server as the Primary DNS and the UTM as the Secondary DNS for your workstations, you provide DNS redundancy for your workstations.
    Creating an internal DNS Request Route with a PTR will allow the UTM to generate hostnames instead of IP addresses in the Web Proxy and other reports.

#### DNS allowed networks

    Browse to Network Services > DNS > Global
    Depending on network configuration either add:
        The internal networks if clients use UTM as DNS server
        The DNS servers if clients use an internal DNS server for DNS requests

#### DNS availability group

    Browse to Definitions & Users > Network Definitions and click on +New network definition.
    Configure two separate network definitions with the following properties:
        Name: Google DNS 1 & Google DNS 2
        Type: Host
        IPv4 Address: 8.8.8.8 & 8.8.4.4
    Browse to Definitions & Users > Network Definitions and click on +New network definition.
    Configure the definition as follows:
        Name: Google DNS Servers
        Type: Availability Group
        Members:
            Google DNS 1
            Google DNS 2

Note: The use of Google DNS is only one suggestion, any public DNS provider would do. Please pick the one you trust and use that. Cloudflare's new DNS resolver at 1.1.1.1 would also be a valid option. 

#### DNS forwarders

    Browse to Network Services > DNS > Forwarders.
    Select the option Use forwarders assigned by ISP.
    Remove any internal DNS servers from this list.
    Add the availability group Google DNS Servers created earlier.
    Apply changes.

#### Request routing

    Browse to: Network Services > DNS > Request Routing and select +New DNS request route.
    Configure the rule as follows
        Domain: [Your domain]
        Target Servers: [Your internal DNS server]

Note: If this is a multi-domain environment you may need to configure multiple request routes.

#### Reverse DNS
With this PTR request route, the UTM can list machine names instead of internal IP addresses in the reports.

    Browse to Network Services > DNS > Request Routing and select +New DNS request route.
    Configure the rule as follows:
        Domain: [PTR record for your network]
        Target Servers: [Your internal DNS server]

Note: An example PTR record for an address range of 172.16.20.0/24 would be 20.16.172.in-addr.arpa.

#### Network Configuration
Although not required if the above options have been configured, you may want to consider setting your workstations to use your internal DNS server as their DNS server rather than the UTM.  Make sure the UTM is configured as a forwarder on your internal DNS server.  

The result of this would be internal DNS requests would go directly to the DNS server rather than being relayed via the UTM.  The tradeoff is external DNS requests would now have to be relayed via your internal DNS server to the UTM.

