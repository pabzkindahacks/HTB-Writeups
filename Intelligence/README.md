# Intelligence - HackTheBox

## Box Info
- **OS:** Windows
- **Difficulty:** Medium
- **IP:** 10.129.95.154

## Enumeration

Ran gobuster and found /documents/ directory. Directory listing was forbidden
so I fuzzed for date-based PDF filenames using ffuf.

## PDF Analysis

Downloaded 95 PDFs and extracted usernames from metadata using exiftool.
Also found a default password in one of the PDFs using pdftotext.

## Foothold

Password sprayed the default password against all 30 users using crackmapexec.
Tiffany.Molina had not changed her default password: NewIntelligenceCorpUser9876.
Accessed the IT SMB share and found downdetector.ps1.

The script runs every 5 minutes and makes authenticated web requests to any DNS 
record starting with "web" using the running user's credentials.

Added a fake DNS record pointing to my machine using dnstool, then started 
Responder to capture the incoming NTLMv2 hash from Ted.Graves.

## Lateral Movement

Cracked Ted.Graves hash with hashcat and rockyou.txt. Password: Mr.Teddy

Ted.Graves is a member of the ITSupport group which has ReadGMSAPassword 
rights on svc_int$. Dumped the GMSA hash using netexec.

## Privilege Escalation

svc_int$ has constrained delegation with protocol transition to WWW/dc.intelligence.htb.
Used impacket-getST to impersonate Administrator and get a Kerberos ticket.
Used the ticket with impacket-psexec to get a SYSTEM shell on the domain controller.

## Flags
- User: 
- Root:
