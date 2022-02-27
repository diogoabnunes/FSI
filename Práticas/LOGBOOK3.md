# Trabalho realizado na Semana #3

## Identificação

- Pulse Connect Secure (PCS) is a product created by the PulseSecure company.
- The service provides a seamless, cost-effective, SSL VPN solution for remote and mobile users from any web-enabled device to corporate resources.
- The versions 8.2 before 8.2R12.1, 8.3 before 8.3R7.1, and 9.0 before 9.0R3.4 were affected by the CVE-2019-11510 vulnerability.
- The vulnerability allows an unauthenticated remote attacker to send a special URI to perform an arbitrary file reading (Directory Traversal).


## Catalogação

- On April 4th 2019, PulseSecure published SA44101 advisory reporting there was a vulnerability that allowed authentication bypass.
- On January 10th 2020, Cyber Infrastructure division of Department of Homeland Security issued Alert (AA20-010A) reporting widespread exploitation activity.
- PCS is implemented across 80% of the Fortune 500 and used by 20 million endpoints. There are ~24’000 PulseSecure servers worldwide.
- Given the associated risk, CVE-2019-11510 has been assigned a maximum CVSS risk score of 10.0 Critical.


## Exploit

- Download the “etc/hosts” file, which contains the internal hostnames and IP addresses associated with the VPN server.
- Download the “etc/passwd” file, which contains the usernames associated with the Pulse Secure VPN server.
- The exploits majority known on this vulnerability scans for vulnerable Pulse Secure servers using directory traversal and downloading /etc/passwd file.
- If the server is vulnerable, directory traversal vulnerability is used to download “/runtime/mtmp/lmdb/dataa/data.mdb” database file and extract username and password.

## Ataques

- The Sodinokibi Ransomware was used to exploit this vulnerability, being injected in several devices and demanding over 10M$ in total.
- Maze (a ransomware gang) attacked SK Hynix, stealing documents, including emails relating to price negotiations with clients like Apple and IBM.
- The Egregor ransomware gang obtained the Barnes & Noble’s Pulse Secure VPN credentials exposing them in the darknet.
- Two of a Vietnamese IT corporation’s IP addresses were exposed in the Pulse Secure lists.
