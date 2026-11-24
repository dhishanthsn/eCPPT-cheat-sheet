# Windows PowerShell and CMD — eCPPT Study Notes

> Source: Notion page **WINDOWS_POWERSHELL**  
> Last retrieved: 2026-11-24  
> Scope: Beginner-to-advanced Windows command-line, PowerShell, Windows security, authorised assessment, enumeration, defensive monitoring, and practical labs.
>
> Use these notes only in systems and environments you own or are explicitly authorised to assess.

---

# Study roadmap and navigation

**Purpose:** A structured Windows command-line and PowerShell course for eCPPT preparation, progressing from fundamentals to authorised Windows red-team assessment and blue-team validation.

**How to use these notes**

1. Learn the Windows command line (CMD) and PowerShell foundations before memorising attack commands.
2. Practise each command in a Windows 10/11 or Windows Server virtual machine that you own or are authorised to test.
3. For every technique, record: objective, prerequisites, command syntax, expected output, interpretation, limitations, detection evidence, remediation, and cleanup.
4. Follow the mentor-led roadmap below. CMD, PowerShell, Windows security architecture, authorised assessment, defensive monitoring, and labs are all part of the main course.
5. Revisit the quick-reference section only after you understand the underlying concepts.

## Course progression — Basic to Advanced

| Level | Module | Learning outcome |
| --- | --- | --- |
| Basic | 01. Windows CMD Foundations | Navigate Windows and understand host, user, process, service, and network information. |
| Basic | 02. PowerShell Foundations | Understand cmdlets, help, objects, and the pipeline. |
| Basic → Intermediate | 03. Core PowerShell Commands | Use administration and investigation commands with correct interpretation. |
| Intermediate | 04. PowerShell Scripting | Build safe, reusable scripts with parameters and error handling. |
| Intermediate | 05. Windows Security Architecture | Understand identities, tokens, UAC, ACLs, services, and authentication. |
| Intermediate | 06. Initial Access Rules and Validation | Validate an authorised foothold and follow rules of engagement. |
| Intermediate → Advanced | 07. Post-Initial-Access Enumeration | Collect host, user, process, service, network, task, and domain context. |
| Advanced | 08. Post-Enumeration Analysis | Validate observations, prioritise risk, and decide what to investigate next. |
| Advanced | 09. Privilege-Boundary Assessment | Assess service, task, file, registry, and remote-management misconfigurations safely. |
| Advanced | 10. Defensive Monitoring and Response | Correlate Windows telemetry, preserve evidence, and recommend hardening. |
| Practical | 11. Hands-on Labs | Apply the workflow in an isolated Windows lab. |
| Revision | 12. Quick Reference and Exam Readiness | Consolidate commands and demonstrate end-to-end understanding. |

## How to use the interactive modules

Each module below is a collapsible lesson. Open one module at a time, study the concept, run the guided commands in your authorised lab, complete the practice task, and tick the completion checklist only when you can explain the result without copying the notes.

**Learning rule:** Do not skip ahead just to reach advanced commands. Post-initial-access and post-enumeration work depends on understanding CMD, PowerShell, Windows identities, permissions, and process/service behaviour.

## Course-wide operating rules

- Use only systems and accounts explicitly authorised for the assessment.
- Confirm scope, exclusions, test window, permitted techniques, and stop conditions before testing.
- Prefer read-only collection and least-impact validation.
- Do not dump or reuse real credentials, disable security controls, clear logs, conceal activity, or deploy covert persistence.
- Use synthetic accounts and benign marker artefacts in isolated labs.
- Preserve timestamps, host identity, account context, commands, output, and errors.
- Treat an observation as a hypothesis until its prerequisites and impact are validated.
- Check PowerShell version, module availability, privileges, and operating-system compatibility before using a command.

## Technical accuracy standard

- Use `Get-CimInstance` for modern CIM/WMI queries; `Get-WmiObject` is legacy and unavailable in PowerShell 7.
- Use `Get-WinEvent` for modern event-log queries and EVTX files; `Get-Content` reads text files.
- `Get-Process` CPU is accumulated processor time, not instantaneous CPU percentage.
- PowerShell Execution Policy is not a security boundary.
- Event IDs are investigative leads; correlate identity, logon ID, host, process, timestamp, and adjacent events.
- `TrustedHosts` is a WinRM client-side setting, not an inbound firewall allow-list.
- Constrained Language Mode is normally enforced through application-control policy, not a simple registry toggle.

---

# PowerShell and Windows Security — Mentor-led Course

**Purpose:** A structured Windows command-line, PowerShell, and Windows security course for eCPPT preparation, progressing from fundamentals to authorised post-initial-access enumeration, post-enumeration analysis, privilege-boundary assessment, and defensive validation.

**Teaching style:** Beginner-first explanations, line-by-line command walkthroughs, practical interpretation, troubleshooting, safe lab exercises, and exam-style checkpoints.

---

# Mentor-led learning method

This course is written as if a Windows security mentor is guiding a beginner through each topic. Do not rush to the command examples. First understand the problem the command solves; then learn the syntax; finally practise, interpret, and explain the result.

## The learning sequence

1. **Concept first:** Learn the meaning of the Windows feature or security concept in plain language. New terms are defined before they are used.
2. **Why it exists:** Understand the legitimate administrative purpose of the feature. Security testing makes more sense when you know how the system is intended to work.
3. **How Windows handles it:** Learn the relevant process, service, permission, registry, authentication, or event-log behaviour.
4. **Command anatomy:** Break down the cmdlet or CMD command, its parameters, pipeline input, output type, and required permissions.
5. **Mentor demonstration:** Follow a task from question to evidence. Each step explains why the command is being run and what to inspect.
6. **Interpret the output:** Identify normal results, unusual results, missing data, errors, and what the output cannot prove.
7. **Security application:** Apply the knowledge in an authorised lab, using safe test data and clearly defined scope.
8. **Detection and remediation:** Learn what telemetry may be generated, how a defender investigates it, and how to reduce the risk.
9. **Independent practice:** Complete an exercise without copying the walkthrough, then compare your result with the solution and explain any differences.

## Module-by-module mentor roadmap

The 12 collapsible modules below follow the complete Basic → Intermediate → Advanced → Practical → Revision sequence. Each module includes a completion checklist.

| Stage | Modules | Expected result |
| --- | --- | --- |
| Basic | 01–03 | Understand CMD, PowerShell, the object pipeline, and core commands. |
| Intermediate | 04–06 | Write scripts, understand Windows security, and validate an authorised initial-access session. |
| Advanced | 07–10 | Perform post-initial-access enumeration, post-enumeration analysis, privilege-boundary assessment, and defensive correlation. |
| Practical | 11 | Demonstrate the workflow in an isolated lab and produce evidence. |
| Revision | 12 | Explain concepts and commands in scenario-based questions. |

**Mentor rule:** A module is not complete just because you have read it. You should be able to explain the concept without notes, run the relevant command in your lab, interpret the result, and state at least one limitation or defensive consideration.

## Standard lesson format

Major concepts and commands should use this structure:

| Part | What the lesson must teach |
| --- | --- |
| 1. Beginner explanation | Definition, purpose, analogy where useful, and prerequisite terms |
| 2. How it works | Relevant Windows internals and the sequence of events |
| 3. Syntax and parameters | Correct syntax, parameter meaning, defaults, and version requirements |
| 4. Guided demonstration | A realistic, authorised task explained line by line |
| 5. Expected output | Representative output and an explanation of important fields |
| 6. Interpretation and limitations | What the result means, what it does not mean, common errors, and troubleshooting |
| 7. Security relevance | How an assessor validates the configuration and how a defender uses the information |
| 8. Detection and remediation | Relevant logs, telemetry, hardening, and safe cleanup |
| 9. Practice and review | Beginner exercise, challenge exercise, hints, solution, and knowledge-check questions |

Not every small reference item needs nine long sections. Use the complete format for important concepts and commands; keep quick-reference tables concise and link them conceptually to the detailed explanation.

## How to practise each command

Use this repeatable routine:

1. **Ask a question:** For example, “Which processes are currently running?”
2. **Predict:** Before executing a command, write down what information you expect to receive.
3. **Run:** Execute the command in a Windows lab VM. Start with read-only commands.
4. **Inspect:** Use Get-Member, Format-List, or Select-Object to understand the returned data.
5. **Verify:** Compare the result with another trusted Windows interface or command when possible.
6. **Record:** Save the command, relevant output, timestamp, host, user context, and any limitations.
7. **Explain:** Describe the result in your own words without simply repeating the command.
8. **Clean up:** Remove only the benign test artefacts you created and confirm the lab has returned to its intended state.

**PowerShell for Cyber Security**

**Structured PowerShell and Windows Security Course**

**Focus:** Windows administration, authorised penetration testing, security monitoring, and incident response.

| **Topic** | **Coverage** |
| --- | --- |
| Windows and CMD foundations | Shell basics, navigation, files, users, processes, services, networking, and batch scripting |
| PowerShell foundations | Cmdlets, help, command discovery, objects, providers, and the pipeline |
| Core administration commands | Process, service, file, network, CIM, and event-log investigation |
| PowerShell scripting | Variables, data types, conditions, loops, functions, parameters, and error handling |
| Windows security architecture | Principals, SIDs, access tokens, UAC, integrity levels, ACLs, and authentication |
| Authorised red-team assessment | Read-only enumeration, scoped misconfiguration validation, and evidence collection |
| Defensive operations | PowerShell logging, Windows event analysis, threat hunting, incident response, and hardening |
| Practical labs | Guided beginner tasks, challenge exercises, expected results, troubleshooting, and cleanup |
| Assessment and revision | Repeatable workflow, reporting template, knowledge checks, and command reference |

MODULE 01 — Windows CMD Foundations

CMD is still important in Windows assessments. You will encounter it in interactive shells, batch files, legacy administration scripts, service command lines, and incident-response evidence. Learn the syntax and limitations rather than treating CMD as an outdated PowerShell.

## 1.1 CMD fundamentals

- **CMD / cmd.exe:** The Windows command interpreter. It primarily processes text and launches native executables or built-in commands.
- **PowerShell:** A shell and scripting language whose pipeline passes .NET objects between commands.
- **Batch file:** A text file ending in `.bat` or `.cmd` containing commands interpreted by CMD.
- **Environment variable:** A named value such as `%USERNAME%`, `%COMPUTERNAME%`, or `%PATH%`.

Open CMD normally for read-only learning. Use an elevated terminal only when a lab task explicitly requires administrative privileges. A command returning “Access is denied” is useful evidence of the current permission boundary; do not automatically elevate.

### Essential navigation and file commands

| Command | Purpose | Example |
| --- | --- | --- |
| `cd` / `chdir` | Change directory | `cd C:\\Windows\\System32` |
| `dir` | List files and folders | `dir /a` |
| `tree` | Display folder structure | `tree C:\\Lab /f` |
| `type` | Display a text file | `type C:\\Lab\\readme.txt` |
| `more` | Page through text output | \`type large.txt |
| `where` | Locate an executable | `where whoami` |
| `where /r` | Search a directory tree for a filename | `where /r C:\\Lab *.txt` |
| `findstr` | Search text using strings or patterns | `findstr /i "error failed" app.log` |
| `copy`, `move`, `ren` | Copy, move, rename files | Practise only inside a lab folder |
| `mkdir`, `rmdir` | Create/remove directories | `mkdir C:\\Lab\\Practice` |

**Important:** `del`, `rmdir /s`, and overwrite operations can destroy data. Do not practise destructive file operations against system directories or real user data.

## 1.2 Identify the current Windows context

```shell
whoami
whoami /user
whoami /groups
whoami /priv
hostname
echo %USERNAME%
echo %USERDOMAIN%
echo %COMPUTERNAME%
set
ver
systeminfo
```

**How to interpret the results**

- `whoami` identifies the current security principal.
- `/user` displays the account SID; a SID is a stable identifier, not a password.
- `/groups` lists group memberships and group attributes. Membership in an administrative group does not always mean the current process has an elevated token.
- `/priv` lists privileges present in the current token. A privilege being listed does not necessarily mean it is enabled or usable in the current context.
- `hostname` and `systeminfo` help establish host identity and OS/build details.
- `set` prints environment variables. Treat output as potentially sensitive because applications may place tokens or configuration values in the environment.

## 1.3 Process and service inspection

```shell
tasklist
tasklist /svc
tasklist /fi "STATUS eq RUNNING"
sc query
sc query type= service state= all
sc qc Spooler
```

- `tasklist` lists processes and PIDs.
- `tasklist /svc` maps hosted services to processes where supported.
- `sc query` displays service state.
- `sc qc` displays a service's configuration, including its binary path and start account.

**Assessment method:** Record the service name, binary path, start account, start mode, and service state. Then inspect the relevant file and directory ACLs. A service running as SYSTEM is not automatically vulnerable; the security question is whether an untrusted principal can modify the executable, its directory, its configuration, or a dependency in a way that changes privileged execution.

Do not change service configuration on a production host. Use a deliberately vulnerable lab VM for modification tests.

## 1.4 Network discovery and troubleshooting

```shell
ipconfig /all
ipconfig /displaydns
arp -a
route print
netstat -ano
netstat -ano | findstr LISTENING
ping 127.0.0.1
ping 192.0.2.10
tracert 192.0.2.10
nslookup lab.example.test
```

Replace the documentation-only example address and hostname with a target explicitly listed in your lab scope. The 192.0.2.0/24 range is reserved for documentation and is not a real target.

**Interpretation**

- `ipconfig /all`: interfaces, IP addresses, gateways, DNS servers, DHCP status, and MAC addresses.
- `arp -a`: recently resolved IPv4-to-MAC mappings on the local host; it is not a complete network inventory.
- `route print`: routing table and interface metrics.
- `netstat -ano`: connections/listeners, local and remote endpoints, and owning PID. Map a PID with `tasklist /fi "PID eq 1234"`.
- `ping`: tests ICMP reachability; a failed ping does not prove a host is offline because ICMP may be filtered.
- `nslookup`: checks DNS resolution; compare the answer with the expected lab DNS configuration.

Only scan networks that are explicitly in scope. These commands are primarily local diagnostics, not a substitute for an authorised network scan.

## 1.5 Accounts and local groups

```shell
net user
net user %USERNAME%
net localgroup
net localgroup Administrators
net accounts
```

Use these commands to understand local account and group configuration. The output can vary with domain policy and permissions. Do not attempt password guessing or account changes on systems you do not own or administer.

## 1.6 File, registry, and permission inspection

```shell
icacls C:\\Lab
icacls C:\\Lab\\sample.exe
reg query HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion
reg query HKCU\\Environment
```

- `icacls` displays or modifies NTFS discretionary access control lists (DACLs). In an assessment, begin with read-only inspection.
- `reg query` reads registry keys and values. Registry data may reveal software configuration, startup settings, and system policy.
- Registry permissions matter as much as the value itself. A writable key used by a privileged component may create risk, but validate the complete execution and permission chain before reporting it.

Avoid `icacls /grant`, `reg add`, and `reg delete` outside a disposable lab. These commands change security settings or system configuration.

## 1.7 Event logs and audit policy

```shell
wevtutil el
wevtutil qe Security /c:10 /rd:true /f:text
wevtutil qe System /c:10 /rd:true /f:text
auditpol /get /category:*
```

- `wevtutil el` lists event channels.
- `wevtutil qe` queries events. Access to the Security log normally requires appropriate rights.
- `auditpol` displays audit policy configuration.
- Use PowerShell `Get-WinEvent` when you need richer filtering, structured properties, or to inspect an exported EVTX file.

Never clear event logs as part of a learning exercise. Log deletion destroys evidence and is itself a high-priority security event.

## 1.8 CMD variables, redirection, and operators

| Syntax | Meaning | Example |
| --- | --- | --- |
| %NAME% | Expand an environment variable | echo %COMPUTERNAME% |
| & | Run the next command regardless of the previous result | whoami & hostname |
| && | Run the next command only if the previous command succeeds | whoami && hostname |
|  |  |  |
| Pipe (vertical bar) | Pass standard output as text to another command | ipconfig /all, then findstr IPv4 |
| > | Redirect output and overwrite a file | whoami > identity.txt |
| >> | Append output to a file | hostname >> identity.txt |
| 2> | Redirect standard error | command 2> errors.txt |
| 2>&1 | Redirect standard error to standard output | command > output.txt 2>&1 |

**Quoting and paths:** Use double quotes around paths containing spaces, for example `dir "C:\\Program Files"`. CMD escaping rules differ from PowerShell. A command that works in one shell may not work in the other.

## 1.9 Batch scripting basics

A batch file is useful for simple repeatable administration tasks. It is not a replacement for PowerShell when you need structured objects, robust error handling, or complex data processing.

```shell
@echo off
echo Host: %COMPUTERNAME%
echo User: %USERNAME%
whoami
ipconfig
if errorlevel 1 (
    echo A command reported an error.
) else (
    echo Command completed.
)
pause
```

- `@echo off` suppresses command echoing.
- `if errorlevel 1` checks whether the preceding command returned exit code 1 or higher.
- `%ERRORLEVEL%` exposes the most recent exit code; capture it immediately if later commands may change it.
- `pause` waits for a key press; remove it for unattended scripts.

## 1.10 Launching PowerShell from CMD

```shell
powershell.exe -NoProfile -Command "Get-Process | Select-Object -First 5"
pwsh.exe -NoProfile -Command "Get-Service | Where-Object Status -eq 'Running'"
```

- `powershell.exe` normally starts Windows PowerShell 5.1.
- `pwsh.exe` starts PowerShell 7 when installed.
- `-NoProfile` starts without loading the user's profile, improving repeatability in a lab.
- Quoting is important because CMD parses the outer command line before PowerShell receives it.

Do not assume that a PowerShell command line is invisible or unlogged. Process creation telemetry, PowerShell logging, and endpoint monitoring may record command execution.

## 8.11 CMD vs PowerShell — practical translation

| Task | CMD | PowerShell |
| --- | --- | --- |
| List files | `dir` | `Get-ChildItem` |
| Show current identity | `whoami` | `whoami.exe` or token-related .NET/Windows tools |
| List processes | `tasklist` | `Get-Process` |
| List services | `sc query` | `Get-Service` |
| Read text file | `type file.txt` | `Get-Content file.txt` |
| Search text | `findstr` | `Select-String` |
| Network configuration | `ipconfig /all` | `Get-NetIPConfiguration` |
| Query event logs | `wevtutil qe` | `Get-WinEvent` |
| File permissions | `icacls` | `Get-Acl` |
| Registry query | `reg query` | `Get-ItemProperty` |

**Exam point:** CMD pipelines pass text. PowerShell pipelines pass objects. In PowerShell, use `Select-Object`, `Where-Object`, and `Sort-Object` to manipulate object properties rather than parsing formatted screen output.

---

## Completion checklist

- [ ] I can navigate CMD and explain its text-based pipeline.
- [ ] I can identify host, user, OS, process, service, and network context.
- [ ] I can use read-only commands without changing system state.

MODULE 02 — PowerShell Foundations

**2.1 What is PowerShell?**

PowerShell is Microsoft's task automation framework built on the .NET runtime. It combines a command-line shell with a scripting language. Unlike CMD which works with plain text, PowerShell works with OBJECTS — structured data you can filter, sort, and pipe between commands.

Think of it like this: CMD gives you a text printout of a process list. PowerShell gives you a live, manipulable object where you can filter by CPU, memory, name — anything.

**PowerShell in offensive security**

- Built-in to every Windows machine — no installation needed
- Supports automation and interaction with Windows APIs and .NET.
- Can be used for remote administration through WinRM when configured and authorised.
- Is monitored by modern security controls, including AMSI, PowerShell logging, and endpoint detection tools.
- Can be abused by attackers, so defenders should understand its normal and suspicious use.

**PowerShell in defensive security**

- Powerful log analysis and event querying
- Automation of repetitive security tasks
- Active Directory management and auditing
- Network scanning and traffic analysis
- Incident response automation
- Enforce security policies at scale

**2.2 PowerShell vs CMD — Key Differences**

| **Feature** | **CMD** | **PowerShell** |
| --- | --- | --- |
| Output Type | Plain text | Objects (.NET) |
| Piping | Text streams | Object pipelines |
| Scripting | Basic .bat files | Full scripting language |
| Remote Execution | Limited (psexec) | Built-in (WinRM, Invoke-Command) |
| Error Handling | None / basic | Try/Catch/Finally |
| Web Interaction | curl (external) | Invoke-WebRequest (built-in) |
| Common use | Basic administration and batch automation | Administration, automation, security assessment, and investigation |
| Version | Static | v1-v7+ (check: \$PSVersionTable) |

**2.3 Understanding Cmdlets**

A cmdlet (pronounced 'command-let') is the basic unit of PowerShell. Every cmdlet follows the Verb-Noun format.

**STRUCTURE: Get-Process** → Verb-Noun

**Common PowerShell Verbs**

| **Verb** | **Meaning** | **Example** |
| --- | --- | --- |
| Get | Retrieve information | Get-Process, Get-Service |
| Set | Change/configure something | Set-ExecutionPolicy, Set-Item |
| New | Create something new | New-Item, New-Object |
| Remove | Delete something | Remove-Item, Remove-Service |
| Start | Start a process/service | Start-Process, Start-Service |
| Stop | Stop a process/service | Stop-Process, Stop-Service |
| Invoke | Execute/run something | Invoke-Command, Invoke-WebRequest |
| Test | Check/verify something | Test-Connection, Test-Path |
| Export | Send data to file | Export-Csv, Export-Clixml |
| Import | Load data from file | Import-Csv, Import-Module |

**1.4 PowerShell help and command discovery**

Before memorizing commands, learn how to FIND and USE help. This is the foundation.

**<u>Get-Help — Read the manual</u>**

> 
\# Basic help for a command

Get-Help Get-Process

\# Full detailed help with examples

Get-Help Get-Process -Full

\# Show only examples

Get-Help Get-Process -Examples

\# Update help files (run as admin)

Update-Help

\# Online help in browser

Get-Help Get-Process -Online

**<u>Get-Command — Find commands you don't know</u>**

> 
\# List ALL available commands

Get-Command

\# Find commands matching a pattern

Get-Command \*network\*

Get-Command \*log\*

\# Find all cmdlets only

Get-Command -CommandType Cmdlet

\# Find commands in a module

Get-Command -Module ActiveDirectory

**<u>Get-Member — Explore object properties</u>**

> 
\# See all properties and methods of a process object

Get-Process \| Get-Member

\# Output shows: TypeName, Methods, Properties

\# TypeName: System.Diagnostics.Process

\# Methods: Kill(), Start(), WaitForExit()

\# Properties: Name, Id, CPU, WorkingSet, Handles

\# Access a specific property

Get-Process \| Select-Object Name, CPU, Id

**1.5 Beginner walkthrough — understand the PowerShell pipeline**

Before using advanced commands, understand one idea that makes PowerShell different from CMD: **PowerShell passes objects through the pipeline.**

### What is an object?

An object is a structured item of data. Think of a process as a record with named fields: its name, process ID, CPU time, memory usage, and other properties. PowerShell can pass that record to another command without first converting it into a line of text.

### Step 1 — Produce objects

```powershell
Get-Process
```

PowerShell returns process objects. The console displays a formatted view of those objects, but that display is not the complete object.

### Step 2 — Discover the properties

```powershell
Get-Process | Get-Member
```

Read the output as a description of the object type and the members it exposes. Properties describe data; methods represent actions supported by the object. You do not need to memorise every property. Learn how to discover the ones relevant to the task.

### Step 3 — Select only the fields you need

```powershell
Get-Process | Select-Object Name, Id, CPU
```

This keeps the process name, process ID, and accumulated CPU time. It does not change the running processes; it only selects information for display.

### Step 4 — Filter objects

```powershell
Get-Process | Where-Object { $_.CPU -gt 10 }
```

Read this from left to right:

- Get-Process creates the process objects.
- The pipe character passes those objects to the next command.
- Where-Object tests each object against the condition.
- \$\_ means the current object being examined.
- .CPU accesses that object's CPU property.
- -gt means greater than.

The number 10 is an example threshold, not a universal definition of suspicious CPU usage.

### Step 5 — Sort and limit the result

```powershell
Get-Process |
    Sort-Object CPU -Descending |
    Select-Object -First 5 Name, Id, CPU
```

The first command gathers the data, the second sorts it from highest to lowest CPU time, and the third displays the first five records.

### Step 6 — Save structured results

```powershell
Get-Process |
    Select-Object Name, Id, CPU |
    Export-Csv -Path '.\process-inventory.csv' -NoTypeInformation
```

CSV export preserves rows and columns for later review. Treat exported inventory as potentially sensitive because process names and host information can reveal operational details.

### Common beginner mistakes

- **Confusing display with data:** Format-Table is for presentation. Avoid formatting objects before you need to filter, sort, or export them.
- **Assuming every property exists:** Inspect objects with Get-Member and check the documentation for the cmdlet.
- **Using the wrong comparison operator:** -eq means equal, -ne not equal, -gt greater than, -ge greater than or equal, -lt less than, and -le less than or equal.
- **Treating CPU as a percentage:** The CPU property from Get-Process is accumulated processor time in seconds, not a live utilisation percentage.
- **Ignoring errors:** An empty result can mean no objects matched; an error can mean the command failed. Read the error before drawing a conclusion.

### Practice — do it yourself

1. List all running processes.
2. Display only Name, Id, and CPU.
3. Find processes whose accumulated CPU time is greater than 5 seconds.
4. Sort the results by CPU time, highest first.
5. Export the selected results to a CSV file in your lab folder.

**Challenge:** Explain the difference between Where-Object and Select-Object. Then explain why Format-Table should normally be used at the end of a pipeline.

**CMD comparison**

```shell
tasklist
tasklist /fo csv
```

CMD can display or export a process list as text/CSV, but PowerShell's object pipeline makes property-based filtering and sorting more direct.

---

## Example: learn Get-Process like an analyst

**The question:** “What is running on this Windows computer, and which processes are using the most CPU?”

**Step 1 — Establish context**

```powershell
$PSVersionTable
whoami
hostname
```

These commands establish the PowerShell version, current user, and computer name. Record this context because results and available cmdlets can differ between hosts and sessions.

**Step 2 — Collect the process list**

```powershell
Get-Process
```

This returns process objects, not just lines of text. Each object represents a process visible to the current user and includes properties such as Name, Id, CPU, and WorkingSet.

**Step 3 — Inspect the object structure**

```powershell
Get-Process | Get-Member
Get-Process | Select-Object -First 10 Name, Id, CPU, WorkingSet
```

Get-Member shows the available properties and methods. Select-Object chooses the fields needed for the investigation. WorkingSet is memory currently resident in physical RAM; it is not the same as total virtual memory.

**Step 4 — Rank CPU time**

```powershell
Get-Process |
    Sort-Object CPU -Descending |
    Select-Object -First 10 Name, Id, CPU
```

This sorts by accumulated processor time reported by the process object. It is not an instantaneous CPU percentage. A process with a high accumulated value may simply have been running for a long time.

**Step 5 — Interpret carefully**

- A process name alone does not establish whether a program is legitimate or malicious.
- A familiar name can be imitated. Validate the executable path, signer, parent process, user context, start time, and related network or file activity where appropriate.
- Access to some process details may be restricted. An access error is a permission boundary, not proof that the process is malicious.
- Process listings are snapshots. A short-lived process may exit before collection.

**Practice task:** Find the five processes with the highest accumulated CPU time. Display their names, process IDs, CPU time, and memory working set. Explain why the CPU field is not a live percentage.

**CMD comparison**

```shell
tasklist
tasklist /v
```

CMD's tasklist produces formatted text. PowerShell returns structured objects that can be filtered and sorted through the object pipeline. Do not assume that the two commands expose identical fields.

## Completion checklist

- [ ] I can explain cmdlets, help, objects, and the PowerShell pipeline.
- [ ] I can inspect an unfamiliar command with Get-Help, Get-Command, and Get-Member.
- [ ] I can compare PowerShell 5.1 and PowerShell 7.

MODULE 03 — Core PowerShell Commands

Use these command lessons to understand purpose, syntax, output, and security relevance. Check help and version support before running a command. Start with read-only use; treat commands that change services, processes, execution settings, files, or remote systems as administrative actions requiring explicit approval.

**COMMAND: Get-Process**

> 
**1. WHAT IT DOES:**

Lists all running processes on the computer. Returns objects with full process information including memory usage, CPU time, process ID (PID), and more.

**2. SYNTAX BREAKDOWN:**

Get-Process \[-Name \<String\[\]>\] \[-Id \<Int32\[\]>\] \[-ComputerName \<String\[\]>\]

**Get-Process** → The cmdlet itself — fetches running process information

- **Name \<String\[\]>** → Filter by process name (supports wildcards like note\*)
- **Id \<Int32\[\]>** → Filter by specific Process ID (PID)
- **ComputerName** → Available in Windows PowerShell 5.1; removed from PowerShell 7. For modern remote collection, use authorised PowerShell remoting or CIM with the required transport and permissions.

**3. SECURITY ASSESSMENT AND DEFENSIVE USE CASE:**

During an authorised assessment, use process enumeration to understand the host's running workload and identify processes that require further validation. Do not treat the presence or absence of a security product as permission to evade it. Defenders use process inventory to investigate unexpected executables, unusual execution paths, suspicious parent-child relationships, and resource anomalies.

**4. EXAMPLE (Input → Output):**

\# List all processes

Get-Process

\# OUTPUT (sample):

\# Handles NPM(K) PM(K) WS(K) CPU(s) Id SI ProcessName

\# ------- ------ ----- ----- ------ -- -- -----------

\# 340 22 11234 15678 0.45 1234 1 chrome

\# 120 10 5678 8901 0.12 5678 1 notepad

\# 1024 45 88234 102345 12.34 4321 1 MsMpEng \<-- Defender!

\# Get specific process

Get-Process -Name 'chrome'

\# Review process inventory for unusual resource usage

Get-Process \| Sort-Object CPU -Descending \| Select-Object -First 10 Name, Id, CPU

\# High CPU consumers (defender use: find malware)

Get-Process \| Sort-Object CPU -Descending \| Select-Object -First 10

**5. WHY & WHEN TO USE IT:**

Use during authorised host enumeration and routine administration to establish process context. Defenders use it to identify unexpected processes, investigate masquerading, and triage CPU or memory anomalies. Correlate process name with executable path, signer, parent process, user, start time, and endpoint telemetry.

**6. TELEMETRY AND DETECTION:**

A process query is a normal administrative action. Its visibility depends on the collection configuration. PowerShell Script Block Logging may record script content when enabled; process-creation auditing or Sysmon may record the PowerShell process and command line. Correlate the event with the user, parent process, host, and surrounding activity rather than treating one command as proof of malicious intent.

**7. HOW DEFENDERS DETECT/PREVENT IT (Blue Team):**

Enable Script Block Logging (Event ID 4104). Alert on PowerShell commands searching for AV/EDR process names. Monitor for unusual parent processes spawning PowerShell. Use EDR to correlate PowerShell process enumeration with subsequent suspicious activity.

**COMMAND: Get-Service**

> 
**1. WHAT IT DOES:**

Lists all Windows services — background programs that run without user login. Shows service name, display name, and status (Running/Stopped).

**2. SYNTAX BREAKDOWN:**

Get-Service \[-Name \<String\[\]>\] \[-DisplayName \<String\[\]>\] \[-ComputerName \<String\[\]>\]

**Get-Service** → Fetches Windows service information

- **Name** → Short service name (e.g., 'wuauserv' for Windows Update)
- **DisplayName** → Full display name (e.g., 'Windows Update')
- **ComputerName** → Available in Windows PowerShell 5.1; removed from PowerShell 7. For modern remote collection, use authorised PowerShell remoting or CIM with the required transport and permissions.

**3. REAL-WORLD CYBERSECURITY USE CASE:**

Assessment: Inventory service accounts, executable paths, and effective permissions. A privileged service is a concern only when an untrusted principal can influence its configuration or the files it executes. Defender: Baseline services and investigate unexpected changes or unusual paths.

**4. EXAMPLE (Input → Output):**

\# List all services

Get-Service

\# List stopped services for operational review

Get-Service \| Where-Object \{\$\_.Status -eq 'Stopped'\}

\# Check specific security services

Get-Service -Name 'WinDefend' # Windows Defender

Get-Service -Name 'wscsvc' # Windows Security Center

\# OUTPUT:

\# Status Name DisplayName

\# ------ ---- -----------

\# Running WinDefend Windows Defender Antivirus Service

\# Stopped wscsvc Security Center

\# Review services configured to run as LocalSystem

Get-CimInstance Win32\_Service \| Where-Object \{\$\_.StartName -eq 'LocalSystem'\} \| Select-Object Name, StartName, State, PathName

**5. WHY & WHEN TO USE IT:**

Use during reconnaissance to map running services. Critical for privilege escalation (finding weak service permissions). Defenders use it to audit services for persistence and detect unauthorized service installations.

**6. RISKS / DETECTION (Red Team):**

Querying services is low-risk detection-wise. However, modifying services or creating new ones triggers Event ID 4697 (service installed) and Event ID 7045 (new service). These are high-value alerts.

**7. HOW DEFENDERS DETECT/PREVENT IT (Blue Team):**

Monitor Event ID 4697 and 7045 for new service installations. Audit service binary paths for writable locations. Use SC.exe or Get-Service regularly to baseline legitimate services. Alert on services with unusual paths (e.g., temp folders, user profile directories).

**COMMAND: Get-EventLog (legacy; prefer Get-WinEvent)**

> 
**1. WHAT IT DOES:**

Reads entries from supported classic Windows event logs, including System, Security, and Application. It is a legacy cmdlet; use Get-WinEvent for modern channels, structured filtering, and EVTX files.

**2. SYNTAX BREAKDOWN:**

Get-EventLog -LogName \`<String>` \[-Newest \`<Int32>`\] \[-Source \`<String>`\] \[-EventId \<Int32\[\]>\]

- **LogName** → Which log to read: Security, System, Application, etc.
- **Newest \`<Int32>`** → Limit results to the N most recent entries
- **Source \`<String>`** → Filter by event source (e.g., 'Microsoft-Windows-Security-Auditing')
- **EventId \<Int32\[\]>** → Filter by specific Event ID numbers

**3. REAL-WORLD CYBERSECURITY USE CASE:**

Defender: Check Security log Event ID 4625 for failed login attempts (brute force detection). Event ID 4648 for explicit credential use. Event ID 4720 for new account creation. Security note: unauthorised log clearing destroys evidence and is itself a high-priority detection event. Use event logs for investigation and preserve evidence.

**4. EXAMPLE (Input → Output):**

\# Read Security log, last 50 events

Get-EventLog -LogName Security -Newest 50

\# Find failed login attempts (brute force detection)

Get-EventLog -LogName Security -EventId 4625 -Newest 100

\# Find successful logins

Get-EventLog -LogName Security -EventId 4624 -Newest 50

\# Detect PowerShell usage

Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -MaxEvents 50

\# OUTPUT (sample for Event 4625 - Failed Logon):

\# Index Time EntryType Source EventID Message

\# ----- ---- --------- ------ ------- -------

\# 12345 May 01 03:21 FailureAudit Security-Auditing 4625 An account failed to log on.

\# Defensive validation: identify log-clearing events

Get-WinEvent -FilterHashtable @\{LogName='Security'; Id=1102\} -MaxEvents 20

**5. WHY & WHEN TO USE IT:**

Essential for defenders doing threat hunting and incident response. Use Event ID 4625 for brute force, 4720 for account creation, 4732 for group membership changes. Defenders use event records to reconstruct activity and validate whether expected auditing is enabled.

**6. RISKS / DETECTION (Red Team):**

Reading logs is not suspicious. Attempting to clear the Security log generates Event ID 1102 and destroys forensic evidence. Never clear logs during an assessment; preserve the original records and investigate the cause.

**7. HOW DEFENDERS DETECT/PREVENT IT (Blue Team):**

Forward logs to SIEM immediately (on-box logs can be cleared). Alert on Event ID 1102 (log cleared) and 4719 (audit policy changed). Enable Advanced Audit Policy for maximum visibility. Use Get-WinEvent (newer version) for better filtering capabilities.

**COMMAND: Get-Content — Read text files**

> 
**1. WHAT IT DOES:**

Reads text from files. It is useful for reviewing scripts, configuration samples, and plain-text logs in an approved lab. It does not parse Windows EVTX event-log files.

**2. SYNTAX:**

```powershell
Get-Content -Path '.\sample.txt'
Get-Content -Path '.\sample.txt' -TotalCount 10
Get-Content -Path '.\sample.txt' -Tail 20
Get-Content -Path '.\sample.txt' -Raw
```

- Path identifies the file.
- TotalCount reads the first N lines.
- Tail reads the last N lines.
- Raw returns the content as one string.

**3. SECURITY USE:**

Use it to review an approved sample configuration or script for insecure settings. Do not search user profiles or unrelated system paths for passwords, tokens, browser data, or private keys.

**4. RELATED EXAMPLES:**

```powershell
Get-Content -Path 'C:\Lab\sample-config.txt' -TotalCount 30
Select-String -Path 'C:\Lab\*.log' -Pattern 'error|warning'
Get-WinEvent -Path 'C:\Lab\sample.evtx' -MaxEvents 20
```

Use Get-WinEvent to read EVTX files. Use Get-Content only for text files.

**5. LIMITATIONS:**

Large files may consume substantial memory, especially with Raw. File access depends on NTFS permissions. An access error is not evidence that a file is malicious.

**6. DETECTION AND DEFENCE:**

Sensitive file access may be visible through endpoint telemetry, file auditing, or DLP depending on configuration. Restrict sensitive files with least-privilege ACLs and avoid storing secrets in plaintext.

**COMMAND: Get-ExecutionPolicy / Set-ExecutionPolicy**

> 
**1. WHAT IT DOES:**

Get-ExecutionPolicy reports the effective script execution policy. Set-ExecutionPolicy changes policy at a supported scope. Execution Policy is intended to reduce accidental script execution; it is not a security boundary.

**2. INSPECT POLICY:**

```powershell
Get-ExecutionPolicy
Get-ExecutionPolicy -List
```

**3. SECURITY INTERPRETATION:**

- Restricted, AllSigned, RemoteSigned, Unrestricted, and Bypass have different script-handling behaviour.
- Group Policy scopes can override local settings.
- A displayed policy does not prove that untrusted code cannot execute.
- Do not use policy changes or process-scoped overrides to evade controls.

**4. DEFENSIVE GUIDANCE:**

Use application control such as WDAC or AppLocker, endpoint protection, least privilege, and PowerShell logging as layered controls. Validate the effective policy and the organisation's intended configuration.

**5. PRACTICE:**

Run Get-ExecutionPolicy and Get-ExecutionPolicy -List in a lab. Record the effective policy and all scopes. Explain which policy source takes precedence and why this setting is not a security boundary.

**COMMAND: Test-Connection**

> 
**1. WHAT IT DOES:**

Tests network reachability using ICMP echo requests. It is a diagnostic command, not a complete port or vulnerability scanner.

**2. SYNTAX AND EXAMPLES:**

```powershell
Test-Connection -ComputerName $env:COMPUTERNAME -Count 2
Test-Connection -ComputerName $env:COMPUTERNAME -Count 2 -Quiet
```

- ComputerName identifies the approved lab host.
- Count controls the number of requests.
- Quiet returns a Boolean-style result.

**3. INTERPRETATION:**

A successful response indicates that an ICMP reply was received. A failed response does not prove the host is offline; ICMP may be blocked or filtered.

**4. SECURITY USE:**

Use it to validate connectivity to an explicitly in-scope host. Do not treat ping success as proof that a service is reachable or vulnerable.

**5. DEFENSIVE CONSIDERATIONS:**

Correlate with routing, DNS, firewall policy, and service-specific checks. Keep tests within the authorised address range.

**COMMAND: Get-CimInstance (modern WMI/CIM)**

> 
**1. WHAT IT DOES:**

Queries Windows Management Instrumentation (WMI) classes through the modern CIM cmdlets. It returns structured objects describing operating-system, hardware, process, service, and other system information.

**2. VERSION NOTE:**

Prefer Get-CimInstance for current scripts. Get-WmiObject is a legacy cmdlet available in Windows PowerShell 5.1 but is not included in PowerShell 7.

**3. EXAMPLES:**

```powershell
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture

Get-CimInstance Win32_ComputerSystem |
    Select-Object Name, Domain, PartOfDomain, Model

Get-CimInstance Win32_Service |
    Select-Object Name, State, StartName, PathName
```

**4. INTERPRETATION:**

The class determines which information is returned. Some properties may be empty or unavailable due to permissions, provider support, or platform version.

**5. SECURITY USE:**

Use CIM for read-only host inventory and service configuration review. Remote CIM queries require the appropriate transport, firewall configuration, and permissions; do not assume that local access implies remote access.

**6. PRACTICE:**

Collect OS and service information from your own Windows VM. Explain what each selected property means and identify any fields that were unavailable.

**COMMAND: Invoke-Command — authorised PowerShell remoting**

> 
**1. WHAT IT DOES:**

Runs a PowerShell command on a remote computer through a configured remoting transport, commonly WinRM. It is a legitimate administration capability and must only be used on systems and accounts explicitly approved for remote access.

**2. PREREQUISITES:**

- The destination must be in scope.
- Remoting must be configured and reachable.
- Authentication must be supported and approved.
- The account must have the required remote permissions.
- Firewall and logging requirements must be respected.

**3. SAFE LAB EXAMPLE:**

```powershell
Test-WSMan -ComputerName 'localhost'

Invoke-Command -ComputerName 'localhost' -ScriptBlock {
    hostname
    whoami
    Get-Service -Name 'EventLog'
}
```

**4. INTERPRETATION:**

A successful Test-WSMan response indicates that a WS-Management endpoint responded. It does not prove that the account is authorised to run every command. An access-denied error may reflect remote permissions or policy.

**5. SECURITY AND DETECTION:**

Remote PowerShell activity may generate logon, WinRM, process-creation, and PowerShell Operational events on the source and destination. Correlate account, source host, destination, process, time, and script content.

**6. RULE:**

Do not use stolen credentials, password hashes, downloaded scripts, backdoor accounts, or remote payload execution. Use a designated lab account and a named lab host only.

**COMMAND: Start-Process — launch a local application**

> 
**1. WHAT IT DOES:**

Starts a local process. Common parameters select the executable, arguments, working directory, and whether PowerShell waits for completion.

**2. SAFE EXAMPLES:**

```powershell
Start-Process -FilePath 'notepad.exe'
Start-Process -FilePath 'notepad.exe' -PassThru
Start-Process -FilePath 'notepad.exe' -Wait
```

**3. PARAMETER NOTES:**

- FilePath identifies the executable.
- ArgumentList supplies arguments expected by that executable.
- PassThru returns a process object.
- Wait waits for the process to exit.
- Verb RunAs requests elevation and may trigger UAC; use only when the task explicitly requires approved administration.

**4. SECURITY INTERPRETATION:**

Process creation may be recorded by Windows process auditing or Sysmon when configured. Review parent process, executable path, signer, command line, user, and time.

**5. SAFE PRACTICE:**

Launch and close a benign application in your own lab. Do not use hidden windows, encoded payloads, untrusted downloads, or alternate credentials as an evasion or escalation technique.

**COMMAND: Stop-Process — stop a process you own**

> 
**1. WHAT IT DOES:**

Requests termination of a process. This can cause data loss or interrupt a service, so it is a state-changing administrative action.

**2. SAFE LAB EXAMPLE:**

```powershell
$process = Start-Process -FilePath 'notepad.exe' -PassThru
$process | Select-Object Id, ProcessName
Stop-Process -Id $process.Id
```

**3. RULES:**

- Stop only a process created for the current lab or one explicitly approved for interruption.
- Do not terminate security tools, logging agents, backup services, or production processes.
- Record the reason and confirm the process has exited.
- Prefer the application's normal close mechanism where possible.

**4. DEFENSIVE MONITORING:**

Unexpected process termination may be visible through endpoint telemetry, service monitoring, and process-creation/termination records. Investigate the initiating user and parent process.

MODULE 04 — PowerShell Scripting for Security Work

This module takes you from individual commands to small, readable scripts. A script should solve one clear problem, validate inputs, report errors honestly, and avoid changing the system unless that change is explicitly part of an approved lab.

## 4.1 Understand the script lifecycle

1. Define the task and expected result.
2. Validate inputs.
3. Gather information using suitable cmdlets.
4. Process returned objects.
5. Present or export the result.
6. Handle errors and record limitations.
7. Verify that no unwanted system changes occurred.

## 4.2 Variables and data types

```powershell
$name = 'LAB-PC'
$count = 5
$isReady = $true
$processes = Get-Process
$ports = @(80, 443, 445)
$settings = @{
    Computer = 'LAB-PC'
    Purpose  = 'Inventory'
}
```

- String: text such as a hostname.
- Integer: a whole number.
- Boolean: `$true` or `$false`.
- Array: an ordered collection.
- Hashtable: key/value pairs.
- Object: structured data with properties and methods.

Inspect a value with `$value.GetType()` and an object with `Get-Member`.

## 4.3 Conditions and comparison operators

```powershell
$service = Get-Service -Name 'Spooler' -ErrorAction SilentlyContinue
if ($null -eq $service) {
    'Service was not found or could not be queried.'
}
elseif ($service.Status -eq 'Running') {
    'The service is running.'
}
else {
    'The service is not running.'
}
```

| Operator | Meaning |
| --- | --- |
| `-eq`, `-ne` | Equal, not equal |
| `-gt`, `-ge` | Greater than, greater than or equal |
| `-lt`, `-le` | Less than, less than or equal |
| `-like` | Wildcard comparison |
| `-match` | Regular-expression comparison |
| `-and`, `-or`, `-not` | Logical operators |

Prefer explicit comparisons and handle null values deliberately. A missing result does not mean a check succeeded.

## 4.4 Loops

```powershell
$names = @('Spooler', 'EventLog', 'WinDefend')
foreach ($name in $names) {
    Get-Service -Name $name -ErrorAction SilentlyContinue |
        Select-Object Name, Status, StartType
}
```

`foreach` iterates over a collection. `ForEach-Object` processes objects arriving through the pipeline. Choose the form that makes the script easiest to read.

## 4.5 Functions and parameters

```powershell
function Get-LabServiceStatus {
    param(
        [Parameter(Mandatory)]
        [ValidateNotNullOrEmpty()]
        [string]$Name
    )
    Get-Service -Name $Name -ErrorAction Stop |
        Select-Object Name, DisplayName, Status, StartType
}
Get-LabServiceStatus -Name 'Spooler'
```

Good functions use clear names, typed parameters, validation, and predictable output. Avoid hidden side effects.

## 4.6 Error handling

PowerShell has terminating and non-terminating errors. `try/catch` catches terminating errors. Use `-ErrorAction Stop` when a cmdlet error must enter the catch block.

```powershell
try {
    Get-Service -Name 'ServiceThatDoesNotExist' -ErrorAction Stop
}
catch {
    Write-Warning "The service query failed: $($_.Exception.Message)"
}
```

Do not suppress every error with `SilentlyContinue`. An error may indicate missing permissions, unavailable modules, or an unsupported command.

## 4.7 Safe reporting script — local inventory

```powershell
$report = [pscustomobject]@{
    ComputerName = $env:COMPUTERNAME
    UserName     = [System.Security.Principal.WindowsIdentity]::GetCurrent().Name
    PSVersion    = $PSVersionTable.PSVersion.ToString()
    CollectedAt  = Get-Date
}
$report | Format-List
$report | Export-Csv -Path '.\host-context.csv' -NoTypeInformation
```

Run this in a lab folder. Check that the CSV exists and contains the expected fields. Do not export secrets, tokens, or unrelated personal data.

## 4.8 Script quality checklist

- Use descriptive variable and function names.
- Validate parameters and handle empty values.
- Prefer objects over parsing formatted console text.
- Use Stop-on-error only where a catch block is intended.
- Avoid hard-coded credentials and secrets.
- Avoid hidden execution, downloaded payloads, or unapproved system changes.
- Include comments explaining why a security check is performed.
- Test normal, missing, and invalid inputs.

## 4.9 Practice

**Beginner:** Write a script that lists three named services and their status.

**Intermediate:** Add a parameter for service names and handle a service that does not exist.

**Challenge:** Export the result to CSV, include collection time and computer name, and explain how you verified the output.

## Completion checklist

- [ ] I can write functions with parameters and validation.
- [ ] I can handle errors without hiding important evidence.
- [ ] I can build and verify a safe read-only inventory script.

MODULE 05 — Windows Security Architecture

This module explains how to assess Windows systems methodically. All practical activity must remain inside an authorised lab or an explicitly scoped engagement. The goal is to understand security boundaries, validate misconfigurations, collect evidence, and explain remediation—not to hide activity or compromise unrelated systems.

## 5.1 Understand the Windows security model first

### Security principals and SIDs

A security principal is an identity to which Windows can assign permissions: a local or domain user, group, computer account, or service identity. Windows represents identities using Security Identifiers (SIDs). Names can change; SIDs are the underlying identifiers used in access checks.

### Access tokens

When a process starts, Windows associates it with an access token containing the user SID, group SIDs, privileges, and other security information. Access checks compare this token with an object's security descriptor.

**Why this matters:** Two command prompts opened by the same user can have different tokens—one standard and one elevated. Always establish the current identity and token context before interpreting access-denied results.

### UAC and integrity levels

User Account Control (UAC) helps limit the privileges available to processes, including split-token behaviour for administrators. A user being a member of the local Administrators group does not prove that the current process is running elevated. Integrity levels (low, medium, high, system) add another layer to Windows process isolation.

### ACLs and security descriptors

An access control list contains access control entries (ACEs) that grant or deny rights to principals. Inspect both the object and its parent directory. For a service executable, a writable parent directory can matter even if the executable itself appears protected.

### Services and service accounts

Services can run as LocalSystem, LocalService, NetworkService, or a dedicated account. Review the service's configured binary path, account, start mode, dependencies, and ACLs. A privileged service is a potential risk only when an attacker-controlled change can influence what it executes or accesses.

### Registry

The registry is a hierarchical configuration database. Common hives include HKLM (machine-wide) and HKCU (current user). Review startup and application configuration carefully, but do not assume every Run key or writable value is exploitable. Confirm the consuming process, execution context, and permissions.

## 5.2 Windows host enumeration workflow

Start with low-impact, read-only collection:

```powershell
$env:COMPUTERNAME
$env:USERNAME
$PSVersionTable
whoami /all
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber, CsDomain
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, BuildNumber, OSArchitecture
Get-CimInstance Win32_ComputerSystem | Select-Object Name, Domain, PartOfDomain, Model
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember -Group 'Administrators'
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, Id, CPU
Get-Service | Sort-Object Status, Name
```

**What to record**

- Hostname, OS edition/build, architecture, PowerShell version.
- Current user, SID, group memberships, enabled privileges, integrity/elevation context.
- Local users and local administrators.
- Running processes and services.
- Network interfaces, routes, DNS configuration, and listening ports.
- Relevant security controls and event-log availability.

**Limitations:** Some cmdlets are unavailable on older Windows builds or in PowerShell 7 without the relevant module. Use `Get-Command `<name>` -ErrorAction SilentlyContinue` and `Get-Help `<name>` -Examples` to verify availability.

## 5.3 Network and domain context

Understand the difference between a workgroup and an Active Directory domain. In a domain, users, computers, groups, Group Policy, and authentication are centrally managed. Domain membership alone does not grant administrative access.

Read-only checks:

```powershell
Get-NetIPConfiguration
Get-NetRoute
Get-DnsClientServerAddress
Test-Connection -ComputerName $env:COMPUTERNAME -Count 2
if ($env:USERDNSDOMAIN) {
    Resolve-DnsName $env:USERDNSDOMAIN
    nltest "/dsgetdc:$env:USERDNSDOMAIN"
}
```

CMD equivalents include `ipconfig /all`, `route print`, `nslookup`, and `whoami /upn`.

**Core protocols to understand**

- DNS: name resolution and service discovery.
- SMB: Windows file and printer sharing, commonly TCP 445.
- Kerberos: ticket-based authentication in Active Directory.
- NTLM: challenge-response authentication, still present in some environments.
- LDAP/LDAPS: directory queries and management.
- WinRM: Windows remote management, commonly HTTP 5985 or HTTPS 5986.
- RPC: remote procedure call services used by many Windows management functions.

Do not equate an open port with a vulnerability. Determine the service, authentication requirements, access controls, exposure, and business purpose.

## 5.4 Privilege-escalation assessment methodology

Privilege escalation means moving from a lower-privileged security context to a higher-privileged one. In an assessment, identify a demonstrable security misconfiguration and prove its impact safely.

Review these categories:

1. **Excessive group membership:** Is the account in a group with unnecessary administrative rights?
2. **Service configuration and ACLs:** Can an unprivileged user modify a privileged service's executable, directory, configuration, or dependency?
3. **Scheduled tasks:** Who can modify the task definition or the files it executes? What account runs it?
4. **File and directory permissions:** Are sensitive scripts, binaries, or configuration directories writable by unintended users?
5. **Registry permissions:** Can an untrusted user alter a setting that a privileged process later consumes?
6. **Credential hygiene:** Are secrets stored in scripts, configuration files, deployment artefacts, or environment variables? Do not extract or reuse real credentials; use synthetic lab secrets.
7. **Patch and configuration state:** Is the system missing security updates or using an insecure configuration? Validate against authoritative vendor guidance.

Read-only inspection examples:

```powershell
whoami /all
Get-CimInstance Win32_Service | Select-Object Name, StartName, State, PathName
Get-ScheduledTask | Select-Object TaskName, TaskPath, State
Get-Acl 'C:\Lab' | Format-List
icacls C:\Lab
```

A finding should include the affected object, effective permissions, security principal, required conditions, impact, reproducible evidence, and remediation. Do not claim privilege escalation based solely on a suspicious path or a service running as SYSTEM.

## 5.5 Remote administration and lateral-movement concepts

Lateral movement describes access from one system to another using an identity, trust relationship, or remote administration mechanism. For eCPPT, understand the protocols and prerequisites:

- **WinRM / PowerShell remoting:** Requires remoting configuration, network reachability, authentication, and appropriate remote permissions.
- **SMB:** File sharing and named-pipe access are controlled by share permissions and NTFS permissions together.
- **RDP:** Interactive remote logon depends on firewall, service state, account rights, and policy.
- **WMI/CIM:** Management queries and remote operations depend on provider, firewall, DCOM/WinRM transport, and permissions.
- **Remote Service Control Manager:** Service administration is privileged and should be tightly restricted.

For a lab, validate only with designated test accounts and systems. Document which prerequisite failed or succeeded; do not attempt credential spraying, pass-the-hash, or access to real third-party hosts.

## 5.6 Windows authentication concepts

- **NTLM:** Challenge-response protocol. Understand why NTLM use and relay exposure matter, and why disabling or restricting NTLM requires compatibility planning.
- **Kerberos:** Uses tickets and a Key Distribution Center (KDC). Understand TGTs, service tickets, SPNs, time synchronisation, and common configuration risks.
- **Credential Guard:** Uses virtualization-based security to isolate certain secrets from the normal operating system.
- **LSASS:** A sensitive Windows process involved in local security authentication. Access to it is heavily protected and monitored.
- **MFA and least privilege:** Reduce the impact of password compromise and excessive account rights.

For exam preparation, be able to explain the authentication flow and identify relevant telemetry. Do not collect, dump, crack, or reuse credentials from real systems.

## 5.7 Persistence concepts — understand and detect

Persistence is a mechanism that causes code or access to survive logoff, restart, or other lifecycle events. Common Windows areas defenders audit include:

- Scheduled tasks.
- Services and drivers.
- Startup folders and Run/RunOnce registry locations.
- WMI event subscriptions.
- Logon scripts and Group Policy.
- New or modified accounts and group membership.

For each category, learn:

1. What legitimate administrative use it serves.
2. Which permissions control modification.
3. Which account/context executes it.
4. What event, registry, task, or file evidence is produced.
5. How to inventory and restore the lab to its original state.

Do not deploy covert persistence on real endpoints. In a lab, use benign marker actions, capture the resulting telemetry, and remove the test artefact immediately after documenting it.

## 5.8 Detection and event correlation

Useful Windows Security event IDs include:

| Event ID | Meaning | Investigation notes |
| --- | --- | --- |
| 4624 | Successful logon | Review logon type, account, source address, and authentication package |
| 4625 | Failed logon | Correlate repeated failures, target accounts, source host, and time |
| 4648 | Logon attempted using explicit credentials | Not proof of Pass-the-Hash; inspect process, account, and surrounding events |
| 4672 | Special privileges assigned to a new logon | Common for privileged accounts; validate expected admin activity |
| 4688 | Process creation | Command-line visibility depends on audit policy configuration |
| 4697 | Service installed (Security log, if audited) | Correlate with System event 7045 and change records |
| 4720 | User account created | Identify creator, target account, and subsequent group changes |
| 4732 | Member added to a security-enabled local group | Confirm group and member SIDs |
| 4719 | System audit policy changed | Investigate unexpected reductions in audit coverage |
| 1102 | Security audit log cleared | High-priority evidence; preserve central copies |

PowerShell logging:

- **4103:** Module logging records pipeline execution details when configured.
- **4104:** Script Block Logging records script block content when configured.
- **400/403:** Windows PowerShell engine lifecycle events.
- **4688 / Sysmon 1:** Process creation telemetry; command-line capture depends on configuration.

An event ID is not a complete verdict. Correlate timestamps, host, account SID, logon ID, process ID, parent process, command line, network connection, and adjacent events. PowerShell logs are usually under the appropriate PowerShell Operational channel, not simply the classic Security log.

## 5.9 Practical eCPPT lab progression

| Lab | Objective | Evidence to collect | Completion test |
| --- | --- | --- | --- |
| 1. CMD orientation | Identify OS, user, network, processes | Command output and notes | Explain every field used |
| 2. PowerShell pipeline | Filter and sort processes/services | Script and output | Produce correct filtered results |
| 3. Token and permissions | Compare standard and elevated context | `whoami /all`, ACL output | Explain why access differs |
| 4. Service audit | Review a deliberately misconfigured lab service | Service config and ACLs | Explain whether the issue is exploitable and why |
| 5. Event-log hunt | Find logon and process events | EVTX query and timeline | Correlate at least three relevant events |
| 6. Domain fundamentals | Identify domain and DNS context | Domain/DNS outputs | Explain the role of DNS, Kerberos, and LDAP |
| 7. Remote management | Verify authorised WinRM configuration | Listener, firewall, and permissions | Explain prerequisites and security controls |
| 8. Incident report | Write up one lab finding | Evidence, impact, fix, retest | Another learner can reproduce the conclusion |

## 9.10 Reporting template

For every issue found, document:

- **Title:** Clear, specific weakness.
- **Affected asset:** Lab host and relevant component.
- **Severity:** Based on demonstrated impact and exploitability, not guesswork.
- **Prerequisites:** User rights, network access, configuration, and other conditions.
- **Evidence:** Exact read-only commands, relevant output, event IDs, timestamps, and screenshots where appropriate.
- **Impact:** What an attacker could realistically achieve.
- **Remediation:** Least privilege, correct ACLs, patching, application control, service hardening, or network restriction.
- **Retest:** Evidence that the issue is fixed.
- **Limitations:** What was not tested or could not be confirmed.

---

## Completion checklist

- [ ] I can explain SIDs, access tokens, UAC, integrity, and ACLs.
- [ ] I can explain service accounts, authentication, and Windows security boundaries.
- [ ] I can describe how permissions affect a process or service.

MODULE 06 — Initial Access: Scope, Rules, and First Validation

This module explains what to do immediately after an initial access event has been established during an authorised assessment. Initial access may be a provided test account, a sanctioned remote shell, a lab foothold, or another explicitly approved access method. The objective is to validate the session and its boundaries—not to obtain access through an unapproved technique.

## 6.1 Rules before running commands

1. **Confirm written authorisation.** Know who approved the test and which engagement or lab it belongs to.
2. **Confirm scope.** Record permitted hosts, IP ranges, accounts, domains, time windows, and excluded systems.
3. **Confirm permitted actions.** Separate read-only enumeration from actions that modify configuration, create accounts, execute files, or access sensitive data.
4. **Use least impact.** Start with commands that collect basic context. Do not assume administrator rights are needed.
5. **Protect data.** Do not collect real passwords, tokens, private files, or unrelated personal information. Use synthetic lab data.
6. **Preserve evidence.** Record timestamp, host, user, command, output, and errors. Store evidence only in the approved location.
7. **Know stop conditions.** Stop if the session reaches an out-of-scope host, production data, a safety-critical service, or an unexpected system.
8. **Do not hide activity.** Do not disable endpoint protection, bypass monitoring, clear logs, or conceal command execution.
9. **Do not persist without explicit approval.** Persistence testing must be specifically scoped and use benign lab markers with immediate cleanup.
10. **Report unexpected impact immediately.** Follow the engagement escalation procedure.

## 6.2 First five minutes — validate the session

Ask: Which host am I on? Which account and security context am I using? Which shell and version are active? Is this host in scope? Is the session interactive, remote, or running under a service context? What can I safely inspect?

**CMD baseline**

```shell
whoami
whoami /all
hostname
ver
echo %COMPUTERNAME%
echo %USERDOMAIN%
ipconfig /all
```

**PowerShell baseline**

```powershell
$PSVersionTable
whoami /all
hostname
Get-Date
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture
```

Interpretation:

- `whoami` identifies the current principal; `whoami /all` shows group and privilege information for the current token.
- `hostname` identifies the computer; compare it with the approved asset list.
- `$PSVersionTable` identifies the shell version. A command available in Windows PowerShell 5.1 may not exist in PowerShell 7.
- `ipconfig /all` shows interface and DNS configuration; it does not authorise testing every reachable network.
- Access denied is a permission boundary. Do not automatically elevate or attempt to bypass it.

## 6.3 Identify the access context

| Access context | Meaning | What to validate |
| --- | --- | --- |
| Standard user shell | Commands run with the user's current token | User SID, groups, integrity context, permitted scope |
| Elevated administrator shell | Process has an elevated token | Confirm elevation and approval |
| Service account context | A service identity executes the process | Service identity, host role, allowed access, owner |
| Remote management session | Session established through a management protocol | Source host, destination, authentication, remote permissions |
| Scheduled or automated context | Task or automation runs under a configured identity | Task owner, execution account, trigger, authorisation |

Do not infer effective privileges from an account name alone. Inspect the actual token and process context.

## 6.4 First-response command sequence

Run only commands required by the task and approved scope.

```powershell
# Identity and host
whoami /all
hostname
$PSVersionTable

# Operating-system context
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture

# Network configuration
Get-NetIPConfiguration
Get-NetRoute

# Process and service inventory
Get-Process | Select-Object -First 20 Name, Id, CPU
Get-Service | Select-Object Name, Status, StartType

# Local group context
Get-LocalGroupMember -Group 'Administrators' -ErrorAction SilentlyContinue
```

If a cmdlet is unavailable, check command discovery and the operating-system version. Use the CMD equivalent where appropriate. Do not silently treat an unavailable command as a negative finding.

## 6.5 What not to do after initial access

- Do not dump credentials, browser secrets, password stores, or LSASS memory.
- Do not search broadly for passwords or tokens in user directories.
- Do not disable antivirus, EDR, logging, firewall, or other security controls.
- Do not clear event logs or alter audit policy to conceal activity.
- Do not create hidden users, scheduled tasks, services, startup entries, or remote access.
- Do not run downloaded scripts or encoded commands from an untrusted source.
- Do not pivot to another host unless it is in scope and the method is approved.

## 6.6 Evidence record

| Field | What to record |
| --- | --- |
| Time | Local timestamp with time zone |
| Asset | Approved hostname or asset ID |
| Identity | User or service account; SID where relevant |
| Session | Local, remote, elevated, or service context |
| Command | Exact command and parameters |
| Result | Relevant output, not an unfiltered data dump |
| Error | Exact error and what it may indicate |
| Scope decision | Why the next action is permitted |

## Completion checklist

- [ ] I can state scope, rules, and stop conditions before testing.
- [ ] I can validate a provided session without assuming elevated rights.
- [ ] I can record commands and evidence safely.

MODULE 07 — Post-Initial-Access Enumeration

Post-initial-access enumeration is structured information collection from a host after an authorised foothold has been provided or established. The aim is to understand the environment and identify questions requiring further validation. Enumeration is not exploitation: collect only what is in scope, interpret it carefully, and avoid changing the host.

## 7.1 Enumeration order

1. Identity and token context.
2. Hostname, OS, architecture, and PowerShell version.
3. Local users, groups, and privilege context.
4. Processes and services.
5. Network interfaces, routes, DNS, and listening sockets.
6. Scheduled tasks and startup configuration.
7. Shares, files, and permissions relevant to the engagement.
8. Domain and remote-management context, if applicable.
9. Event-log and security-control visibility.
10. Summarise observations and decide what needs validation.

## 7.2 Identity and token enumeration

**CMD**

```shell
whoami
whoami /user
whoami /groups
whoami /priv
net user %USERNAME%
net localgroup
net localgroup Administrators
```

**PowerShell**

```powershell
[System.Security.Principal.WindowsIdentity]::GetCurrent() |
    Select-Object Name, User, Groups
Get-LocalUser | Select-Object Name, Enabled, LastLogon, PasswordRequired
Get-LocalGroup
Get-LocalGroupMember -Group 'Administrators' -ErrorAction SilentlyContinue
```

Group membership, enabled privileges, and elevation are different concepts. A user may belong to Administrators while the current process still has a filtered, non-elevated token.

## 7.3 Host and operating-system enumeration

**CMD**

```shell
hostname
ver
systeminfo
echo %PROCESSOR_ARCHITECTURE%
```

**PowerShell**

```powershell
Get-ComputerInfo |
    Select-Object CsName, WindowsProductName, WindowsVersion,
                  OsBuildNumber, OsArchitecture, CsDomain
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture, LastBootUpTime
```

Record edition, build, architecture, uptime, domain/workgroup context, and collection time. An OS version alone does not prove that a patch is missing; compare the build and installed updates with authoritative vendor guidance.

## 7.4 Process enumeration

**CMD**

```shell
tasklist
tasklist /v
tasklist /svc
```

**PowerShell**

```powershell
Get-Process |
    Select-Object Name, Id, CPU, WorkingSet, StartTime -ErrorAction SilentlyContinue
Get-CimInstance Win32_Process |
    Select-Object Name, ProcessId, ParentProcessId, ExecutablePath, CommandLine
```

Validate unusual processes using executable path, digital signature, parent process, account, start time, and endpoint telemetry. Process names can be imitated. Some properties may be unavailable without sufficient permissions.

## 7.5 Service enumeration

**CMD**

```shell
sc query state= all
sc qc Spooler
```

**PowerShell**

```powershell
Get-Service |
    Sort-Object Status, Name |
    Select-Object Name, DisplayName, Status, StartType
Get-CimInstance Win32_Service |
    Select-Object Name, DisplayName, State, StartMode, StartName, PathName
```

Prioritise services that run with powerful accounts, have unusual binary paths, or depend on files/directories with unexpected permissions. A service running as LocalSystem is not by itself a vulnerability.

## 7.6 Network enumeration

**CMD**

```shell
ipconfig /all
route print
arp -a
netstat -ano
net use
net share
```

**PowerShell**

```powershell
Get-NetIPConfiguration
Get-NetRoute
Get-NetNeighbor
Get-DnsClientServerAddress
Get-NetTCPConnection |
    Sort-Object State, LocalPort |
    Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess
Get-SmbShare
```

Understand the difference between an interface address, route, neighbour-cache entry, DNS server, listening socket, and established connection. A neighbour-cache entry is not proof that a host is currently reachable. A listening port is not proof of a vulnerability.

## 7.7 Scheduled tasks and startup configuration

Read-only inventory:

```powershell
Get-ScheduledTask | Select-Object TaskName, TaskPath, State, Author
Get-CimInstance Win32_StartupCommand | Select-Object Name, Command, Location, User
Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' -ErrorAction SilentlyContinue
```

Review the task or startup entry's owner, execution account, trigger, referenced file, and permissions. Do not create or modify startup entries during enumeration.

## 7.8 Shares and relevant file permissions

```powershell
Get-SmbShare | Select-Object Name, Path, Description
Get-Acl 'C:\Lab' | Select-Object Path, Owner, AccessToString
icacls C:\Lab
```

Inspect only approved lab or engagement paths. Do not recursively search user profiles for passwords, tokens, browser data, private keys, or personal documents. If secret-handling validation is required, use synthetic canary files created specifically for that purpose.

## 7.9 Domain and remote context

```powershell
$env:USERDOMAIN
$env:LOGONSERVER
if ($env:USERDNSDOMAIN) {
    nltest "/dsgetdc:$env:USERDNSDOMAIN"
    Resolve-DnsName $env:USERDNSDOMAIN
}
Test-WSMan -ComputerName 'localhost'
```

These checks help identify domain and management context. A successful DNS lookup or WinRM response does not grant permission to access the remote host. Validate the target against scope before any remote action.

## 7.10 Enumeration record

| Area | Observation | Evidence | Confidence | Follow-up |
| --- | --- | --- | --- | --- |
| Identity | Current user and token context | whoami /all | Confirmed / partial | Validate expected access |
| Host | OS/build and role | CIM / ComputerInfo | Confirmed / partial | Compare patch baseline |
| Services | Unusual account/path/config | Service inventory | Needs validation | Inspect effective ACL |
| Network | Interface, route, listener | Network cmdlets | Confirmed / partial | Compare with scope |
| Tasks | Unexpected task or executable | Task inventory | Needs validation | Verify owner and file ACL |
| Shares | Share and NTFS permissions | SMB / ACL output | Needs validation | Verify intended access |

## Completion checklist

- [ ] I can collect host, identity, process, service, network, task, and domain context.
- [ ] I can distinguish an observation from a vulnerability.
- [ ] I can record concise evidence and note collection limitations.

MODULE 08 — Post-Enumeration Analysis and Prioritisation

Enumeration produces observations. Post-enumeration turns those observations into defensible conclusions and a prioritised assessment plan. Do not jump from “I found something unusual” to “I can exploit it.” Verify the full security and execution chain first.

## 8.1 Observation-to-finding process

1. **Observation:** Record exactly what the command returned.
2. **Hypothesis:** Explain why the result may matter.
3. **Validation:** Check permissions, ownership, execution context, dependencies, and scope.
4. **Impact analysis:** Determine what the weakness could allow under realistic prerequisites.
5. **Evidence:** Preserve the smallest sufficient set of outputs and timestamps.
6. **Risk rating:** Consider exploitability, privileges required, exposure, and business impact.
7. **Recommendation:** State the control that would remove or reduce the risk.
8. **Retest:** Verify the fix using the same read-only checks.

## 8.2 Decision guide — what should I investigate next?

| Observation | Next validation question | Do not assume |
| --- | --- | --- |
| User belongs to Administrators | Is the current process elevated? Which token groups/privileges are enabled? | Group membership automatically means an elevated shell |
| Service runs as LocalSystem | Can an untrusted principal modify its executable, directory, configuration, or dependencies? | SYSTEM service automatically means exploitable |
| Service path contains spaces | Is the path quoted correctly, and can an untrusted user write to a relevant path component? | Every unquoted path is exploitable |
| Scheduled task runs with high privileges | Who can modify the task or referenced files? What is the trigger and execution context? | A privileged task is automatically vulnerable |
| Registry Run entry exists | Is the entry expected, signed, and protected by correct ACLs? | Every startup entry is malicious |
| Listening TCP port exists | Which process owns it, what service is bound, and is it exposed beyond the host? | Open port equals vulnerability |
| Domain controller is discoverable | Is the host in scope and what authenticated read access is permitted? | Domain discovery authorises remote access |
| Access denied | Which right is missing and is the command supported in this context? | Access denied proves a control is weak |

## 8.3 Prioritise using evidence

Consider privilege impact, exploit prerequisites, reachability, reliability, business impact, and compensating controls. Explain the prerequisites and evidence behind the risk rating. Do not assign severity solely because a command returned a suspicious string.

## 8.4 Evidence quality rules

- Capture exact command, parameters, host, identity, timestamp, and relevant output.
- Prefer structured output over screenshots of truncated console text.
- Redact secrets and personal data before storing or sharing evidence.
- Keep original evidence immutable where chain-of-custody controls apply.
- Distinguish “not observed” from “not present.” Lack of visibility is not proof that an event did not occur.
- Record tool/version limitations and access errors.
- Do not alter the system to make a finding easier to demonstrate unless the test plan explicitly authorises it.

## 8.5 Post-enumeration deliverable

Answer: What access context was provided? Which systems and identities were confirmed in scope? What was enumerated? Which observations were validated? Which issues are confirmed, suspected, or not reproducible? What evidence supports each conclusion? What should be investigated next? What remediation is recommended?

## 8.6 Knowledge challenge

You discover a service running as LocalSystem with a binary path under C:\\Program Files\\Vendor\\App.

- What additional information do you need before calling this a finding?
- Which ACLs and parent directories should be reviewed?
- How would you identify the service's configured account and executable path?
- What would count as evidence of a genuine permission weakness?
- How would you report the risk without replacing the binary or launching a privileged payload?

## Completion checklist

- [ ] I can validate observations before drawing conclusions.
- [ ] I can prioritise based on prerequisites, exposure, and impact.
- [ ] I can write a defensible next-step plan.

MODULE 09 — Privilege Boundaries and Misconfiguration Assessment

This module applies Windows security architecture to authorised assessment. The objective is to identify a configuration weakness, validate effective permissions, explain realistic impact, and recommend remediation. Begin with read-only checks. Do not replace binaries, create privileged tasks, alter registry startup entries, or deploy persistence as proof of concept.

## 9.1 Establish the privilege boundary

```shell
whoami /all
```

```powershell
[System.Security.Principal.WindowsIdentity]::GetCurrent() |
    Select-Object Name, User, Groups
```

Establish the current user SID, group SIDs, process token, integrity level, elevation state, and enabled privileges. Account group membership is not the same as the effective token of the current process.

## 9.2 Service configuration assessment

```powershell
Get-CimInstance Win32_Service |
    Select-Object Name, DisplayName, State, StartMode, StartName, PathName
```

```shell
sc qc Spooler
```

Inspect the executable and relevant parent-directory ACLs:

```powershell
Get-Acl 'C:\Path\To\Service.exe' | Format-List Owner, Access
icacls 'C:\Path\To\Service.exe'
icacls 'C:\Path\To'
```

A valid finding requires evidence that an untrusted principal has an effective permission that can influence a privileged service's execution or configuration. Consider inherited permissions, group membership, deny entries, parent-directory rights, dependencies, and service account. A writable file alone is not enough unless the privileged process actually uses it.

## 9.3 Scheduled task assessment

```powershell
Get-ScheduledTask | Select-Object TaskName, TaskPath, State, Author
$task = Get-ScheduledTask | Select-Object -First 1
$task | Select-Object TaskName, TaskPath, State, Principal, Actions, Triggers
```

Review permissions on the task definition and referenced executable/script. Confirm who can modify each component and which account runs the task. Do not modify or trigger a privileged task to prove impact unless that exact test is approved.

## 9.4 File and directory ACL assessment

```powershell
Get-Acl 'C:\Lab\Target' | Format-List Owner, Access
icacls C:\Lab\Target
icacls C:\Lab\Target\*
```

Understand read, write, modify, delete, execute, and full-control rights. Check inherited ACEs and group membership. Effective access can depend on nested groups and deny entries; do not interpret one ACE in isolation.

## 9.5 Registry permission assessment

```powershell
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion'
Get-Acl 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion' |
    Format-List Owner, Access
```

A registry key matters only when a process or service consumes the setting in a security-sensitive context. Validate the key's purpose, effective write permissions, and the identity of the consuming process.

## 9.6 Credential hygiene — safe assessment

Review secret-management controls only within an explicitly approved test location. Use synthetic credentials or canary values supplied for the exercise. Do not dump LSASS, extract browser credentials, collect password databases, or reuse credentials found on a real host.

Review whether secrets are stored in source code or deployment files, whether access is restricted, whether service identities are managed, whether Credential Guard and LSA protection are enabled where supported, and whether MFA and rotation policies are appropriate.

## 9.7 Remote administration prerequisites

| Technology | Validate | Security consideration |
| --- | --- | --- |
| WinRM / PowerShell remoting | Listener, firewall, authentication, remote permissions | Restrict source networks and administrative groups |
| SMB | Share ACL and NTFS ACL, signing, exposure | Apply least privilege and limit unnecessary shares |
| RDP | Service, firewall, logon rights, NLA | Restrict access and require MFA where supported |
| WMI/CIM | Transport, provider permissions, firewall | Restrict remote management rights |
| RPC | Required services and firewall scope | Avoid broad exposure of management interfaces |

Read-only checks:

```powershell
Test-WSMan -ComputerName 'localhost'
Get-NetFirewallProfile
Get-SmbShare
```

Successful connectivity does not authorise a remote command. Remote access must remain within the approved asset list and account permissions.

## 9.8 Persistence — defender-focused review

Inventory scheduled tasks, services and drivers, startup folders, Run/RunOnce keys, WMI event subscriptions, logon scripts, Group Policy, and account/group changes. Record owner, execution identity, trigger, referenced file, ACL, creation/modification time, and expected business purpose. Use benign marker artefacts only in a dedicated lab and remove them after the exercise. Do not deploy covert persistence.

## 9.9 Finding template

| Field | Required content |
| --- | --- |
| Title | Specific weakness and affected component |
| Asset | Host and component in scope |
| Preconditions | Required user rights, access, and configuration |
| Evidence | Exact read-only command, output, timestamp, and ACL |
| Validation | Why the permission is effective and how the component consumes it |
| Impact | Realistic privilege or security-boundary consequence |
| Severity | Based on demonstrated impact and exploitability |
| Remediation | Correct ACLs, service configuration, task ownership, or least privilege |
| Retest | Evidence that the weakness is resolved |
| Limitations | What could not be tested or confirmed |

## Completion checklist

- [ ] I can inspect service, task, file, and registry permissions read-only.
- [ ] I can explain the chain required for a privilege-boundary weakness.
- [ ] I can document remediation and retest evidence.

MODULE 10 — Defensive Monitoring and Incident Response

This module teaches how defenders observe PowerShell and Windows activity, investigate suspicious behaviour, and improve host security. Event IDs are starting points, not proof. Always correlate multiple sources.

## 10.1 Windows event-log fundamentals

```powershell
Get-WinEvent -ListLog * |
    Where-Object IsEnabled |
    Select-Object LogName, RecordCount, IsEnabled

Get-WinEvent -LogName 'System' -MaxEvents 20 |
    Select-Object TimeCreated, Id, ProviderName, LevelDisplayName, Message
```

Filter by event ID:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id      = 4624, 4625
} -MaxEvents 50 |
    Select-Object TimeCreated, Id, ProviderName, Message
```

If access is denied, record the limitation. Do not clear logs or weaken audit policy to make collection easier.

## 10.2 Important Windows event IDs

| Event ID | Meaning | Investigation focus |
| --- | --- | --- |
| 4624 | Successful logon | Logon type, account, source, authentication package |
| 4625 | Failed logon | Target account, status/substatus, source host |
| 4648 | Explicit credentials used | Process, account, source; not proof of Pass-the-Hash |
| 4672 | Special privileges assigned to a logon | Expected privileged account and logon context |
| 4688 | Process creation | Parent process and command line if auditing is enabled |
| 4697 | Service installed (Security log, if audited) | Correlate with System 7045 and change records |
| 4698 | Scheduled task created | Creator, task definition, principal, action |
| 4720 | User account created | Creator, target account, subsequent changes |
| 4732 | Member added to local security group | Group, member SID, initiating account |
| 4719 | Audit policy changed | Determine whether visibility was reduced |
| 1102 | Security audit log cleared | Preserve central copies and investigate cause |
| 7045 | Service installed (System log) | Service name, image path, account |
| 4103 | PowerShell module logging | Requires policy configuration |
| 4104 | PowerShell Script Block Logging | Requires policy configuration |

## 10.3 PowerShell logging

- **Script Block Logging (4104):** Records script block content when configured.
- **Module Logging (4103):** Records pipeline execution details for configured modules.
- **Engine lifecycle events (400/403):** Help identify PowerShell session start and stop.
- **Process creation (4688 / Sysmon 1):** Can provide process and command-line context when configured.

```powershell
Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -MaxEvents 30 |
    Select-Object TimeCreated, Id, ProviderName, Message
```

Logging coverage depends on policy, version, channel state, and collection configuration. Absence of an event does not prove that a command did not run.

## 10.4 Investigation workflow

1. Confirm the alert and affected asset.
2. Establish the time window and time zone.
3. Identify user SID, logon ID, source host, and process ID.
4. Review process creation and parent-child relationships.
5. Review relevant PowerShell Operational and Security events.
6. Correlate network connections, service/task changes, and file activity.
7. Compare activity with change tickets and expected administration.
8. Preserve evidence and escalate according to incident-response policy.

## 10.5 Defensive configuration review

Review through approved administrative policy and management tools: PowerShell Script Block Logging and Module Logging; centralised event forwarding and retention; Defender health; application control using WDAC or AppLocker; least privilege; Credential Guard and LSA protection where supported; WinRM firewall scope; service/task/startup baselines; and patch level.

**Important:** PowerShell Execution Policy is not a security boundary. Do not rely on it as malware prevention. Use application control, endpoint protection, least privilege, and logging as part of a layered design.

## 10.6 Constrained Language Mode

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Constrained Language Mode is normally enforced through application-control policy scenarios. It is not a simple per-user registry switch. Validate the policy and actual runtime state; do not use undocumented environment-variable toggles as a production configuration method.

## 10.7 WinRM security

WinRM is a remote-management service, not a security bypass. Review whether remote management is required, which hosts can reach the listener, whether HTTPS or Kerberos is used appropriately, which accounts have remote access rights, whether firewall rules are restricted, and whether session activity is logged.

`TrustedHosts` is a client-side setting used in some non-Kerberos connection scenarios. It is not an inbound firewall allow-list and is not a substitute for authentication or network controls.

## 10.8 Incident-response evidence preservation

Collect only what the response plan authorises. Record timestamps, host, user, process, command, event source, and collection method. Preserve original logs and central copies. Do not clear logs, overwrite evidence, or make unapproved changes to the affected system.

## Completion checklist

- [ ] I can query and correlate Security, System, and PowerShell logs.
- [ ] I can explain key event IDs and their limitations.
- [ ] I can describe evidence preservation and defensive hardening.

MODULE 11 — Practical Labs and Validation

All labs must run in an isolated host-only network using snapshots and synthetic accounts. Do not use production credentials, public targets, or real persistence payloads.

**11.1 Lab environment**

| Component | Suggested setup | Purpose |
| --- | --- | --- |
| Windows client | Windows 10/11 evaluation VM | PowerShell and CMD practice |
| Windows Server | Evaluation VM (optional) | Domain and remote-management concepts |
| Network | Host-only / isolated virtual network | Prevent unintended access to external systems |
| Monitoring | Event Viewer and Sysmon | Observe process and system activity |
| Recovery | VM snapshot | Restore the lab after each exercise |

**11.2 Lab sequence**

1. **CMD fundamentals:** collect hostname, OS details, user identity, network configuration, processes, and services. Explain each output field.
2. **PowerShell pipeline:** retrieve processes and services, filter with `Where-Object`, select fields, sort results, and export a CSV.
3. **Windows security context:** compare a standard terminal with an explicitly elevated lab terminal. Use `whoami /all` and explain groups, privileges, and integrity context.
4. **Permissions review:** inspect a deliberately misconfigured lab folder and service configuration. Identify the principal, access right, and potential impact without replacing binaries or launching a privileged payload.
5. **Event-log investigation:** query logon, process-creation, service-installation, and PowerShell Operational events. Build a short timeline and correlate host, account, process, and timestamp.
6. **Remote administration:** verify WinRM configuration and firewall scope between two lab VMs using designated test accounts. Document prerequisites and access controls.
7. **Incident-response report:** write one finding with evidence, impact, remediation, limitations, and retest steps.

**11.3 Useful verification commands**

```powershell
Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -MaxEvents 20 |
    Select-Object TimeCreated, Id, ProviderName, Message

Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624,4625,4688} -MaxEvents 50 |
    Select-Object TimeCreated, Id, ProviderName, Message

Get-NetTCPConnection |
    Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State

Get-FileHash 'C:\Lab\sample.txt' -Algorithm SHA256
```

**11.4 Lab completion criteria**

- Explain the purpose and limitations of every command used.
- Preserve relevant output and timestamps as evidence.
- Identify which actions generated logs and which did not.
- Restore the VM snapshot or remove only the benign lab artefacts created for the exercise.
- Submit a concise finding with a practical remediation and retest result.

## Completion checklist

- [ ] I can complete the lab objectives in an isolated environment.
- [ ] I can explain each command and interpret the result.
- [ ] I can restore the snapshot or remove approved lab artefacts.

MODULE 12 — Quick Reference and Exam Revision

Use this section as a revision index, not as a substitute for the explanations and labs above.

**12.1 Core PowerShell commands**

| Task | PowerShell | CMD |
| --- | --- | --- |
| Identify user | `whoami /all` | `whoami /all` |
| Identify host and OS | `Get-ComputerInfo` | `hostname`, `systeminfo` |
| List files | `Get-ChildItem` | `dir` |
| Read text | `Get-Content` | `type` |
| Search text | `Select-String` | `findstr` |
| List processes | `Get-Process` | `tasklist` |
| List services | `Get-Service` | `sc query` |
| Network configuration | `Get-NetIPConfiguration` | `ipconfig /all` |
| Routes | `Get-NetRoute` | `route print` |
| DNS lookup | `Resolve-DnsName` | `nslookup` |
| File permissions | `Get-Acl` | `icacls` |
| Registry query | `Get-ItemProperty` | `reg query` |
| Event logs | `Get-WinEvent` | `wevtutil qe` |
| Export results | `Export-Csv` | output redirection |

**12.2 PowerShell pipeline pattern**

```powershell
Get-Process |
    Where-Object { $_.CPU -gt 10 } |
    Sort-Object CPU -Descending |
    Select-Object -First 10 Name, Id, CPU |
    Format-Table -AutoSize
```

Remember: PowerShell passes objects through the pipeline; CMD generally passes text. Filter and select object properties before formatting output.

**12.3 Windows security concepts to explain**

- **Identity:** user, group, computer account, and Security Identifier (SID).
- **Access token:** identity, group memberships, privileges, and integrity context attached to a process.
- **UAC:** elevation and split-token behaviour for administrative users.
- **ACL / ACE:** permissions assigned to a securable object and the entries that grant or deny access.
- **Service security:** service account, executable path, configuration, dependencies, and effective permissions.
- **Authentication:** Kerberos, NTLM, tickets, service principal names, and multi-factor authentication.
- **Remote management:** WinRM, SMB, RDP, WMI/CIM, RPC, firewall rules, and account rights.
- **Logging:** Security, System, PowerShell Operational, and Sysmon telemetry.

**12.4 Event IDs — investigation starting points**

| Event ID | Meaning | Important context |
| --- | --- | --- |
| 4624 | Successful logon | Logon type, account, source, authentication package |
| 4625 | Failed logon | Status/substatus, source, target account |
| 4648 | Explicit credentials used | Not proof of Pass-the-Hash |
| 4672 | Special privileges assigned to a logon | Validate expected privileged activity |
| 4688 | Process created | Command-line auditing must be configured |
| 4697 / 7045 | Service installed | Security and System log sources differ |
| 4698 | Scheduled task created | Review task definition and creator |
| 4720 | User account created | Identify creator and target |
| 4732 | Member added to local security group | Confirm group and member |
| 4719 | Audit policy changed | Check for reduced visibility |
| 1102 | Security audit log cleared | Preserve central log copies |
| 4103 | PowerShell module logging | Requires policy configuration |
| 4104 | PowerShell Script Block Logging | Requires policy configuration |

An event ID is an investigative lead, not proof of an attack. Correlate timestamps, account and logon IDs, host, process, parent process, command line, network activity, and change records.

**12.5 Exam readiness checklist**

- [ ] Explain CMD versus PowerShell and object pipelines.
- [ ] Use help, command discovery, filtering, sorting, and output export.
- [ ] Write a script using variables, conditions, loops, functions, and error handling.
- [ ] Enumerate Windows host, user, process, service, network, and task information.
- [ ] Explain access tokens, UAC, ACLs, services, registry permissions, and authentication.
- [ ] Assess a misconfiguration without assuming that suspicious configuration proves exploitability.
- [ ] Query and correlate Windows and PowerShell event logs.
- [ ] Explain WinRM, SMB, RDP, Kerberos, NTLM, LDAP, and RPC prerequisites.
- [ ] Produce a finding with evidence, impact, remediation, and retest.

**Study priority:** Understand the security model and interpret evidence accurately. Memorising commands without understanding permissions, prerequisites, output limitations, and detection is not sufficient for a practical assessment.

---

**Additional revision checklist**

- [ ] Explain CMD text pipelines versus PowerShell object pipelines.
- [ ] Identify Windows version, build, architecture, user, groups, and privileges.
- [ ] Inspect processes, services, scheduled tasks, and file permissions.
- [ ] Explain NTFS ACLs, registry permissions, access tokens, and UAC.
- [ ] Read network configuration and interpret listening ports.
- [ ] Explain DNS, SMB, Kerberos, NTLM, LDAP, RPC, WinRM, and RDP.
- [ ] Query Security, System, and PowerShell Operational event logs.
- [ ] Distinguish Event IDs 4624, 4625, 4648, 4672, 4688, 4697, 4720, 4732, 4719, and 1102.
- [ ] Explain Script Block Logging 4104 and Module Logging 4103.
- [ ] Explain why Execution Policy is not a security boundary.
- [ ] Write a small PowerShell script with parameters, validation, and error handling.
- [ ] Produce a clear finding with evidence, impact, remediation, and retest.

**Final study rule:** Learn what the command reveals, what it does not reveal, which permissions it requires, how output can mislead you, and how a defender can observe the activity. That reasoning is more valuable in an exam than memorising a long list of commands.

## Completion checklist

- [ ] I can locate the right command reference without memorising everything.
- [ ] I can answer scenario-based questions with evidence.
- [ ] I can write a finding with impact, remediation, and retest.
