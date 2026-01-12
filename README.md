# blue-team-labs1
# Sysmon Process Creation Labs - Windows telemetry Validation
`I deployed Sysmon, generated telemetry, analyzed process creation events, and validated parent-child execution chains`
#### Date and Time: 1/11/2026, 6:29PM to 6:30PM
## Objective:
To validate Sysmon installation and observe process creation events using PowerShell commands.
## Environment:
- OS: Windows 11
- Tool: Sysmon
- Log Source: Microsoft-Windows-Sysmon/Operational
## Steps Performed:
1. Verified Sysmon service was running using `sc query sysmon64`
2. Executed `powershell.exe`
3. Ran commands:
	- `whoami`
	- `ipconfig`
4. Opened Event Viewer > Sysmon Operational logs
5. Filtered for Event ID 1 (Process Create)
## Results Observed:
- Detected powershell.exe execution
- Detected child processes:
	- `whoami.exe`
	- `ipconfig.exe`
- Verified parent-child relationships:
	- cmd.exe > powershell.exe > whoami.exe/ipconfig.exe
## Analysis:
	These events demonstrate successful telemetry capture of process execution  and command-line activity. This visibility is critical for detecting living-off-the-land techniques used by attackers.
## Learning Outcome:
	- Understood process creation logging
	- Learned to analyze parent-child relationships
	- Validated Sysmon pipeline functionality

##PART 1 - What I actually did (SOC language)

When I ran : powershell.exe, whoami, ipconfig
- I did a 3 critical things from a defender perspective:
	1. Spawned a scripting engine (PowerShell)
	2. Enumerated user context (whoami)
	3. Enumerated network configuration (ipconfig)
- To attackers, this is:
	Reconnaisssance phase
- To SOC analysts, this is:
	 Initial telemetry of possible intrusion activity
`*So even though its benign, the behavior pattern is attacker-like.`

## PART 2 - Why PowerShell is important in Cybersecurity

PowerShell is:
	Built into Windows
	Trusted by the OS
	Extremely powerful
	Used by attackers more than malware EXEs
*This is called: Living Off The Land (LOLBins)

Attackers love PwerShell because:
	No download needed
	No antivirus trigger
	Blends with normal admin activity

When :
	ParentImage: cmd.exe
	Image: powershell.exe
`*Brain should go "hmm, why is powershell being launched here?"`
#### This is SIGNAL, not noise.

## PART 3 - Understanding the anatomy of my Sysmon Event

Image: powershell.exe
ParentImage: cmd.exe
CommandLind: powershell.exe
User: test_user
IntegrityLevel: Medium

Analyst Interpretation:
FIELD				WHY IT MATTERS
-Image				- What executed
-ParentImage		- Who launched it
-CommandLine		- How it was used
-User				- Which account is involved
-IntegrityLevel		- Privilege level

`*WHO did WHAT, HOW, and from WHERE`
#### This is the CORE of Incident Triage.

## PART 4 - Incident Triage Anatomy

CONTEXT
Ask:
	- Is this user an IT admin?
	- Is this during business hours?
	- Is this normal for this role?
if:
	HR staff launching PowerShell at 2AM
	That's Suspicious
if:
	IT admin running scripts at 10AM
	That's probably normal

### PARENT-CHILD RELATIONSHIP
	- Abnormal chain
cmd.exe->powershell.exe->whoami.exe/ipconfig.exe
*This execution chain analysis
Always ask: "Does this chain make sense?"
	- Normal chain
explorer.exe->cmd.exe->powershell.exe
	- Suspicious chain
winword.exe->powershell.exe
`*Possible macro attack`

### COMMAND-LINE
*Critical
	( powershell.exe )
	CLEAN Command - "No obfuscation. Likely interactive"
Example of encoded command > likely malicious
	( powershell.exe -enc SQBFAFgAIAAoACgA.... )

### USER & PRIVILEGE
IntegrityLevel: Medium
User: test_user

- Medium = Normal
- Not SYSTEM
- Not ADMINISTRATOR
`*"Low privilege. Limited impact."`

Example of high risk:
User: SYSTEM
IntegrityLevel: High

### FREQUENCY & BEHAVIOR
- Is this happening once?
- Or 500 times?
- On one host or many?

One PowerShell = FINE
PowerShell on 30 endpoints = INCIDENT

## PART 5 - MAPPING

STAGES				COMMAND
-Initial Access		-powershell.exe
-Discovery			-whoami
-Discovery			-ipconfig

## FRAMEWORKS: MITRE ATT&CK
-T1059: Command and Scripting Interpreter
-T1033: Account Discovery
-T1016: Network Configuration Discovery
