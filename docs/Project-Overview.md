# Project Overview

- [Project Summary](#project-summary)
- [Environment and Workflow](#environment-and-workflow) 
- [Simulated DDOS Pattern](#simulated-ddos-pattern)
- [Key Outcomes](#key-outcomes)

## Project Summary

This project focuses on creating a space where I am able to observe network traffic, analyze network events, and understand more about the processes involved in networks. I effectively aimed to build a small but realistic "home SOC Lab" to generate network activity and capture it at multiple layers- it should be seen as more of a learning process rather than an actual tool to secure a network.

The tools I use to achieve this were:
- Wireshark (a packet analysis tool)
- Nmap (a network scanner) 
- Auditd (system level event logging)

These are all incredibly powerful tools that can go extremely low-level, and as such will provide invaluable experience in observing events within my virtual machine's network. They also allow me to follow activity from the initial packets, to the system call that generated it, and finally the logged event recorded by the system.

## Environment and Workflow

The project was entirely built inside an Ubuntu virtual machine- providing a safe space to generate the traffic needed, namely:
- ICMP pings
- Nmap scans
- SSH login attempts

I examined these events from multiple persepectives:
1. **Network layer (Wireshark):** captured packets to understand how the VM communicated externally and internally.
2. **Host layer (Auditd):** inspected the logs produced by the audit daemon to understand how the operating system records network-related activity.

I did not yet cross these perspectives over until the final, most enjoyable part of this project, which is fully covered in the next section; a simulated DDOS attack pattern from my local machine to my virtual machine.

## Simulated DDOS Pattern
Due to limitations which I will explain at the end of this document, I can't implement an actual DDoS attack. However, I can implement the pattern of a DDoS attack and observe them.

## Limitations

## Key Outcomes
Through this project, I gained:
- A practical and foundational understanding of packet structure, protocol behaviour, and traffic patterns
- Experience using Wireshark's filtering and analysis tools
- Familiarity with the process of Nmap's scanning techniques and the SSH login process
- Insight into how Linux logs system activity through Auditd
- The ability to correlate packet-level data with system-level logs by simulating the patterns of an attack

This project demonstrates a small but functional SOC-style analysis environment. I developed a clearer understanding of how network activity is represented at different layers of a Linux system.

For more detailed technical notes, examples, captures, and my own journey of learning the foundations of the applications, see the accompanying Wireshark Overview and Auditd Overview documents.
