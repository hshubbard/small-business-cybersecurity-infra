# Network Reconnaissance Process

This document describes the steps taken to identify active devices and gather network information on the small business LAN for asset inventory and security assessment purposes. The environment used was a Kali Linux VM running in VirtualBox with bridged networking on the subnet 192.168.0.0/24 (anonymized).

First, the network interface and IP address on the Kali VM were verified using the command:

ip addr show eth0

This confirmed the VM had a valid IP in the LAN subnet (e.g., 192.168.0.X).

Next, an ICMP ping sweep was performed to discover live hosts on the subnet using:

nmap -sn 192.168.0.0/24

To extract useful information such as IP addresses, MAC addresses, and vendor details, the nmap output was parsed with the following command:

nmap -sn 192.168.0.0/24 | awk '
  /Nmap scan report for/ {ip=$5; if(ip=="") ip=$4}
  /MAC Address:/ {mac=$3; vendor=""; for(i=4;i<=NF;i++) vendor=vendor $i " "}
  {if(mac) {printf "%-15s %-20s %s\n", ip, mac, vendor; mac=""; ip=""}}
'

Finally, the parsed and formatted output was saved to a file for documentation purposes using:

nmap -sn 192.168.0.0/24 | awk '
  /Nmap scan report for/ {ip=$5; if(ip=="") ip=$4}
  /MAC Address:/ {mac=$3; vendor=""; for(i=4;i<=NF;i++) vendor=vendor $i " "}
  {if(mac) {printf "%-15s %-20s %s\n", ip, mac, vendor; mac=""; ip=""}}
' > docs/asset-summary.txt

Note that all IP addresses and MAC addresses have been anonymized in the publicly shared documents to protect network security and privacy. This process provides a foundational asset inventory necessary for further vulnerability assessments and network hardening.
