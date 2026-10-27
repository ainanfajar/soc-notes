# The Report — 2026-10-26

**Platform:** [BTLO](https://blueteamlabs.online/home/challenge/the-report-a6dd340dba) · **Difficulty:** Easy · **Category:** Security Operations
**Type:** Research
**Score / mistakes:** 10/10
## Task
The task for this challenge was to answer some questions related to threat report released in 2022 by Red Canary. The questions focus on cybersecurity threat intelligence, including supply chain attacks, MITRE ATT&CK techniques, Exchange Server vulnerabilities, initial access methods, ransomware, and security measures.

## Approach
I approach each questions by looking at the keywords that each questions have. Since the PDF file have 80 pages long, it would take a long time to read and understand each and every threats/attacks. So since the task was to answer correctly each questions, then this is the approach that I went through. 
To give an example, I'll explain about question #5 where it asked about the groups that are leveraging SEO to gain access to the machine that user use. First, I was searching anything related to SEO. And from there, I read the context around what it found to search the answer to the question.
I mostly use this approach for every questions. Only, if I can't find the keyword that I choose on the PDF file, then I just move on to another keyword, and so on and so forth. Still, reading the context around it is the key to answering all the questions.

## Key findings
- **Cybercrime works like a service economy.**
Different groups specialize in different stages: one gains initial access (often through phishing or malware like Qbot or Bazar), then hands or sells that access to a ransomware group. 
This makes it hard to say who is behind an attack, because one intrusion can involve several groups.
- **Ransomware groups don't only encrypt anymore.**
In 2021, stealing data first and threatening to leak it (double extortion) became the standard approach. Backups alone no longer protect an organization, because they don't stop a leak.
- **Supply chain attacks hit many victims at once.**
SolarWinds, Kaseya, and Log4j showed that compromising one vendor or library gives attackers access to everyone who uses it.
- **Many top threats are legitimate tools.**
Mimikatz, Cobalt Strike, Impacket, and BloodHound are used by both attackers and security testers. Seeing the tool isn't enough to decide; you need to know whether its use was authorized.
- **Users can be tricked into infecting themselves.**
With SEO poisoning, attackers push malicious sites to the top of search results, so the victim downloads malware while looking for something they actually wanted.
- **Attacks are caught early, not at the end.**
Most detections in the report happened during execution, persistence, and privilege escalation, before any damage. PowerShell and the Windows command shell were the most common techniques. Early detection is what prevents a ransomware incident.

## Why it matters to a SOC
**Threat I picked:**
SEO poisoning leading to Gootkit (initial access through search results)

**What I'd see:** 
A user searches for something like a contract template and lands on a compromised site. They download a ZIP containing a JavaScript file and double-click it. 
On the endpoint, that shows up as `wscript.exe` running a .js file from the user's AppData folder, followed by PowerShell with an encoded command (`-enc`). In Windows logs that means process creation events (4688, or Sysmon Event ID 1) with an unusual parent→child chain: `wscript.exe` → `powershell.exe`.

**ATT&CK mapping:**
- T1189 Drive-by Compromise (initial access)
- T1204.002 User Execution: Malicious File
- T1059.007 JavaScript, T1059.001 PowerShell (execution)
- T1027 Obfuscated Files or Information (the encoded command)

**What I'd do as L1:**
- Treat it as a true positive until proven otherwise. Legitimate software rarely launches PowerShell from wscript.
- Isolate the host, since Gootkit can lead to Cobalt Strike and then ransomware
- Check the browser history for the download URL and look for other hosts that visited the same site
- Escalate to L2 with the process tree, the file hash, and the URL

**About the topic:**
- MITRE ATT&CK was new to me. I now understand it as a shared, globally used knowledge base of real attacker tactics and techniques, and that security teams use its IDs (like T1059 for command and scripting) to describe what they detect.
- I already knew about attacks like SEO poisoning in general, but not the names of the threats and groups behind them. Connecting a technique to real threat names makes reports and alerts easier to understand.
- Also, threat reports age quickly. This one covers 2021, so I'll compare it with newer reports from Red Canary and other vendors.

**About researching:**
- Searching the PDF by keyword and reading the surrounding context was faster than reading all 80 pages, and it got me through every question.
- When a keyword didn't match, switching to related terms worked better than rereading.
- Next time I'll skim the table of contents first. This report is organized into trends, threats, and techniques, so knowing the structure would have told me which section to search before I started.
