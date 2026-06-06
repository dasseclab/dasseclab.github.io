---
layout: post
author: dasseclab
title: "Network Operating System Command Line Interface Translator"

catagories: nos, network, networking-basics 
---

Working in a multi-vendor network environment often involves a lot of context switching when you have to be on a device's command line. I'm writing this, mostly for myself (solving personal problems is, after all, the best source of why we write software), especially as I am the rare breed of folks who got a lot of their most foundational network experience working in JUNOS so Cisco or Arista feel very strange to me. This has become especially apparent as $DayJob has been implementing SONiC, an open source network operating system for whitebox hardware, which has a completely wildly different structure with it's blend of "traditional" network operating system operational syntax and Linux operational syntax. 