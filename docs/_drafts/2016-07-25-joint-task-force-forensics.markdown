---
layouts: post
author: dasseclab
title: "Joint Task Force: Forensics and Anti-Forensics"

categories: netsec
---
Within the digital security realm – encompassing computer, networks, data and information – there has been an adage about learning to defend comes from learning to hack. It’s probably based on some quote, maybe Sun Tzu (cause everyone in InfoSec likes to quote Sun Tzu at one point or another), that in order to learn how to properly defend against an attack, one must be the attacker, or think like an attacker. While the paradigm may be cliche, it is still valuable advice.


The paradigm of ‘attacker’ and ‘defender’ often conjure up token images – in military usage, a distant ‘them’ that isn’t known until an attack commences and data comes in; in a corporate enterprise setting, some unknown hacker intruding upon the corporate network. But defining who/what is in the ‘attack’ role is ultimately determined by the what the threat model is. A ‘defender’ is simply the role receiving the attack. Threat Modeling is determining potential attack vectors, weaknesses and overall risk in scenarios, often backed up by either case study or test data (say, in the form of a vulnerability assessment or penetration test). This modeling can be used by a defender playing the guise of the attacker to determine the points in which their defense needs to be most effective.

Looking at the field of digital forensics, we can go back to this old CSO article, entitled [The Rise Of Antiforensics](http://www.csoonline.com/article/2122329/investigations-forensics/the-rise-of-anti-forensics.html). The article details information security professionals who have written software that “fools” (author’s words) industry standard computer/digital forensics tools and the article’s early tone seems to indicate a bias against antiforensics and it’s tools as they would be harmful to business and law enforcement. The article itself comes around to a more nuanced view towards these tools; however, I want to explore a different nuance here: antiforensics has, in itself, an intrinsic value to a business organization’s information security program, just as forensics does.

Incorporating Digital Forensics

Incorporating digital forensics into your operations is, from a reasoning standpoint, fairly simple: in the event something happens – being able to identify a root cause. Just as senior managers would be interested in why a marketing campaign was failing, not investing in digital forensic capabilities for your disaster recovery or incident response staff can not only properly identify root cause scenarios but can be built in to processes going forward to mitigate it from occurring again. Some of this can be as simple as change management rules, system event logging and monitoring, while more specific software, tools or personnel can be brought in to augment in the event of an incident that requires it.

When it comes to these simple things – logging, monitoring, processes – these are the things that should be accounted for within development and architecture/engineering cycles for systems. Having documentation that is accurate and up-to-date detailing the system overview, how logs aggregate, expected monitor points, etc. can all aid when performing forensic analysis.

But There’s The Flip Side – Antiforensics

There are two areas in which we should look for when we consider the term antiforensics –  prevention and destruction.

Prevention, to me, signifies the prevention of a breach by technical controls – think like encryption. If I have a drive that leaves my possession that is properly encrypted, it should be able to withstand forensic tests and not yield its data to an attacker.

Destruction, though, can be a mix of technical or physical means to prevent a breach. Technical destruction methods would be overwriting platter drives or using Secure Erase features on SSDs. Disk destruction is a topic well known to many organizations because, well, little has changed. Storage capacity may have become cheaper and rotation speeds faster, if your media rotates at all, physically destroying a hard drive has changed very little in probably 30 years.

Physically destroying media is probably still the most surefire way to prevent data from being compromised on a drive. Larger scaled operations will probably utilize pneumatic crushers but in a pinch, a good old fashioned power drill will work.

Marrying The Two Together

In one hand, we have preservation of data used for root cause analysis and in the other, we have methods to destroy data. At first glance, the two seem opposed and there is room for only one in organizational security policies. Indeed, one might argue that according to the CIA Triangle model of Information Security (weighing the trade-offs to ensure the Confidentiality, Integrity and Availability of data),  only the preservation of data through encryption to mitigate forensic threats best fits the model, leaving data destruction out of the model at all.

The CIA model runs of the premise that information needs to be accessible though. In the event of where information must remain confidential with near-zero chance of data or information being accessible or recoverable from a piece of media. However, some business cases might require data to be inaccessible. Equipping your operations staff with the right tools and training is essential for making sure your organization is prepared for an event where data needs to be secured for retrieval later or destroyed beyond any recognition. While information security professionals are entrusted to safeguard information, it’s equally important to have options to be able to act quickly in the event either solution is needed.