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
We had specific services within our data center architecture that required outbound access to 