---
layout: post
author: dasseclab
title: "Load Balancing Load Balancers: Converting to Active Availability"

catagories: load balancing, NAT
---
# Overview
I generally have a policy that I don't talk about "job-specific" stuff on the Intarwebs; however, I decided to write this up for a couple of reasons. The first - as I get further away from the job I did this project at, the more I forget the day to day but this is a good chunk of my resume, so I want to have a reference to remember what I did. The second - I've actually forgotten enough, most likely, that there are very specific details are lost and this is discussed in broad generalities. 

In a former position, I was working with load balancing clusters that provided us Large-Scale NAT (LSN) capabilities for our massive data center presences. The idea of these LSN clusters was that access would be granted to specific hosts that met business justifications for having outbound public Internet access - HTTP/S proxies of course but some other internal systems for this access also met this threshold. And like every organization with an IPv4 network, IPv4 address space is a precious, limited resource. And like many large enterprises, IPv6 networks are too new and scary (still). This project I organized allowed us to preserve IPv4 space, improve reliability and mitigate long-term cost incursion. Its success also wouldn't have been possible without a lot of senior (and senior-plus) engineers answering my questions and reviewing my work. 

# Problem Statement
We had specific services within our data center architecture that required outbound access to different services. To perform network address translation (NAT) and meter the connections, we used clusters of load balancers based on internal service type. These load balancer clusters were originally designed with an High-Availability (HA) architecture and adequately scoped with lots of bandwidth for a long runway of opertaion. However, as all runways do, the runway began to run out and capacity through the clusters began to suffer performance hits and trigger alarms for our on-calls. If left untended, the problem would only get worse and the packets that dropped on the floor would eventually cause true perfomance issues for our customers. We explored a few solutions and eventually decided upon a complete redesign of the system functionality.

# The Origin Story: High Availability Large Scale NAT
<< describe the system design for the HA architecture >>

# Obligatory "Use IPv6, You Idiot" Statement and Defense


# Proposed Remedies
## Proposal: Do Nothing
What most engineers don't understand is that 'do nothing' is actually an option. If we do not evaluate the cost of 'do nothing', it makes it more difficult to assign (usually) already strapped resources to a project and, more importantly, it immediately gives credence that every complaint is a problem that requires engineering a solution to, which isn't always the case.

In this specific instance, we'd looked over several months of data and there were periodic instances where traffic through the LSN load balancers would peak, causing pages to oncall. During these peaks, we would see some packet loss, though the interfaces were never fully saturated for long. Interfaces would fall back to about 60% utilization after about an hour to ninety minutes. Peaks were rarely correlated, such as trying to pinpoint a specific service. Given the near random nature and infrequency of the events - do nothing could have been a real option. It'd have been a minor pain for on-call to have to look at an event but also knowing that it wasn't severely impactful and would subside on its own could have placed it much lower on the stack of priorities to tackle for another year or so since the runway hadn't completely run out.

What eventually ruled this proposal out as being an option were capacity requests and predictuons coming down the pike for future product launches and needs that expected to double traffic over 18 months and potentially triple over 24 months. If traffic remained within some level of current levels, we could have ridden this out for another year or so with only minor pain points but with this much traffic forecasted, something had to be done.

## Proposal: Expand As-Is == Expensive
Build moar clusters.

## Proposal: Do It In Software

## Proposal: Load Balance The Load Balancers [ACCEPTED]
