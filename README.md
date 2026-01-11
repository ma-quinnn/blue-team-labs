# blue-team-labs1
# Sysmon Process Creation Labs - Windows telemetry Validation
`I deployed Sysmon, generated telemetry, analyzed process creation events, and validated parent-child execution chains`
Date and Time: 1/11/2026, 6:29PM to 6:30PM
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
