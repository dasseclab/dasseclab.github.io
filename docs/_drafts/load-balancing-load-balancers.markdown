---
layout: post
author: dasseclab
title: "Load Balancing Load Balancers: Converting to Active Availability"

catagories: load balancing, NAT
---
# Overview
I generally have a policy that I don't talk about "job-specific" stuff on the Internets; however, I decided to write this up for a couple of reasons. The first - as I get further away from the job I did this project at, the more I forget the day to day but this is a good chunk of my resume, so I want to have a reference to remember what I did. The second - I've actually forgotten enough, most likely, that there are very specific details are lost and this is discussed in broad generalities. 

In a former position, I was working with load balancing clusters that provided us Large-Scale NAT (LSN) capabilities for our massive data center presences. The idea of these LSN clusters was that access would be granted to specific hosts that met business justifications for having outbound public Internet access - HTTP/S proxies of course but some other internal systems for this access also met this threshold. And like every organization with an IPv4 network, IPv4 address space is a precious, limited resource. And like many large enterprises, IPv6 networks are too new and scary (still). This project I organized allowed us to preserve IPv4 space, improve reliability and mitigate long-term cost incursion. Its success also wouldn't have been possible without a lot of senior (and senior-plus) engineers answering my questions and reviewing my work. 

# Problem Statement
We had specific services within our data center architecture that required outbound access to different services. To perform [Network address translation (NAT)|https://en.wikipedia.org/wiki/Network_address_translation] and meter the connections, we used clusters of load balancers based on internal service type. These load balancer clusters were originally designed with an High-Availability (HA) architecture and adequately scoped with lots of bandwidth for a long runway of opertaion. However, as all runways do, the runway began to run out and capacity through the clusters began to suffer performance hits and trigger alarms for our on-calls. If left alone, the problem would only get worse and the packets that dropped on the floor would eventually cause true perfomance issues for our customers. We explored a few solutions and eventually decided upon a complete redesign of the system functionality.

# The Origin Story: High Availability Large Scale NAT
Any number of network devices, by the 2010s, could do address translation. As mentioned, these were for massive data center presenses/campuses, so using a firewall device could cause significant bottlenecks at scale and the security features weren't necessarily in-scope with primarily outbound initiated connections. Using our core and edge routers that we were already using for Internet connectivity, we probably could have done address translation, too but that would increase the scope of impact if the routers became overwhelmed. Instead, we opted for new hardware load balancers - new clusters to isolate their function from other LB services - which catered to the CG-NAT ([RFC 6598|https://datatracker.ietf.org/doc/html/rfc6598]) market, thus having robust address translation software running on it. 

We scoped the clusters out into initially two clusters - one focused on external proxy services and services like DNS or NTP and one devoted to a class of storage services that backed up their coldest storage to public cloud storage providers. As our serving infrastructure grew, a third cluster was added as a second resource for cold storage. The fourth and final cluster was initially developed for more latency-sensitive backend services but it also ended up sharing some of the load from the core services proxies. 

# Obligatory "Use IPv6, You Idiot" Statement and Defense
Of couse, NAT is only a stop gap for conserving IPv4 address space. If we truly, TRULY, wanted to move away from address availability constraints, we would be using IPv6. Anytime anyone mentions anything related to NAT, just about, you can guarantee there will be someone saying you should be using IPv6 instead of NAT.

And they're not entirely wrong - given that the purposed of address translation was for traversing the Internet and for well known functions and cloud providers, IPv6 at least externally could have been a contender. But depsite the scale of our environment, IPv6 was consistenly de-prioritized, often from software engineering teams. I guess having `dead::beef` being a completely valid IP address was a little too intimidating. Given that, all the IPv6 resoucring was all geared towards peering and transit on the Internet but after routing, all service connectivity was done using IPv4. That's the funny thing about running company networks, is that engineering often has to make trade offs and pivots around business requirements, even if those requirements are from other engineers. 


# Proposed Remedies
## Proposal: Do Nothing [REJECTED]
What most engineers don't understand is that 'do nothing' is actually an option. If we do not evaluate the cost of 'do nothing', it makes it more difficult to assign (usually) already strapped resources to a project and, more importantly, it immediately gives credence that every complaint is a problem that requires engineering a solution to, which isn't always the case.

In this specific instance, we'd looked over several months of data and there were periodic instances where traffic through the LSN load balancers would peak, causing pages to oncall. During these peaks, we would see some packet loss, though the interfaces were never fully saturated for long. Interfaces would fall back to about 60% utilization after about an hour to ninety minutes. Peaks were rarely correlated, such as trying to pinpoint a specific service. Given the near random nature and infrequency of the events - do nothing could have been a real option. It'd have been a minor pain for on-call to have to look at an event but also knowing that it wasn't severely impactful and would subside on its own could have placed it much lower on the stack of priorities to tackle for another year or so since the runway hadn't completely run out.

What eventually ruled this proposal out as being an option were capacity requests and predictuons coming down the pike for future product launches and needs that expected to double traffic over 18 months and potentially triple over 24 months. If traffic remained within some level of current levels, we could have ridden this out for another year or so with only minor pain points but with this much traffic forecasted, something had to be done.

## Proposal: Expand As-Is == Expensive [REJECTED]
At the time to consider re-designing the large-scale NAT system, we'd already had two clusters dedicated to one service. Another cluster that was recently turned up had a very small customer base and we could re-purpose it to offload some customers from our paging devices. Each of these devices cost tens of thousands of dollars in initial capital plus annual maintenance fees and when a new cluster is turned up, an identical cluster needs to be deployed in each production data center where the backend services live. Easily, the design of one new cluster, grows from not just one pair but three to five pair of new clusters. The operational overhead of the cluster, once deployed and integrated, is negligible but the upfront costs easily made this proposal very unattractive.

## Proposal: Do It In Software [REJECTED]
Of all the alternative proposals, this was bandied about and explored a good bit before we reached opted to pursue other avenues. In fact, we ended up putting software load balancing into a parking lot to work on a future service - basically pointing out that this was the direction we should be moving but we're currently not in a position to execute now to minitigate today's problems. 

We assessed that our team make up at the time was more of a traditional network engineer profile and did not have enough of the cross-over skillsets needed to run our own software load balancer as a distributed microservice successfully (such as the service not dying in production and causing outages). The team make up was planning to shift though - we already had a few other products that could be run as a distributed microservice - and hiring a few heads on a SRE job ladder was starting to happen. But throwing a project like this at them where there was no middle-ground (hardware-based networking to pure software) while our pagers continued to blow up, was not a good way to solve this problem.

## Proposal: Load Balance The Load Balancers [ACCEPTED]
This proposal, while being a significant technical undertaking, won out on the financial fronts because it was capital neutral immediately as no new equipment needed to be purchased. High Availability architecture means that there are two devices, one in Primary mode processing traffic and another failover unit should the Primary unit fail. This means that the High Availability architecture limits your bandwidth based on the lowest common demoninator being the Primary member of the cluster. Bandwidth requirements were scoped adequately for future growth at implementation but some unforseen growth over the years meant new clusters needed to be deployed. The Active Architecture though, each member node processes traffic acting independently, which means the available bandwidth increases with the scale of the cluster. Active Architecture also provides a way to grow scalably by being able to add new nodes individually rather than having to build out in pairs. 

# Technical Designs
Most of this post so far has been going over the technical design of the differing NAT architectures but the chief other networking component to discuss in technical brief is our data center edge routing layer. This section will explain a few of the device or functional layers to illustrate each layer and then go into the architecture designs.

## High Availability Architecture
### Data Center Edge Routing Layer
Our data center edge routing layer is a network layer that connected each of our production data center facilities via backbone connections to other network PoPs. Unlike other edge designs, our network edge layers actually functioned in two segments. The data center edge did not peer with upstream transit providers or external customers, instead, they peered with other internal routers and load balancers. The other segment of our edge layer, our core router layer, peered with other backbone routers and upstream transit and private interconnection and announced our larger network prefixes. The data center edge announced more specific and anycast prefixes within our network to the core router, which announced the larger prefixes. Production Data Centers started operation with a cluster of two devices for DC Edge routing, which then grew to four routers. Each member of the cluster was peered with another for a full BGP mesh. 

In terms of the Large-Scale NAT clusters, the DC Edge routers were connected to each cluster member in a redundant fashion - for instance, a NAT cluster with 80Gb/s of capacity, each NAT device was connected to each edge router at 4x10Gb/s and aggregated via LACP. When moving away from the High Availability architecture to the Active Architecture, this was retained. The publicly routable (non-RFC-1918) address prefixes were shared from the NAT devices to the Data Center Edges, which would then be announced up and summarized at the core router for advertisement to the general Interet. 

### Large-Scale Network Address Translation Layer
The Large-Scale Network Address Translation Layer (LSN or Large-Scale NAT) was a load balancing product aimed at service providers to perform NAT and CGNAT. In High Availability, the two cluster members were connected with the same 4x10Gb/s connections to each data center edge router with another 10Gb/s connection between each HA device utilizing Virtual Router Redundancy Protocol (VRRP) to designate a cluster leader in the event of a failover. The LSN clusters had the publicly routable IP prefixes configured into NAT groups and an access control list (ACL) of which backend servers were permitted to utilize which NAT group. 

<< network diagram of HA pair architecture >>

### Network Flow
From the backend service, when it requests an external service, 

## Active Availability
### Network 