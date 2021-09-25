## This is taken from a response a forum for bayt.com. Found [here.](https://specialties.bayt.com/en/specialties/q/303010/what-is-the-dns-forwarder/)
A forwarder is a Domain Name System (DNS) server on a network that forwards DNS queries for external DNS names to DNS servers outside that network. You can also forward queries according to specific domain names using conditional forwarders.

You designate a DNS server on a network as a forwarder by configuring the other DNS servers in the network to forward the queries that they cannot resolve locally to that DNS server. By using a forwarder, you can manage name resolution for names outside your network, such as names on the Internet, and improve the efficiency of name resolution for the computers in your network

When you designate a DNS server as a forwarder, you make that forwarder responsible for handling external traffic, which limits DNS server exposure to the Internet. A forwarder builds up a large cache of external DNS information because all the external DNS queries in the network are resolved through it. In a small amount of time, a forwarder resolves a large number of external DNS queries using this cached data. This decreases the Internet traffic over the network and the response time for DNS clients.

A DNS server that is configured to use a forwarder behaves differently than a DNS server that is not configured to use a forwarder. A DNS server that is configured to use a forwarder behaves as follows:

    When the DNS server receives a query, it attempts to resolve this query by using the zones that it hosts and by using its cache.
    If the query cannot be resolved using local data, the DNS server forwards the query to the DNS server that is designated as a forwarder.
    If forwarders are unavailable, the DNS server attempts to use its root hints to resolve the query..

When a DNS server forwards a query to a forwarder, it sends a recursive query to the forwarder. This is different than the iterative query that a DNS server sends to another DNS server during standard name resolution (name resolution that does not involve a forwarder).
