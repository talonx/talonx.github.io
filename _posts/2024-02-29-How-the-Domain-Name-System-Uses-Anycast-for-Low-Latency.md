---           
layout: post
title: How the Domain Name System Uses Anycast for Low Latency
date: 2024-02-29 00:00:00 IST
comments: true
categories: anycast dns computernetworking
---

In this article, I will explore what Anycast is in internetworking and how it is used to reduce latency.

Anycast is a concept that involves a group of servers that share the same IP address, and the server that is closest to
the client gets to serve the request. This definition raises some questions — won't there be IP conflicts? How is "closest"
determined? How does the request reach the "closest" server?

We will take the example of DNS throughout this article. All major DNS providers use Anycast.

First, let's look at how DNS resolution works.

## The Mechanics of DNS Resolution

A request for the domain *google.com* from your browser goes to a DNS resolver, which resolves it to an IP address. 
This resolution happens by querying nameservers recursively. Why recursively? Each query in the recursive process resolves 
one part of the domain and the process starts from the tail-end.

The resolver is usually on your laptop (e.g. unbound or resolved on Linux). It contacts one of the 
[13 root nameservers](https://www.iana.org/domains/root/servers){:target="_blank"} first. From there, it fetches the IP
of the nameserver that knows about the TLD (top-level domain), in this case, *.com*. Next, it contacts the *.com* nameserver 
to ask who knows about *google.com*. The response is the IP of another nameserver — called an authoritative nameserver. 
The authoritative nameserver responds with the IP address of *google.com*. If we had queried for a subdomain of *google.com* 
(www.google.com or images.google.com) the query would have continued similarly.

What's important to note here is the server that responds at each step.

What if one or all of the 13 nameservers, or the other servers in the chain, were down or unreachable because of hardware failure,
a damaged undersea cable, or a Distributed Denial-of-Service (DDoS) attack?

In reality, the 13 servers are 13 IP addresses, each backed by multiple actual servers. So are the TLD and 
authoritative nameservers. So which server actually responds to our DNS resolver query? That is where Anycast comes in.

We have to dive a bit into how routing works on the internet to understand this.

## Routing on the Internet

The client (the resolver) gets the IP address of a root nameserver and it sends out a query — let's call it Q. 
How is Q routed?

The internet is made up of Autonomous Systems (ASs) — blocks of network owned by different entities, 
many of them ISPs. Each AS knows how to route packets within its own network, and it advertises the network prefixes to
which it can route. These prefixes include its own network prefix as well as other ASs it connects to and to which it
can forward packets. Routers in one AS announce these advertisements using the Border Gateway Protocol (BGP) to routers in other
ASs. This is how routers know how to send a packet originating in one AS to its destination. BGP is used for most of the routing on the internet.

Our Q will also use BGP. If we had just one physical server for a root nameserver IP (let's say 198.41.0.4),
Q will get routed through various ASs until it reaches the root nameserver. BGP will use its shortest path algo to send the packet to 198.41.0.4.

But what if
- The client and 198.41.0.4 are far away (as measured by BGP).
- There is a damaged undersea cable, further lengthening the path Q has to take.
- 198.41.0.4's data center has a power failure. Q will never receive a response. If 198.41.0.4 were to be replicated 
behind a load balancer and multiple data centers, a single network disruption could still make it unreachable.

Anycast is used to mitigate such issues.

## Anycast in a Nutshell

Multiple servers in different locations (ASs) announce the same address (198.41.0.4 in this example) to their routing device.
This is possible because internet routes for a particular prefix can come from 
[multiple ASs](https://bgp.potaroo.net/as6447/bgp-multi-org-prefix.txt){:target="_blank"}. BGP uses this information to 
create routes. When Q reaches its first routing point in Q's AS, BGP calculates the shortest path to 198.41.0.4 from Q's AS.
This router might have multiple paths to 198.41.0.4, which in reality points to different servers - but the router thinks 
they are the same endpoint through different routes. Based on the client's location, and thus the path, the actual server to
which 198.41.0.4 is mapped might be different. The packet gets routed to the closest server which answers to 198.41.0.4.
A client in a different location might get routed to another server which also answers to 198.41.0.4.

The servers are geographically distributed, which helps to prevent or minimize disruptions from outages.

If you think about this for a moment, some things stand out:
- All servers that answer to 198.41.0.4 must have the same information.
- The admin of 198.41.0.4 should be able to announce the same IP address into the routing system at multiple points.

Anycast is not a different protocol, does not need any different hardware, and does not require any special capabilities.

## Anycast for Stateful Applications

The original RFC [describing](https://datatracker.ietf.org/doc/html/rfc1546){:target="_blank"} Anycast raised some interesting points.

What stops a packet from reaching multiple servers since the Internet Protocol (IP) is allowed to misroute and duplicate packets?

Imagine that Q reaches its target but the ACK is lost. The packet will get redelivered, but will it reach the same server as
before or a different server? What if BGP's shortest path algorithm determines a new path in the time between the redelivery 
attempts because of a change? Does it even matter?

At the network level, these issues matter for stateful protocols like TCP. TCP's connections will get reset frequently 
if the destination server changes in the middle due to routing changes.

DNS queries use mostly UDP, so these issues don't arise. However, if other applications using UDP attempt to maintain stateful 
connections, they might run into such issues when using Anycast. The RFC goes on to say:
> *"The obvious solutions to these issues are to require applications which wish to maintain state to learn the unicast address of their peer on the first exchange of UDP datagrams or during the first TCP connection and use the unicast address in future conversations."* 

What about the application level? Can we use Anycast?

We can, in theory, but it would restrict application capabilities and raise new challenges:
- Transactions will have to be short-lived enough to get routed to the same server.
- Servers will have to synchronize data between themselves.

But wait, many Content Delivery Networks (CDNs) also use Anycast for data transfer over HTTP. HTTP is a stateless protocol 
but uses TCP which is stateful. So how does it work?

CDNs mostly serve short-lived, static content that can be served with a single request, so this issue is of 
low-importance for such cases. There are also some studies indicating that the actual "switching" of servers due to routing 
changes is [very low](https://archive.nanog.org/meetings/nanog37/presentations/matt.levine.pdf){:target="_blank"} (PDF link). 
For longer-lived connections, some services use a strategy where the initial address 
reached using Anycast is used to redirect the client to a nearby server, which is possibly co-located in the same data center. 
All subsequent communication happens using that.

Anycast is not a load-balancing mechanism. Also, BGP's path selection algorithm uses the AS_PATH metric between ASs, which 
determines the shortest path based on the number of ASs that have to be traversed. It does not take into account network delays or capacity.

## Anycast in DDoS Mitigation

Anycast is also used to mitigate DDoS attacks. Eliminating a single point of failure improves resiliency of the service. 
In the case of DNS, the root nameservers are replicated. During an attack, the bulk of the DDoS traffic can be localized
to specific regions, and thus avoid taking down the entire service.

Anycast remains a key mechanism for global internet services like DNS and CDN to reduce latency and has been put into operation by most big providers.

### References
- List of Anycast-related RFCs
  - Host Anycasting Service [https://www.rfc-editor.org/info/rfc1546](https://www.rfc-editor.org/info/rfc1546){:target="_blank"}
  - Distributing Authoritative Name Servers via Shared Unicast Addresses [https://www.rfc-editor.org/info/rfc3258](https://www.rfc-editor.org/info/rfc3258){:target="_blank"}
  - Operation of Anycast Services [https://www.rfc-editor.org/info/rfc4786](https://www.rfc-editor.org/info/rfc4786){:target="_blank"}
  - Architectural Considerations of IP Anycast [https://www.rfc-editor.org/info/rfc7094](https://www.rfc-editor.org/info/rfc7094){:target="_blank"}

- Submarine Cable Map [https://www.submarinecablemap.com/](https://www.submarinecablemap.com/){:target="_blank"}

Thank you for reading, and do reach out via comments or on [Twitter](https://twitter.com/talonx){:target="_blank"} if you want to chat or share your thoughts.
