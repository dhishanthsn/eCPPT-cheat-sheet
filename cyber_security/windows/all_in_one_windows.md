# all\_in\_one\_windows — Windows eCPPT Notes

<blockquote>
Windows commands, security concepts and host-enumeration notes.

<b>Format:</b> command → one-line explanation. Short explanations, full concepts.

<b>Lab rule:</b> Use only systems you own or are authorised to test. Prefer read-only checks. Take a VM snapshot before configuration changes.

The collapsible source modules below are kept unchanged.
</blockquote>

## 1. Windows fundamentals

| Term | Simple meaning |
| --- | --- |
| Kernel mode | Core Windows code that manages hardware, memory and processes |
| User mode | Where normal applications and most services run |
| Service Control Manager (SCM) | Starts, stops and manages Windows services |
| NTFS | Windows filesystem with permissions, ownership and auditing |
| Registry | Database for Windows and application configuration |
| LSASS | Process responsible for important local security and authentication functions |
| UAC | Helps control when an application receives administrative rights |
| Access token | Stores a process/user identity, groups, privileges and integrity level |
| ACL / ACE | ACL is the permission list; an ACE is one allow/deny entry |
| Inheritance | Permissions passed from a parent object to child files/folders |
| Effective access | What an identity can actually do after group membership and ACL rules are applied |
| PowerShell | Shell that passes objects through its pipeline |
| CMD | Command Prompt; its pipelines mainly pass text |

### Important Windows paths

| Path | What to look for |
| --- | --- |
| <code>C:\Windows\System32</code> | Core Windows binaries, services and libraries |
| <code>C:\Windows\SysWOW64</code> | 32-bit system components on 64-bit Windows |
| <code>C:\Program Files</code> | Common 64-bit application location |
| <code>C:\Program Files (x86)</code> | Common 32-bit application location |
| <code>C:\ProgramData</code> | Shared application data and configuration |
| <code>%APPDATA%</code> / <code>%LOCALAPPDATA%</code> | Per-user application data |
| <code>%TEMP%</code> | Temporary files; inspect context and permissions |
| <code>C:\Windows\System32\Tasks</code> | Scheduled-task definition files |

## 2. CMD quick reference

| Command | What it does |
| --- | --- |
| <code>whoami</code> | Shows the current account |
| <code>whoami /all</code> | Shows account, SID, groups, privileges and integrity information |
| <code>whoami /user</code> | Shows the current user's SID |
| <code>whoami /groups</code> | Lists group memberships |
| <code>whoami /priv</code> | Lists privileges in the current token |
| <code>hostname</code> | Shows the computer name |
| <code>ver</code> | Shows the Windows version string |
| <code>systeminfo</code> | Shows OS, build, hardware and hotfix information |
| <code>set</code> | Lists environment variables |
| <code>dir /a</code> | Lists files, including hidden and system entries |
| <code>ipconfig /all</code> | Shows IP, DNS, DHCP and adapter details |
| <code>route print</code> | Displays the routing table |
| <code>arp -a</code> | Displays the local ARP cache |
| <code>netstat -ano</code> | Shows connections, listening ports and owning PIDs |
| <code>tasklist /v</code> | Lists processes with detailed information |
| <code>tasklist /svc</code> | Maps processes to hosted services |
| <code>sc.exe query state= all</code> | Lists Windows services and their states |
| <code>sc.exe qc Spooler</code> | Shows the Spooler service configuration |
| <code>net user</code> | Lists local user accounts |
| <code>net localgroup</code> | Lists local groups |
| <code>net localgroup Administrators</code> | Shows local Administrators group members |
| <code>schtasks /query /fo LIST /v</code> | Lists scheduled tasks with detailed properties |
| <code>icacls C:\Lab</code> | Displays NTFS permissions on the specified path |
| <code>reg query "HKLM\SOFTWARE"</code> | Reads registry keys and values |
| <code>cmdkey /list</code> | Lists saved credential target names, not plaintext passwords |

<b>CMD output operators</b>

| Operator | Meaning |
| --- | --- |
| <code>></code> | Writes output to a file, replacing existing content |
| <code>>></code> | Appends output to a file |
| <code>2></code> | Redirects error output |
| <code>&&</code> | Runs the next command only if the previous command succeeds |
| OR operator | Runs the next command only if the previous command fails |
| <code>&</code> | Runs commands sequentially regardless of success |

Example evidence capture:

```shell
whoami /all > identity.txt
systeminfo > system.txt
ipconfig /all > network.txt
tasklist /svc > processes-services.txt
```

<b>What does it do?</b> Runs a baseline host-enumeration set and saves identity, operating-system, network and process/service output to separate text files. Store the output securely because it contains host details.

Each command saves its output to a separate text file.

## 3. PowerShell quick reference

### Learn the shell

| Command | What it does |
| --- | --- |
| <code>$PSVersionTable</code> | Shows PowerShell edition and version |
| <code>Get-Command Get-Process</code> | Finds command details and available parameters |
| <code>Get-Help Get-Process -Examples</code> | Shows usage examples |
| <code>Get-Process \| Get-Member</code> | Shows properties and methods returned by a command |
| <code>Get-Alias</code> | Lists command aliases |
| <code>Get-ExecutionPolicy -List</code> | Shows execution-policy settings by scope |

PowerShell commands usually follow <code>Verb-Noun -Parameter Value</code>.

### Common enumeration commands

| Command | What it does |
| --- | --- |
| <code>Get-ComputerInfo</code> | Shows broad OS and hardware information |
| <code>Get-CimInstance Win32_OperatingSystem</code> | Reads OS version, build and boot information |
| <code>Get-CimInstance Win32_ComputerSystem</code> | Shows computer and domain details |
| <code>Get-LocalUser</code> | Lists local user accounts |
| <code>Get-LocalGroup</code> | Lists local groups |
| <code>Get-LocalGroupMember -Group Administrators</code> | Lists local Administrators members |
| <code>Get-Process</code> | Lists running processes |
| <code>Get-Service</code> | Lists Windows services |
| <code>Get-NetIPConfiguration</code> | Shows network interface configuration |
| <code>Get-NetTCPConnection</code> | Shows TCP connections and listening sockets |
| <code>Get-NetRoute</code> | Shows network routes |
| <code>Get-Acl -LiteralPath 'C:\Lab'</code> | Reads file/directory permissions |
| <code>Get-ItemProperty 'HKLM:\SOFTWARE\Example'</code> | Reads registry values |
| <code>Get-ScheduledTask</code> | Lists scheduled tasks |
| <code>Get-WinEvent -LogName System -MaxEvents 20</code> | Reads recent System log events |
| <code>Get-HotFix</code> | Lists hotfix records exposed by this cmdlet |

### Pipeline example

```powershell
Get-Process |
    Where-Object { $_.CPU -gt 10 } |
    Sort-Object CPU -Descending |
    Select-Object -First 10 Name, Id, CPU
```

<b>What does it do?</b> Filters process objects, sorts by CPU time and displays the top ten. The CPU property is cumulative processor time, not a live CPU percentage.

- <code>Where-Object</code> — keeps objects matching the condition.
- <code>Sort-Object</code> — sorts the results by CPU use.
- <code>Select-Object</code> — displays only the chosen fields and first 10 results.

Use <code>Format-Table</code> or <code>Format-List</code> only for display at the end of a pipeline. Use <code>Export-Csv</code> to save structured results.

## 4. Identity, tokens and permissions

- <b>Security principal:</b> a user, group, computer or service identity Windows can authorise.
- <b>SID:</b> the unique identifier Windows uses for a security principal.
- <b>Access token:</b> identity, group SIDs, privileges and integrity context attached to a process.
- <b>UAC:</b> controls elevation prompts and filtered/elevated administrator tokens.
- <b>Integrity level:</b> trust level of a process (Low, Medium, High or System).
- <b>ACL:</b> permission list attached to a securable object.
- <b>ACE:</b> one permission entry inside an ACL.
- <b>Inheritance:</b> permissions passed from a parent object to child objects.
- <b>Effective access:</b> actual rights after group membership, allow/deny entries and inheritance are considered.

### Integrity levels

| Level | Typical context |
| --- | --- |
| System | Core Windows services and SYSTEM processes |
| High | Elevated administrator process |
| Medium | Standard user or non-elevated administrator process |
| Low | Restricted or sandboxed process |
| Untrusted | Highly restricted process |

### Privileges to recognise

| Privilege | Why it matters |
| --- | --- |
| <code>SeImpersonatePrivilege</code> | May allow impersonation of an authenticated client in specific conditions |
| <code>SeAssignPrimaryTokenPrivilege</code> | Allows assigning a primary token in defined contexts |
| <code>SeBackupPrivilege</code> | Supports backup-style file access |
| <code>SeRestorePrivilege</code> | Supports restore operations and certain ownership/permission changes |
| <code>SeDebugPrivilege</code> | Can allow debugging other processes, subject to protection boundaries |
| <code>SeTakeOwnershipPrivilege</code> | Allows taking ownership of securable objects |
| <code>SeLoadDriverPrivilege</code> | Allows loading drivers; highly sensitive |

<b>Important:</b> A privilege listed by <code>whoami /priv</code> is not automatically enabled or exploitable. Check the token, enabled state, process context and prerequisites.

## 5. First host-enumeration checks

Run these after authorised access. Record hostname, account, timestamp and relevant output.

### A. Confirm identity

| Command | What it tells you |
| --- | --- |
| <code>whoami /all</code> | Current identity, groups, privileges and integrity |
| <code>hostname</code> | Hostname |
| <code>echo %USERDOMAIN%</code> | Current logon domain |
| <code>echo %LOGONSERVER%</code> | Logon server used for the session |

```powershell
[PSCustomObject]@{
    Computer   = $env:COMPUTERNAME
    Identity   = [System.Security.Principal.WindowsIdentity]::GetCurrent().Name
    Domain     = $env:USERDOMAIN
    PowerShell = $PSVersionTable.PSVersion.ToString()
}
```

<b>What does it do?</b> Collects the computer name, current Windows identity, logon domain and PowerShell version in one structured object.

Shows host, current identity, domain and PowerShell version in one object.

### B. Identify OS and patch context

| Command | What it does |
| --- | --- |
| <code>systeminfo</code> | Shows OS, build, architecture and system details |
| <code>Get-CimInstance Win32_OperatingSystem</code> | Reads OS caption, version, build and boot time |
| <code>Get-HotFix</code> | Lists hotfix records exposed by Windows |

```powershell
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture, LastBootUpTime
```

<b>What does it do?</b> Reads the Windows operating-system caption, version, build, architecture and last boot time for patch-context review.

Shows OS version and build so you can check whether an advisory applies.

WMIC may be unavailable on current Windows versions; prefer CIM (<code>Get-CimInstance</code>).

### C. Check network and domain context

| Command | What it does |
| --- | --- |
| <code>ipconfig /all</code> | Shows IP, gateway, DNS and adapter configuration |
| <code>route print</code> | Shows routes available to the host |
| <code>arp -a</code> | Shows recently resolved local network neighbours |
| <code>netstat -ano</code> | Shows connections and owning process IDs |
| <code>nltest /dsgetdc:%USERDNSDOMAIN%</code> | Finds a domain controller (domain environment required) |
| <code>Get-NetIPConfiguration</code> | Shows adapter and IP configuration |
| <code>Get-NetTCPConnection</code> | Shows TCP connection state and endpoints |
| <code>Get-DnsClientServerAddress</code> | Shows configured DNS servers |

### D. Review processes and services

| Command | What it does |
| --- | --- |
| <code>tasklist /v</code> | Lists processes with detailed information |
| <code>tasklist /svc</code> | Shows services hosted by each process |
| <code>sc.exe qc Spooler</code> | Shows the Spooler service configuration |
| <code>Get-CimInstance Win32_Process</code> | Shows process ID, parent PID, path and command line |
| <code>Get-CimInstance Win32_Service</code> | Shows service state, startup mode, account and executable path |

```powershell
Get-CimInstance Win32_Process |
    Select-Object ProcessId, ParentProcessId, Name, ExecutablePath, CommandLine

Get-CimInstance Win32_Service |
    Select-Object Name, State, StartMode, StartName, PathName
```

<b>What does it do?</b> Lists processes and services with executable paths and command lines to support host and service enumeration.

Helps identify unusual processes, parent-child relationships and services running under privileged accounts.

## 6. Windows privilege-escalation checks

A finding needs a complete chain:

<b>Current user → weak permission/configuration → privileged process that uses it → valid trigger/prerequisites → impact.</b>

| Area | What to check |
| --- | --- |
| Service permissions | Can the current user change the service, executable, dependency or parent directory? |
| Unquoted service path | Is the path unquoted, does it contain spaces, and is a candidate directory writable? |
| Scheduled task | Who runs it, what does it execute, when does it run, and who can modify the task or target? |
| File/directory ACL | Can the user modify a file or parent directory used by a privileged process? |
| Registry permissions | Can the user change a value consumed by a privileged component? |
| AlwaysInstallElevated | Are both machine and user policy values enabled? |
| Token privileges | Is the privilege enabled and are its required conditions present? |
| DLL search order | Does a privileged application search a directory the user can modify? |
| PATH resolution | Does a privileged process search a user-writable directory for an executable? |
| Patch level | Does the exact OS build, architecture and component match a known vulnerability? |
| Credentials | Are secrets exposed through unsafe storage or permissions? Never extract or reuse real credentials. |

### A. Services

```shell
sc.exe query state= all
sc.exe qc Spooler
sc.exe sdshow Spooler
```

<b>What does it do?</b> Queries service state, configuration and security descriptor information for the selected service. Review permissions before making changes.

- <code>sc.exe query</code> — lists service state.
- <code>sc.exe qc</code> — shows service account, binary path and startup configuration.
- <code>sc.exe sdshow</code> — displays the service security descriptor (SDDL).

```powershell
Get-CimInstance Win32_Service |
    Select-Object Name, DisplayName, State, StartMode, StartName, PathName

$service = Get-CimInstance Win32_Service -Filter "Name='Spooler'"
$service | Select-Object Name, StartName, State, StartMode, PathName
Get-Acl -LiteralPath 'C:\Windows\System32\spoolsv.exe' |
    Format-List Owner, AccessToString
icacls 'C:\Windows\System32\spoolsv.exe'
```

<b>What does it do?</b> Lists services and inspects a selected service's configuration and security descriptor; it does not prove that a service is exploitable.

Shows service configuration and ACL on the example binary. Confirm the actual executable path before checking it.

### B. Unquoted service paths

```powershell
Get-CimInstance Win32_Service |
    Where-Object {
        $_.PathName -and
        $_.PathName -notmatch '^\s*"' -and
        $_.PathName -match '\s'
    } |
    Select-Object Name, StartName, StartMode, PathName
```

<b>What does it do?</b> Finds service paths that appear unquoted and contain spaces. A candidate becomes a finding only if path resolution and write permissions create a valid escalation chain.

Finds service paths that are unquoted and contain spaces; this is a lead, not proof of vulnerability.

### C. Scheduled tasks

```shell
schtasks /query /fo LIST /v
```

<b>What does it do?</b> Lists scheduled tasks in verbose form so you can review their triggers, actions, principals and run context.

Lists scheduled tasks with actions, triggers and run-as information.

```powershell
Get-ScheduledTask |
    Select-Object TaskPath, TaskName, State

Get-ScheduledTask | ForEach-Object {
    $task = $_
    [PSCustomObject]@{
        TaskPath  = $task.TaskPath
        TaskName  = $task.TaskName
        State     = $task.State
        Principal = $task.Principal.UserId
        RunLevel  = $task.Principal.RunLevel
        Actions   = ($task.Actions | ForEach-Object {
            "$($_.Execute) $($_.Arguments)"
        }) -join '; '
    }
} | Format-Table -Wrap
```

<b>What does it do?</b> Enumerates scheduled-task actions and related metadata for review; task state alone does not establish risk.

Shows task identity, run level and action. Check task permissions and the executable/parent-directory ACL separately.

### D. File and directory permissions

```shell
icacls C:\Lab
icacls C:\Lab\sample.exe
icacls "C:\Program Files\Example"
```

<b>What does it do?</b> Displays NTFS ACLs on selected lab paths. Check both the file and parent-directory permissions.

Displays NTFS permissions for each path.

```powershell
Get-Acl -LiteralPath 'C:\Lab' |
    Format-List Owner, AccessToString

(Get-Acl -LiteralPath 'C:\Lab').Access |
    Select-Object IdentityReference, FileSystemRights, AccessControlType,
                  IsInherited, InheritanceFlags, PropagationFlags
```

<b>What does it do?</b> Displays ownership and access-control entries for a directory so you can review effective permissions and inheritance.

Shows owner, allow/deny entries, rights and inheritance. Check every parent directory, not just the final file.

### E. Registry permissions and startup configuration

```shell
reg query "HKLM\SYSTEM\CurrentControlSet\Services"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

<b>What does it do?</b> Queries service and startup-related Registry locations for configuration and persistence review.

Reads service, startup and logon-related registry configuration.

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Services\Spooler'
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' -ErrorAction SilentlyContinue
Get-Acl 'HKLM:\SYSTEM\CurrentControlSet\Services\Spooler' |
    Format-List Owner, AccessToString
```

<b>What does it do?</b> Reads selected Registry values and ACLs. Access-denied results are meaningful and should not be bypassed during routine enumeration.

Reads example registry values and permissions. A value matters only if the user can change it and a privileged component consumes it.

### F. AlwaysInstallElevated

```shell
reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer" /v AlwaysInstallElevated
reg query "HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer" /v AlwaysInstallElevated
```

<b>What does it do?</b> Checks both machine and user AlwaysInstallElevated policy values. The escalation condition requires both values to be enabled.

Checks machine and current-user Windows Installer policy values.

```powershell
$machine = (Get-ItemProperty 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\Installer' -Name AlwaysInstallElevated -ErrorAction SilentlyContinue).AlwaysInstallElevated
$user = (Get-ItemProperty 'HKCU:\SOFTWARE\Policies\Microsoft\Windows\Installer' -Name AlwaysInstallElevated -ErrorAction SilentlyContinue).AlwaysInstallElevated

[PSCustomObject]@{
    MachinePolicy = $machine
    UserPolicy    = $user
    BothEnabled   = ($machine -eq 1 -and $user -eq 1)
}
```

<b>What does it do?</b> Reads the two AlwaysInstallElevated values and reports whether the risky configuration is present in both policy hives.

Reports whether both values are set to <code>1</code>. Do not install a payload to test this on a real system.

### G. Token privileges

```shell
whoami /priv
whoami /groups
whoami /all
```

<b>What does it do?</b> Displays the current token's privileges, groups and integrity information; a listed privilege is not automatically enabled or exploitable.

Shows token privileges, group SIDs and integrity information. Presence alone does not prove an escalation path.

### H. DLL and PATH review

```powershell
Get-Process -Name notepad -ErrorAction SilentlyContinue |
    Select-Object Name, Id, Path

Get-Process -Name notepad -ErrorAction SilentlyContinue |
    ForEach-Object { $_.Modules } |
    Select-Object ModuleName, FileName
```

<b>What does it do?</b> Inspects a process path and loaded modules where permissions allow. Access to another process's modules may require elevation and can fail for protected processes.

Shows the example process path and loaded modules; protected processes or architecture differences may restrict access.

```shell
echo %PATH%
```

<b>What does it do?</b> Displays the current process PATH environment variable so you can review executable search locations.

Shows the executable search path in the current CMD session.

```powershell
$env:Path -split ';' | ForEach-Object {
    [PSCustomObject]@{
        Directory = $_
        Exists    = Test-Path -LiteralPath $_
    }
}
```

<b>What does it do?</b> Enumerates PATH directories and their existence and permissions to identify writable search locations; validate the application's actual search order before reporting risk.

Lists PATH directories and checks whether each currently exists. Confirm whether the privileged application uses that search path.

### I. Patch and credential exposure review

```powershell
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture

Get-HotFix |
    Sort-Object InstalledOn -Descending |
    Select-Object -First 30 HotFixID, Description, InstalledOn
```

<b>What does it do?</b> Collects Windows build and hotfix information to support patch and vulnerability applicability checks.

Shows OS/build and recent hotfix records. Verify a suspected CVE against the exact component and vendor advisory.

```shell
cmdkey /list
```

<b>What does it do?</b> Lists saved Credential Manager targets. It does not reveal every stored password; treat target names and metadata as sensitive.

Lists saved credential target names. Do not dump LSASS, copy SAM/SECURITY/NTDS databases, extract browser passwords or reuse credentials from real systems.

## 7. Domain and remote-management context

| Technology | Purpose |
| --- | --- |
| Kerberos | Ticket-based Active Directory authentication |
| NTLM | Challenge-response authentication |
| LDAP | Directory queries |
| SMB | File and named-pipe sharing |
| RDP | Remote interactive desktop |
| WinRM | Windows remote-management transport used by PowerShell remoting |
| RPC | Framework used by Windows management services |

```shell
whoami /upn
whoami /groups
echo %USERDOMAIN%
echo %LOGONSERVER%
nltest /dsgetdc:%USERDNSDOMAIN%
net config workstation
net user /domain
net group /domain
```

<b>What does it do?</b> Displays domain identity, group membership and logon-server context for domain enumeration.

Shows domain identity, logon server, domain controller and domain account/group information. Domain commands require a suitable domain environment.

```powershell
Get-CimInstance Win32_ComputerSystem |
    Select-Object Name, Domain, PartOfDomain

Test-WSMan -ComputerName localhost
Get-SmbShare
Get-NetFirewallProfile
```

<b>What does it do?</b> Checks whether the computer is domain-joined and tests local WinRM responsiveness. A successful [localhost](http://localhost) test does not prove remote management is enabled across the network.

Shows domain membership, local WinRM response, SMB shares and firewall profiles. Connectivity does not grant remote execution permission.

## 8. Startup and persistence review

Use this section for inventory and detection, not creating covert persistence.

```powershell
Get-CimInstance Win32_Service |
    Select-Object Name, StartName, State, StartMode, PathName

Get-ScheduledTask |
    Select-Object TaskPath, TaskName, State

Get-LocalUser |
    Select-Object Name, Enabled, LastLogon, PasswordLastSet

Get-LocalGroupMember -Group 'Administrators'

Get-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' -ErrorAction SilentlyContinue
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' -ErrorAction SilentlyContinue
```

<b>What does it do?</b> Inventories services and scheduled tasks that can participate in startup or persistence; correlate entries with expected software and change records.

Inventories services, tasks, local users, local Administrators and common Run-key startup entries.

Sysinternals Autoruns provides a broader startup inventory. Verify publisher, signature, path, ACL and expected software before treating an entry as suspicious.

## 9. Windows logs and detection

### Useful event IDs

| Event ID | Meaning | Check |
| --- | --- | --- |
| 4624 | Successful logon | Account, logon type and source |
| 4625 | Failed logon | Target account, source and status |
| 4648 | Explicit credentials used | Process, account and source |
| 4672 | Special privileges assigned | Account and logon context |
| 4688 | Process creation | Parent process and command line (if configured) |
| 4697 / 7045 | Service installed | Service name, image path and account |
| 4698 | Scheduled task created | Creator, principal and action |
| 4720 | User created | Initiating account and new account |
| 4732 | Member added to local group | Group, member and initiator |
| 4719 | Audit policy changed | What logging configuration changed |
| 1102 | Security log cleared | Preserve central copies and investigate |
| 4103 | PowerShell module logging | Requires logging policy |
| 4104 | PowerShell script-block logging | Requires logging policy |

### Query recent events

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4624, 4625, 4672, 4688, 4697, 4698, 4720, 4732, 4719, 1102
} -MaxEvents 100 |
    Select-Object TimeCreated, Id, ProviderName, Message
```

<b>What does it do?</b> Queries selected Security events from a time-bounded or event-limited view. Required audit policies and permissions must be enabled for relevant events to exist.

Retrieves recent selected Security events for investigation.

```powershell
Get-WinEvent -LogName 'System' -MaxEvents 50 |
    Where-Object { $_.Id -in 7045, 7036 } |
    Select-Object TimeCreated, Id, Message
```

<b>What does it do?</b> Displays recent System log events associated with service installation or state changes.

Shows recent service-installation and service-state events.

```powershell
Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -MaxEvents 50 |
    Where-Object { $_.Id -in 4103, 4104 } |
    Select-Object TimeCreated, Id, Message
```

<b>What does it do?</b> Displays recent PowerShell module/script-block events when the corresponding logging policies are enabled.

Shows recent PowerShell module/script-block events when the channel and policies are enabled.

<b>Remember:</b> Event IDs are investigation clues, not proof. If logging is disabled, access is denied or events are missing, record that limitation. Do not clear logs during evidence collection.

## 10. Common mistakes

- Administrators group membership does not mean the process is elevated; check <code>whoami /all</code>.
- An unquoted service path is only a lead; verify path parsing, ACLs and service identity.
- A SYSTEM service is not automatically vulnerable; prove the current user can influence something it consumes.
- A writable folder matters only when a privileged process uses content from it.
- A listed token privilege may be disabled or lack required conditions.
- A scanner result is not proof; validate permissions and prerequisites manually.
- No event in a log does not prove no activity occurred; logging may be incomplete or disabled.
- PowerShell Execution Policy is not a security boundary.
- WMIC is deprecated and may be absent; prefer CIM.
- Use <code>Get-WinEvent</code> for event logs; <code>Get-Content</code> is for text files.

## 11. Reporting a finding

| Field | What to record |
| --- | --- |
| Title | Specific weakness and affected component |
| Asset | Hostname, OS version and build |
| Initial context | User, groups, integrity and privileges |
| Observation | Exact service, task, file or registry setting |
| Evidence | Command, timestamp, output and relevant ACL/log |
| Preconditions | Required access, trigger, version and configuration |
| Impact | Which security boundary could be affected |
| Limitations | What could not be verified |
| Remediation | Exact permission or configuration change |
| Retest | Evidence that the issue is fixed |

<b>Assessment rule:</b> Do not report privilege escalation just because a setting looks unusual. Show the effective permission, privileged consumer, required trigger and evidence.

<details>
<summary>MODULE 01 — WINDOWS FUNDAMENTALS</summary>


## 1. Learning objectives

By the end of this module, you should be able to:

- Explain the role of an operating system.
- Distinguish user mode from kernel mode.
- Describe the purpose of common Windows file systems.
- Navigate directories and inspect files using CMD and PowerShell.
- Identify hidden files and understand why defenders inspect them.

## 2. What is an operating system?

An <b>Operating System (OS)</b> manages the computer's hardware and provides services that applications need to run.

Examples of its responsibilities:

- <b>Process management:</b> starts, schedules and stops programs.
- <b>Memory management:</b> allocates memory to processes.
- <b>File management:</b> stores and retrieves files.
- <b>Device management:</b> communicates with hardware through drivers.
- <b>Security:</b> manages accounts, permissions and access to resources.

### Windows architecture — simplified

```txt
Applications
    |
Windows APIs and system libraries
    |
User mode
    |
System calls
    |
Kernel mode
    |
Hardware abstraction and device drivers
    |
Hardware
```

<b>What does it do?</b> Provides a quick reference for windows architecture simplified; use the surrounding explanation for context and prerequisites.

## 3. User mode vs kernel mode

| Area | User mode | Kernel mode |
| --- | --- | --- |
| Purpose | Runs ordinary applications and many user processes | Runs the Windows kernel and kernel-mode drivers |
| Privilege | Restricted access | Highly privileged access |
| Failure impact | Usually affects the application or process | Can affect the whole operating system |
| Security relevance | Malware can execute here without kernel access | Kernel-level compromise can undermine operating-system protections |

<b>Important:</b> Malware does not need kernel mode to be dangerous. Most malware runs in user mode. A kernel-mode rootkit is a more privileged and difficult-to-detect case.

<b>Red Team perspective:</b> Understand the privilege boundary and why kernel access represents a major escalation.

<b>Blue Team perspective:</b> Monitor suspicious processes, drivers and attempts to cross privilege boundaries.

## 4. Windows file systems

| File system | Common use | Security and technical notes |
| --- | --- | --- |
| NTFS | Windows system and data volumes | Supports Windows access-control lists (ACLs), file auditing, encryption features such as EFS, and alternate data streams |
| FAT32 | Removable media and compatibility | Does not provide NTFS-style file permissions; individual files are limited to 4 GB |
| exFAT | Flash drives and removable storage | Designed for removable media and large files; does not provide NTFS-style ACLs |
| ReFS | Supported Windows Server and specialised deployments | Designed for data integrity and resilience; feature and edition support varies |

<b>Security takeaway:</b> NTFS provides Windows file-level permission controls. FAT32 and exFAT do not provide the same NTFS ACL model. This does not mean every user automatically has access to every file; access also depends on the device, operating system and where the media is mounted.

## 5. CMD — file and directory navigation

### List files and folders

```shell
dir
dir /a
dir /a:h
dir /s /b
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### Change directories

```shell
cd C:\Windows\System32
cd ..
cd \
cd /d D:\
```

<b>What does it do?</b> Provides a quick reference for change directories; use the surrounding explanation for context and prerequisites.

### View a directory tree

```shell
tree C:\Users
tree C:\Users /f
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### Create, copy, move and remove test items

```shell
mkdir C:\Lab
echo Windows practice file > C:\Lab\sample.txt
copy C:\Lab\sample.txt C:\Lab\sample-copy.txt
move C:\Lab\sample-copy.txt C:\Lab\moved-sample.txt
del C:\Lab\moved-sample.txt
rmdir C:\Lab /s /q
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

## 6. PowerShell — file and directory navigation

### List and filter files

```powershell
Get-ChildItem
Get-ChildItem -Force
Get-ChildItem -File
Get-ChildItem -Filter "*.exe"
Get-ChildItem -Path C:\Windows -Force
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### Navigate directories

```powershell
Set-Location C:\Windows\System32
Set-Location ..
Get-Location
```

<b>What does it do?</b> Provides a quick reference for navigate directories; use the surrounding explanation for context and prerequisites.

### Useful aliases

| Alias | Full command |
| --- | --- |
| <code>ls</code> | <code>Get-ChildItem</code> |
| <code>cd</code> | <code>Set-Location</code> |
| <code>pwd</code> | <code>Get-Location</code> |

<b>Note:</b> Aliases are shortcuts. Use the full cmdlet name in scripts when clarity and portability matter.

## 7. Practical lab 1 — directory enumeration

<b>Objective:</b> Practise listing files and collecting basic file metadata without scanning the entire system.

### CMD

```shell
mkdir C:\Lab
dir C:\Windows\System32 /b
dir C:\Users /a:d /b
dir C:\Lab /a
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### PowerShell

```powershell
New-Item -Path C:\Lab -ItemType Directory -Force

Get-ChildItem -Path C:\Windows\System32 -Filter "*.exe" -File |
    Select-Object Name, Length, LastWriteTime |
    Sort-Object Name

Get-ChildItem -Path C:\Users -Directory |
    Select-Object Name, LastWriteTime
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

## 8. Practical lab 2 — hidden-file attributes

Windows file attributes can mark a file as <b>Hidden</b> or <b>System</b>. These attributes affect ordinary visibility; they do not encrypt the file or make it secure.

### Create a test file and mark it hidden

```shell
mkdir C:\Lab
echo Test data > C:\Lab\hidden-test.txt
attrib +H +S C:\Lab\hidden-test.txt
attrib C:\Lab\hidden-test.txt
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### Find hidden files with CMD

```shell
dir C:\Lab /a:h /s /b
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### Find hidden files with PowerShell

```powershell
Get-ChildItem -Path C:\Lab -Recurse -Force -ErrorAction SilentlyContinue |
    Where-Object { $_.Attributes -band [IO.FileAttributes]::Hidden } |
    Select-Object FullName, Attributes, LastWriteTime
```

<b>What does it do?</b> Creates, reads or enumerates NTFS alternate data streams. ADS can store data outside the default stream; investigate unexpected streams without assuming they are executable or malicious.

### Restore and clean up

```shell
attrib -H -S C:\Lab\hidden-test.txt
del C:\Lab\hidden-test.txt
rmdir C:\Lab
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

## 9. Windows editions — quick reference

| Edition | Typical environment | Examples of features (availability varies) |
| --- | --- | --- |
| Home | Personal devices | Core Windows security and Defender features |
| Pro | Power users and small businesses | BitLocker management, Group Policy and business-management features |
| Enterprise | Managed organisations | Additional deployment, application-control and credential-protection capabilities |
| Server | Server workloads | Server roles, domain services and advanced administration |

Feature availability can depend on the Windows release, licensing, hardware and policy configuration. Verify the exact edition before relying on a feature during an assessment.

## 10. Knowledge check

<b>Q1. What is the main difference between user mode and kernel mode?</b>

User mode restricts application access. Kernel mode has highly privileged access to core operating-system functions and hardware.

<b>Q2. Does all malware need kernel-mode access?</b>

No. Most malware can operate in user mode. Kernel-mode access represents a more privileged level of compromise.

<b>Q3. Which CMD command lists hidden files?</b>

<code>dir /a:h</code>

<b>Q4. Which PowerShell option includes hidden and system items?</b>

<code>Get-ChildItem -Force</code>

<b>Q5. Why is NTFS commonly used for Windows system volumes?</b>

It supports Windows ACLs and other features needed for permissions, auditing and file management.

## 11. Module summary

- The OS manages processes, memory, files, devices and security.
- User mode is restricted; kernel mode is highly privileged.
- NTFS supports Windows ACLs; FAT32 and exFAT do not provide the same permission model.
- Use <code>dir /a:h</code> or <code>Get-ChildItem -Force</code> to inspect hidden items.
- Hidden and System attributes do not protect or encrypt a file.
- Practise file operations inside a dedicated lab directory.

<b>Next:</b> Module 02 — Windows File System Deep Dive.
</details>

<details>
<summary>MODULE 02 — WINDOWS FILE SYSTEM DEEP DIVE</summary>


<b>Use cases:</b> Understand NTFS, inspect ownership and ACLs, assess effective file access and identify alternate data streams for forensic review.

> <b>Level:</b> Beginner–Intermediate \| <b>Track:</b> Red Team + Blue Team

---

## 🎯 Learning Objectives

- Understand NTFS architecture and the Master File Table
- Master file permissions (ACLs) and how they protect resources
- Discover Alternate Data Streams (ADS) — attacker's hidden storage
- Use icacls, attrib, fsutil for investigation

---

## 💾 NTFS Architecture

### Master File Table (MFT)

<p>
NTFS stores all file metadata in the <b>Master File Table</b> — every file and directory has an entry.  
</p>

<h3>
Real-World Analogy 
</h3>

<blockquote>
The MFT is like a <b>library card catalog</b>. Instead of storing books (data) in the catalog, it stores location cards (metadata). Each card holds: file name, size, timestamps, location on disk, permissions. 
</blockquote>

```javascript
NTFS Volume
├─ $MFT          ← Master File Table (all file metadata)
├─ $MFTMirr      ← MFT backup
├─ $LogFile       ← Transaction journal
├─ $Volume        ← Volume info
├─ $AttrDef       ← Attribute definitions
├─ $Bitmap        ← Cluster usage map
└─ $Secure        ← Security descriptors 
```

<b>What does it do?</b> Provides a visual reference for master file table mft, showing the main components or relationships.

### Security Context

> <b>Forensics:</b> MFT records can retain metadata about deleted files, but recovery depends on whether the record and file content have been reused or overwritten. An MFT entry alone does not guarantee content recovery.

> <b>Timeline analysis:</b> Interpret file timestamps carefully. Last-access updates may be disabled, delayed or altered; correlate MFT data with USN Journal, event logs, application artefacts and other evidence.

---

## 🔐 File Permissions (ACLs)

NTFS uses <b>Access Control Lists (ACLs)</b> to control who can do what with each file.

```javascript
File ACL Structure:
┌────────────────────────────────────────┐
│ Security Descriptor                      │
│   ├─ Owner: DOMAIN\john.doe              │
│   ├─ DACL (Discretionary ACL):           │
│   │   ├─ ACE: Administrators → Full     │
│   │   ├─ ACE: Users → Read+Execute      │
│   │   └─ ACE: john.doe → Modify          │
│   └─ SACL (System ACL - Auditing)        │
└────────────────────────────────────────┘
```

<b>What does it do?</b> Provides a visual reference for file permissions acls, showing the main components or relationships.

### NTFS Permission Levels

<table header-row="true">
<tr>
<td>
Permission
</td>
<td>
Read
</td>
<td>
Write
</td>
<td>
Execute
</td>
<td>
Delete
</td>
<td>
Change Perms
</td>
<td>
Take Ownership
</td>
</tr>
<tr>
<td>
<b>Full Control</b>
</td>
<td>
✅
</td>
<td>
✅
</td>
<td>
✅
</td>
<td>
✅
</td>
<td>
✅
</td>
<td>
✅
</td>
</tr>
<tr>
<td>
<b>Modify</b>
</td>
<td>
✅
</td>
<td>
✅
</td>
<td>
✅
</td>
<td>
✅
</td>
<td>
❌
</td>
<td>
❌
</td>
</tr>
<tr>
<td>
<b>Read & Execute</b>
</td>
<td>
✅
</td>
<td>
❌
</td>
<td>
✅
</td>
<td>
❌
</td>
<td>
❌
</td>
<td>
❌
</td>
</tr>
<tr>
<td>
<b>Read</b>
</td>
<td>
✅
</td>
<td>
❌
</td>
<td>
❌
</td>
<td>
❌
</td>
<td>
❌
</td>
<td>
❌
</td>
</tr>
<tr>
<td>
<b>Write</b>
</td>
<td>
❌
</td>
<td>
✅
</td>
<td>
❌ 
</td>
<td>
❌
</td>
<td>
❌
</td>
<td>
❌
</td>
</tr>
</table>

---

## 💻 icacls — Manage File Permissions

```shell
REM View permissions
icacls C:\SensitiveFile.txt

REM Grant permissions
icacls C:\file.txt /grant "john.doe:(R)"      REM Read only
icacls C:\file.txt /grant "Domain Admins:(F)" REM Full control 

REM Deny permissions
icacls C:\file.txt /deny "lowprivuser:(W)"    REM Deny write

REM Remove permissions
icacls C:\file.txt /remove john.doe

REM Reset to inherited
icacls C:\file.txt /reset

REM Recursive
icacls C:\FolderName /grant "john.doe:(OI)(CI)(F)"
REM OI = Object Inherit, CI = Container Inherit
```

<b>What does it do?</b> Inspects or changes NTFS access-control entries. Review the target path and principal carefully; test permission changes on a lab folder and restore them afterward.

```powershell
# Get ACL
Get-Acl C:\SensitiveFile.txt | Format-List

# Check specific user permissions
$acl = Get-Acl C:\file.txt
$acl.Access | Where-Object {$_.IdentityReference -like "*john*"}

# Grant permission
$acl = Get-Acl C:\folder
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
  "john.doe", "ReadAndExecute", "Allow")
$acl.AddAccessRule($rule)
Set-Acl C:\folder $acl
```

<b>What does it do?</b> Inspects or changes NTFS access-control entries. Review the target path and principal carefully; test permission changes on a lab folder and restore them afterward.

---

## 🕵️ Alternate Data Streams (ADS)

<h3>
What is ADS? 
</h3>

NTFS allows files to have <b>multiple data streams</b>. The default stream holds the file content. Additional named streams are invisible to normal <code>dir</code> commands and Windows Explorer.

### Real-World Analogy

> ADS is like a <b>secret compartment in a briefcase</b>. The briefcase looks normal. Inside is regular paperwork. But in the secret compartment: stolen data, tools, malware.

### Red Team: Hide Data in ADS

```shell
REM Write hidden data to ADS
echo "Hidden malware content" > legitimate.txt:hidden_stream

REM Write malware to ADS
type C:\Tools\malware.exe > C:\Windows\notepad.exe:evil.exe

REM ADS stores data but is not a normal standalone executable path.
REM Do not assume a stream can be launched directly with CreateProcess.
REM For analysis, enumerate the stream, extract it to a controlled lab file,
REM then inspect the extracted file safely without executing it.
```

<b>What does it do?</b> Creates, reads or enumerates NTFS alternate data streams. ADS can store data outside the default stream; investigate unexpected streams without assuming they are executable or malicious.

```powershell
# Write to ADS using PowerShell
"Secret data" | Set-Content -Path C:\file.txt -Stream hidden_data

# Read ADS content
Get-Content -Path C:\file.txt -Stream hidden_data
```

<b>What does it do?</b> Creates, reads or enumerates NTFS alternate data streams. ADS can store data outside the default stream; investigate unexpected streams without assuming they are executable or malicious.

### Blue Team: Detect ADS

```shell
REM List ALL streams on a file
dir /R C:\suspicious_file.txt
dir /R C:\Folder\*

REM List streams with more detail
streams.exe -s C:\  (Sysinternals Streams tool)
```

<b>What does it do?</b> Creates, reads or enumerates NTFS alternate data streams. ADS can store data outside the default stream; investigate unexpected streams without assuming they are executable or malicious.

```powershell
# Detect ADS on all files in a directory
Get-ChildItem C:\ -Recurse -Force -ErrorAction SilentlyContinue |
  ForEach-Object {
    $streams = Get-Item $_.FullName -Stream * -ErrorAction SilentlyContinue
    $streams | Where-Object {$_.Stream -ne ':$DATA'} |
    Select-Object @{N='File';E={$_.FileName}},
                  @{N='Stream';E={$_.Stream}},
                  @{N='Size';E={$_.Length}}
  } | Where-Object {$_ -ne $null} | Format-Table
```

<b>What does it do?</b> Creates, reads or enumerates NTFS alternate data streams. ADS can store data outside the default stream; investigate unexpected streams without assuming they are executable or malicious.

---

## 🧪 Lab 1 — File Permissions

```shell
REM Create test structure
mkdir C:\PermTest
echo "Sensitive Data" > C:\PermTest\secret.txt

REM Check current permissions
icacls C:\PermTest\secret.txt

REM Remove all inherited permissions and set explicit
icacls C:\PermTest /inheritance:d
icacls C:\PermTest\secret.txt /remove "Authenticated Users"
icacls C:\PermTest\secret.txt /grant "Administrators:(F)"

REM Verify
icacls C:\PermTest\secret.txt
```

<b>What does it do?</b> Inspects or changes NTFS access-control entries. Review the target path and principal carefully; test permission changes on a lab folder and restore them afterward.

---

## 🧪 Lab 2 — ADS Attack & Detection

### Red Team Simulation

```shell
REM Create innocent-looking file
echo "This is a text file" > C:\Temp\readme.txt

REM Hide secret payload in ADS
echo "This is hidden malware" > C:\Temp\readme.txt:payload

REM Verify normal dir doesn't show it
dir C:\Temp\readme.txt
REM Shows: 21 bytes (only main stream!)

REM Show the hidden stream
dir /R C:\Temp\readme.txt
REM Shows: :payload:$DATA  25 bytes (FOUND!)
```

<b>What does it do?</b> Creates, reads or enumerates NTFS alternate data streams. ADS can store data outside the default stream; investigate unexpected streams without assuming they are executable or malicious.

### Blue Team Detection Script

```powershell
# Comprehensive ADS scanner
function Find-AlternateDataStreams {
  param([string]$Path = "C:\")

  Write-Host "Scanning for Alternate Data Streams in $Path..." -ForegroundColor Yellow

  Get-ChildItem -Path $Path -Recurse -Force -ErrorAction SilentlyContinue |
    ForEach-Object {
      try {
        $streams = Get-Item $_.FullName -Stream * -ErrorAction Stop
        $streams | Where-Object {$_.Stream -ne ':$DATA'} |
        ForEach-Object {
          [PSCustomObject]@{
            FilePath = $_.FileName
            HiddenStream = $_.Stream
            StreamSize = $_.Length
          }
        }
      } catch {}
    }
}

Find-AlternateDataStreams -Path "C:\Temp" | Format-Table
```

<b>What does it do?</b> Creates, reads or enumerates NTFS alternate data streams. ADS can store data outside the default stream; investigate unexpected streams without assuming they are executable or malicious.

---

## ❓ Module 02 Quiz

<b>Q1:</b> What is the Master File Table?

> The MFT is an NTFS database containing metadata (name, size, timestamps, location, permissions) for every file and directory on the volume.

<b>Q2:</b> How would an attacker use ADS to hide malware?

> An attacker can write an executable into an ADS of an innocent file: <code>type malware.exe > readme.txt:hidden</code>. The file appears normal with regular <code>dir</code> but contains the payload in the hidden stream.

<b>Q3:</b> What icacls flag denies write permissions?

> <code>icacls file.txt /deny username:(W)</code>

---

## ✅ Module 02 Summary

- NTFS MFT stores all file metadata — critical for forensics
- ACLs control file access — misconfigured permissions are a common attack vector
- ADS allows hidden data in additional file streams — invisible to basic tools
- <code>dir /R</code> and <code>Get-Item -Stream *</code> reveal ADS
- Always audit file permissions on sensitive directories

> ➡️ <b>Next Module:</b> Processes, Services & Memory
</details>

<details>
<summary>MODULE 03 — PROCESSES, SERVICES & MEMORY</summary>


<b>Use cases:</b> Inventory running processes and services, trace parent-child relationships, inspect service configuration and recognise suspicious process behaviour.

> <b>Level:</b> Beginner–Intermediate \| <b>Track:</b> Red Team + Blue Team

---

## 🧠 What Is a Process?

A <b>process</b> is a running instance of a program. Every application you open creates at least one process.

### Real-World Analogy

> A process is like a <b>chef cooking a meal</b>. The recipe is the program (stored on disk). The chef actively cooking is the process (running in memory). Multiple chefs can follow the same recipe simultaneously — multiple processes of the same program.

### Process Anatomy

```javascript
Process
├─ PID (Process ID)       ← Unique number
├─ PPID (Parent PID)      ← Who created this process
├─ Threads               ← Execution units within process
├─ Virtual Memory        ← Private address space
├─ Handles               ← Open files, registry, network
├─ Security Token        ← Privileges and identity
└─ DLLs                  ← Libraries loaded into memory
```

<b>What does it do?</b> Provides a visual reference for process anatomy, showing the main components or relationships.

---

## 💻 Process Commands

```shell
REM List all running processes
tasklist
tasklist /FI "STATUS eq RUNNING"
tasklist /SVC           REM Show services per process
tasklist /M             REM Show loaded DLLs
tasklist /FI "IMAGENAME eq malware.exe"   REM Find specific

REM Kill a process
taskkill /PID 1234
taskkill /IM chrome.exe /F     REM Force kill by name
taskkill /F /T /IM malware.exe REM Kill process tree
```

<b>What does it do?</b> Enumerates processes and demonstrates stopping a selected process. Force-termination can interrupt work; use a disposable test process.

```powershell
# List processes
Get-Process
Get-Process | Sort-Object CPU -Descending | Select -First 10
Get-Process -Name "chrome"
Get-Process | Where-Object {$_.CPU -gt 50}  # CPU is cumulative seconds, not percentage

# Get process with full details
Get-Process | Select-Object Name, Id, CPU, WorkingSet, Path

# Kill process
Stop-Process -Name "malware" -Force
Stop-Process -Id 1234 -Force

# Get parent-child relationships
Get-CimInstance Win32_Process | Select-Object ProcessId, ParentProcessId, Name
```

<b>What does it do?</b> Enumerates processes and demonstrates stopping a selected process. Force-termination can interrupt work; use a disposable test process.

---

## ⚙️ Windows Services

A <b>service</b> is a process that runs in the background, typically without user interaction, usually starting at boot.

### Key Service Commands

```shell
REM List services
sc query                      REM Running services
sc query type= all            REM All services
sc qc ServiceName             REM Configuration

REM Control services
sc start ServiceName
sc stop ServiceName
sc delete ServiceName

REM Query via tasklist
tasklist /SVC
```

<b>What does it do?</b> Inspects or changes Windows service configuration and state. Start, stop, create and delete operations modify the host; use a clearly identified lab service.

```powershell
# Service management
Get-Service
Get-Service | Where-Object {$_.Status -eq 'Running'}
Get-Service -Name "wuauserv"   # Windows Update

Start-Service -Name "ServiceName"
Stop-Service -Name "ServiceName" 

# Find services with suspicious paths
Get-CimInstance Win32_Service |
  Select Name, PathName, StartMode, State |
  Where-Object {$_.PathName -like "*Temp*" -or $_.PathName -like "*AppData*"}
```

<b>What does it do?</b> Inspects or changes Windows service configuration and state. Start, stop, create and delete operations modify the host; use a clearly identified lab service.

---

## 🔍 Security: Malware Process Detection

### Red Team: Process Injection Concept

Attackers inject malicious code into legitimate processes (svchost.exe, explorer.exe) so that malware runs under a trusted process name.

### Blue Team: Suspicious Process Indicators

```powershell
# Find processes with no parent or suspicious parents
$processes = Get-CimInstance Win32_Process |
  Select ProcessId, ParentProcessId, Name, ExecutablePath, CommandLine

# Show parent-child tree
$processes | ForEach-Object {
  $parent = $processes | Where-Object {$_.ProcessId -eq $_.ParentProcessId}
  [PSCustomObject]@{
    PID = $_.ProcessId
    Name = $_.Name
    ParentPID = $_.ParentProcessId
    ParentName = ($processes | Where-Object {$_.ProcessId -eq $_.ParentProcessId}).Name
    Path = $_.ExecutablePath
  }
} | Format-Table
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

### Suspicious Process Indicators

```javascript
Red Flags to Investigate:
✔ svchost.exe NOT in C:\Windows\System32
✔ Unexpected parent process or parent PID that cannot be resolved (investigate context; PID 0 is a valid parent for the System process)
✔ Known process name but different path
✔ Very high CPU with no clear purpose
✔ Process making outbound network connections
✔ Process with no description or company name 
```

<b>What does it do?</b> Provides a quick reference for suspicious process indicators; use the surrounding explanation for context and prerequisites.

---

## 🧪 Lab 1 — Monitor Suspicious Processes

```powershell
# Comprehensive suspicious process checker
function Get-SuspiciousProcesses {
    Write-Host "=== Suspicious Process Analysis ==" -ForegroundColor Red

    # Processes in non-standard locations
    Write-Host "`n[!] Processes in suspicious locations:" -ForegroundColor Yellow
    Get-CimInstance Win32_Process | Where-Object {
        $_.ExecutablePath -and (
        $_.ExecutablePath -like "*\Temp\*" -or
        $_.ExecutablePath -like "*\AppData\*" -or
        $_.ExecutablePath -like "*\Downloads\*" -or
        $_.ExecutablePath -like "*\Public\*")
    } | Select Name, ProcessId, ExecutablePath | Format-Table 

    # Processes whose executable path could not be read.
    # This can be caused by permissions or protected processes; it is not proof of injection.
    Write-Host "`n[?] Processes with no accessible executable path:" -ForegroundColor Yellow
    Get-CimInstance Win32_Process | Where-Object {
        !$_.ExecutablePath -and $_.Name -ne "System" -and $_.Name -ne "Idle"
    } | Select Name, ProcessId, CommandLine | Format-Table 
}

Get-SuspiciousProcesses
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

---

## 🧪 Lab 2 — Service Security Audit

```powershell
# Audit all services for security issues
function Invoke-ServiceSecurityAudit {
    Write-Host "=== Service Security Audit ==" -ForegroundColor Cyan 

    # Services running as SYSTEM with non-standard paths
    Write-Host "`n[!] High-privilege services with suspicious paths:" -ForegroundColor Yellow
    Get-CimInstance Win32_Service | Where-Object {
        ($_.StartName -like "*SYSTEM*" -or $_.StartName -like "*LocalSystem*") -and
        ($_.PathName -like "*Temp*" -or $_.PathName -like "*AppData*" -or
         $_.PathName -like "*Users\*")
    } | Select Name, PathName, StartName, State | Format-Table -AutoSize

    # Services set to auto-start
    Write-Host "`n[+] Auto-start services (potential persistence):" -ForegroundColor Yellow
    Get-CimInstance Win32_Service | Where-Object {
        $_.StartMode -eq "Auto" -and
        $_.PathName -notlike "*System32*" -and
        $_.PathName -notlike "*SysWOW64*"
    } | Select Name, PathName, StartName | Format-Table -AutoSize
}

Invoke-ServiceSecurityAudit
```

<b>What does it do?</b> Lists services and inspects their state, startup mode, executable path and run-as account.

---

## ❓ Module 03 Quiz

<b>Q1:</b> What is the difference between a process and a service?

> A process is any running program instance. A service is a special type of process that runs in the background, typically without user interaction, often starting automatically at boot.

<b>Q2:</b> How does process injection help attackers?

> Process injection inserts malicious code into a legitimate running process (like svchost.exe). This hides the malware under a trusted process name, evading process-name-based detection.

<b>Q3:</b> What command shows which DLLs are loaded by each process?

> <code>tasklist /M</code> (CMD) or <code>Get-Process | Select-Object Modules</code> (PowerShell)

---

## ✅ Module 03 Summary

- Every running program is a process with a unique PID and parent PPID
- Services are background processes that run without user interaction
- Process parent-child relationships reveal attack chains
- Malware often hides in legitimate process names or injects into them
- Always investigate processes in non-standard locations (Temp, AppData)

> ➡️ <b>Next Module:</b> Windows Registry
</details>

<details>
<summary>MODULE 04 — WINDOWS REGISTRY</summary>


<b>Use cases:</b> Query registry hives and values, understand application configuration, inspect startup locations and investigate registry-based persistence indicators.

> <b>Level:</b> Intermediate \| <b>Track:</b> Red Team + Blue Team

> ⚠️ <b>WARNING:</b> Incorrect registry edits can break Windows. Always backup the registry before editing: <code>reg export HKLM C:\registry_backup.reg</code>

---

## 🎯 What Is the Registry?

The Windows Registry is a hierarchical database that stores configuration settings for Windows, hardware, software, and users.

### Real-World Analogy

> The Registry is like a <b>hospital's master configuration database</b>. It stores: how every department operates, which doctors are authorized, what medications are available, start-up procedures. Change the wrong setting and the hospital malfunctions.

---

## 🏗️ Registry Structure

```javascript
REGISTRY ROOT
├─ HKEY_LOCAL_MACHINE (HKLM)
│   ├─ SOFTWARE         ← Installed software config
│   ├─ SYSTEM           ← Device drivers, services
│   ├─ SECURITY         ← Protected local security policy and LSA secrets
│   └─ SAM              ← Protected local account database; stores password hashes
├─ HKEY_CURRENT_USER (HKCU)
│   ├─ SOFTWARE         ← Per-user application settings
│   └─ Environment      ← User environment variables
├─ HKEY_USERS (HKU)
│   └─ (All user profiles)
├─ HKEY_CLASSES_ROOT (HKCR)
│   └─ File type associations
└─ HKEY_CURRENT_CONFIG (HKCC)
    └─ Current hardware profile
```

<b>What does it do?</b> Provides a visual reference for registry structure, showing the main components or relationships.

### Security Significance

| Key | What Attackers Want |
| --- | --- |
| <code>HKLM\SAM</code> | Protected local account database containing password hashes; access is restricted |
| <code>HKLM\SYSTEM\CurrentControlSet\Services</code> | Install malicious services |
| <code>HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run</code> | User-level persistence |
| <code>HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run</code> | System-wide persistence |

---

## 💻 Registry Commands

```shell
REM Query registry
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
reg query HKLM /s /f "password"  REM Search for "password" everywhere!

REM Add registry key
reg add "HKCU\SOFTWARE\TestKey" /v TestValue /t REG_SZ /d "TestData"

REM Add persistence (malware technique)
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" ^  
  /v "UpdateService" /t REG_SZ /d "C:\Temp\malware.exe" /f

REM Delete key
reg delete "HKCU\SOFTWARE\TestKey" /f

REM Export / Import (backup)
reg export HKLM C:\full_backup.reg
reg import C:\registry_restore.reg
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

```powershell
# Query registry
Get-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
Get-Item "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion"

# Get all values in a key
Get-ChildItem "HKLM:\SYSTEM\CurrentControlSet\Services\" |
  ForEach-Object {
      Get-ItemProperty -LiteralPath $_.PSPath
  } | Select-Object PSChildName, ImagePath

# Add persistence (lab only!)
Set-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" `
  -Name "LegitService" -Value "C:\Temp\payload.exe"

# Detect: Find all Run key entries
Get-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run",
                        "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" |
  Select * -ExcludeProperty PS*
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## 🔒 Security: Persistence Techniques

### Run Keys (Most Common Persistence)

```javascript
Persistence Run Key Locations:

[SYSTEM-WIDE - requires admin]
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run

[USER-LEVEL - no admin needed]
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce

[LESS COMMON]
HKLM\SYSTEM\CurrentControlSet\Services  (services)
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
  (Userinit, Shell values)
```

<b>What does it do?</b> Provides a quick reference for run keys most common persistence; use the surrounding explanation for context and prerequisites.

### Winlogon Hijack (Advanced Persistence)

```shell
REM Legitimate:
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
REM Should show: Userinit = C:\Windows\system32\userinit.exe,
REM Should show: Shell = explorer.exe

REM Malicious modification:
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" ^
  /v Userinit /t REG_SZ ^
  /d "C:\Windows\system32\userinit.exe,C:\Temp\backdoor.exe" /f
REM Now backdoor runs at every login!
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## 🧪 Lab 1 — Persistence via Run Keys

```shell
REM Red Team Lab (Lab VM ONLY)
REM Step 1: Create fake payload
echo @echo off > C:\Temp\fake_update.bat
echo echo Running update... >> C:\Temp\fake_update.bat
echo ping 127.0.0.1 -n 5 >> C:\Temp\fake_update.bat

REM Step 2: Add to Run key for persistence
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "WindowsUpdate" /t REG_SZ /d "C:\Temp\fake_update.bat" /f

REM Step 3: Verify
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"

REM Cleanup!
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "WindowsUpdate" /f
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## 🧪 Lab 2 — Blue Team: Detect Registry Persistence

```powershell
# Comprehensive persistence detector
function Find-RegistryPersistence {
    Write-Host "=== Registry Persistence Hunter ==" -ForegroundColor Red

    $runKeys = @(
        "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run",
        "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce",
        "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run",
        "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce",
        "HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run"
    )

    foreach ($key in $runKeys) {
        Write-Host "`n[+] Checking: $key" -ForegroundColor Yellow
        try {
            $props = Get-ItemProperty -Path $key -ErrorAction Stop
            $props.PSObject.Properties |
              Where-Object {$_.Name -notlike "PS*"} |
              ForEach-Object {
                Write-Host "  Name: $($_.Name)" -ForegroundColor White
                Write-Host "  Path: $($_.Value)" -ForegroundColor Cyan

                # Check if the file exists
                $exePath = $_.Value -replace '"',''
                if (Test-Path $exePath) {
                  $sig = Get-AuthenticodeSignature $exePath -ErrorAction SilentlyContinue
                  Write-Host "  Signed: $($sig.Status)" -ForegroundColor $(if($sig.Status -eq 'Valid'){'Green'}else{'Red'})
                } else {
                  Write-Host "  FILE NOT FOUND - Suspicious!" -ForegroundColor Red
                }
              }
        } catch {
            Write-Host "  (Key not found)" -ForegroundColor Gray
        }
    }
}

Find-RegistryPersistence
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## ❓ Module 04 Quiz

<b>Q1:</b> What registry key contains user-level startup persistence that requires no admin rights?

> <code>HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run</code>

<b>Q2:</b> What sensitive data is stored in HKLMSAM?

> Windows user account password hashes.

<b>Q3:</b> How would you search for the word "password" across the entire registry?

> <code>reg query HKLM /s /f "password"</code> and <code>reg query HKCU /s /f "password"</code>

---

## ✅ Module 04 Summary

- Registry is Windows' central configuration database
- Run keys are the most common persistence mechanism used by malware
- HKLM requires admin; HKCU doesn't — attackers prefer HKCU for low-privilege persistence
- Always monitor Run, RunOnce, Winlogon, and Services registry locations
- <code>reg export</code> before any changes; <code>Get-ItemProperty</code> for PowerShell inspection

> ➡️ <b>Next Module:</b> Users, Groups & Privileges
</details>

<details>
<summary>MODULE 05 — USERS, GROUPS & PRIVILEGES</summary>


<b>Use cases:</b> Audit local accounts and groups, identify administrative context, understand UAC and inspect assigned user rights.

> <b>Level:</b> Intermediate \| <b>Track:</b> Red Team + Blue Team

---

## 👤 User Account Types

| Account Type | Privileges | Security Risk |
| --- | --- | --- |
| <b>Administrator</b> | Full system control | Highest — protect strictly |
| <b>Standard User</b> | Limited, no system changes | Lower risk |
| <b>Guest</b> | Built-in guest identity with restricted access | Keep disabled unless a documented requirement exists |
| <b>SYSTEM</b> | Built-in LocalSystem service identity with extensive local privileges | Frequently targeted; not universally more powerful for every resource |
| <b>Service Accounts</b> | Specific service rights | Often over-privileged |

---

## 💻 User Management Commands

```shell
REM List users
net user 
net user username          REM Detailed info

REM Create user
net user newuser * /add   REM Prompts for a password instead of exposing it in command history

REM Delete user
net user newuser /delete

REM Change password (prompts securely)
net user username *

REM Disable/Enable account
net user username /active:no
net user username /active:yes

REM List groups
net localgroup
net localgroup Administrators

REM Add to admins 
net localgroup Administrators username /add

REM Who am I?
whoami
whoami /all           REM Full privileges
whoami /groups        REM My groups
whoami /priv          REM My privileges
```

<b>What does it do?</b> Audits local identities and demonstrates account or group changes. Creating, deleting or adding an administrator is a privileged change; use a lab account and remove it during cleanup.

```powershell
# User management
Get-LocalUser
Get-LocalUser | Select Name, Enabled, LastLogon, PasswordExpires

$securePassword = Read-Host "Enter a lab password" -AsSecureString
New-LocalUser -Name "testuser" -Password $securePassword
Remove-LocalUser -Name "testuser"

Get-LocalGroup
Get-LocalGroupMember -Group "Administrators"

Add-LocalGroupMember -Group "Administrators" -Member "testuser"
```

<b>What does it do?</b> Audits local identities and demonstrates account or group changes. Creating, deleting or adding an administrator is a privileged change; use a lab account and remove it during cleanup.

---

## 🔐 User Account Control (UAC)

UAC is Windows' security mechanism that requires explicit approval for administrative actions.

```javascript
UAC Prompt Flow:
[Standard User requests admin action]
          ↓
  [UAC prompt appears]
          ↓
  [Admin approves?]
     YES ↓        NO ↓
  [Action runs]  [Access Denied]
```

<b>What does it do?</b> Provides a visual reference for user account control uac, showing the main components or relationships.

### UAC Bypass (Red Team concept — for awareness)

Attackers use techniques like:

- fodhelper.exe registry hijack
- eventvwr.exe COM hijack
- DLL hijacking of auto-elevated processes

### Blue Team UAC Hardening

```shell
REM Check UAC level
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v ConsentPromptBehaviorAdmin
REM ConsentPromptBehaviorAdmin values:
REM 0 = Elevate without prompting (least secure)
REM 1 = Prompt for credentials on the secure desktop
REM 2 = Prompt for consent on the secure desktop
REM 3 = Prompt for credentials
REM 4 = Prompt for consent
REM 5 = Prompt for consent for non-Windows binaries (default)
REM EnableLUA=0 disables UAC; do not use on a normal workstation.
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

<h2>
🔑 Windows Privileges 
</h2>

| Privilege | What It Allows | Attacker Value |
| --- | --- | --- |
| <code>SeDebugPrivilege</code> | Request debugging access to processes, subject to protection boundaries | May be relevant to process-memory access; LSASS protections can still prevent access |
| <code>SeImpersonatePrivilege</code> | Impersonate tokens | Token theft attacks |
| <code>SeTakeOwnershipPrivilege</code> | Take ownership of an object | May enable a subsequent permission change; ownership alone does not grant unrestricted file access |
| <code>SeLoadDriverPrivilege</code> | Load device drivers, subject to Windows signing and policy controls | Potential kernel-level impact; privilege alone does not bypass driver security controls |
| <code>SeBackupPrivilege</code> | Read files using backup semantics when the privilege is enabled and correctly used | Can expose protected data in backup operations; access is not automatic |
| <code>SeRestorePrivilege</code> | Write or restore files using restore semantics when the privilege is enabled and correctly used | Can affect protected files during restore operations; access is not automatic |

---

## 🧪 Lab 1 — User and Privilege Audit

```powershell
# Security-focused user audit
function Invoke-UserSecurityAudit {
    Write-Host "=== User Security Audit ==" -ForegroundColor Cyan

    # Administrators group members
    Write-Host "`n[+] Administrators Group Members:" -ForegroundColor Yellow
    Get-LocalGroupMember -Group "Administrators" |
      Select Name, ObjectClass, PrincipalSource | Format-Table

    # Enabled accounts
    Write-Host "`n[+] All enabled user accounts:" -ForegroundColor Yellow
    Get-LocalUser | Where-Object {$_.Enabled -eq $true} |
      Select Name, Enabled, LastLogon, PasswordExpires, Description | Format-Table

    # Enabled accounts with no recorded last logon — review, do not assume compromise.
    Write-Host "`n[?] Enabled accounts with no recorded last logon:" -ForegroundColor Yellow
    Get-LocalUser | Where-Object {
        $_.Enabled -eq $true -and $_.LastLogon -eq $null
    } | Select Name, Enabled, Description | Format-Table

    # Current privileges
    Write-Host "`n[+] Current user privileges:" -ForegroundColor Yellow
    whoami /priv  
}

Invoke-UserSecurityAudit
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

---

## 🧪 Lab 2 — Create and Audit a Test Local Administrator (Lab VM Only)

```shell
REM Create a clearly named lab account; Windows accounts are not hidden by this comment.
net user lab_audit_test * /add
net user lab_audit_test /comment:"Temporary authorised lab account"
net localgroup Administrators lab_audit_test /add
REM Remove the test account and group membership after the exercise:
REM net localgroup Administrators lab_audit_test /delete
REM net user lab_audit_test /delete

REM Blue Team: Detect it
net localgroup Administrators
net user | findstr svc 
```

<b>What does it do?</b> Audits local identities and demonstrates account or group changes. Creating, deleting or adding an administrator is a privileged change; use a lab account and remove it during cleanup.

```powershell
# Blue Team: Find recently created accounts
Get-LocalUser | Where-Object {
    $_.Enabled -eq $true
} | Select Name, Enabled, Description,
    @{N='PasswordAge';E={(Get-Date)-$_.PasswordLastSet}} |
  Where-Object {$_.PasswordAge.Days -lt 7} |
  Format-Table
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

---

## ❓ Module 05 Quiz

<b>Q1:</b> Which privilege can be relevant to process-memory access, and why does it not guarantee LSASS dumping?

<blockquote>
<code>SeDebugPrivilege</code> can permit access to many processes, but LSASS may be protected by Protected Process Light and Credential Guard. The privilege alone does not guarantee that LSASS memory can be read or dumped. 
</blockquote>

<b>Q2:</b> Why is SeImpersonatePrivilege dangerous?

> It allows a process to impersonate another user's security token, enabling privilege escalation attacks like token impersonation (Potato attacks).

<b>Q3:</b> What command lists all your current privileges?

> <code>whoami /priv</code>

---

## ✅ Module 05 Summary

- LocalSystem has extensive local privileges, but its effective access depends on the resource and security boundary.
- SeDebugPrivilege and SeImpersonatePrivilege are sensitive privileges; neither is automatically exploitable.
- UAC reduces unintended elevation but is not a complete security boundary.
- Audit membership of local Administrators and other privileged groups.
- An account with no recorded logon is not automatically malicious; investigate its owner, purpose, status and creation history.

> ➡️ <b>Next Module:</b> Networking Fundamentals in Windows
</details>

<details>
<summary>MODULE 06 — NETWORKING FUNDAMENTALS IN WINDOWS</summary>


<b>Use cases:</b> Inspect IP configuration, DNS, routes, ARP, listening ports and active connections during host enumeration.

> <b>Level:</b> Intermediate \| <b>Track:</b> Red Team + Blue Team

---

## 🌐 Core Networking Concepts

| Concept | What It Is | Security Relevance |
| --- | --- | --- |
| <b>IP Address</b> | Unique network identifier | Source of attacks |
| <b>MAC Address</b> | Hardware identifier | Network forensics |
| <b>DNS</b> | Domain to IP translation | C2 domains, DNS poisoning |
| <b>DHCP</b> | Automatic IP assignment | Rogue DHCP attacks |
| <b>Port</b> | Service endpoint number | Open ports = attack surface |

---

## 💻 Essential Network Commands

```shell
REM IP configuration
ipconfig /all                    REM Full details
ipconfig /release                REM Release DHCP
ipconfig /renew                  REM Renew DHCP
ipconfig /flushdns               REM Flush DNS cache

REM Connectivity tests
ping 8.8.8.8                    REM Test connectivity
ping -n 1 target_ip             REM Single ping
tracert 8.8.8.8                 REM Trace network path

REM Active connections
netstat -ano                    REM All connections with PIDs
netstat -an | findstr LISTENING  REM Only listening ports
netstat -an | findstr ESTABLISHED REM Only established connections

REM ARP table (IP to MAC)
arp -a
arp -d                          REM Clear ARP cache

REM DNS
nslookup google.com             REM DNS lookup
nslookup -type=MX google.com    REM Mail records
```

<b>What does it do?</b> Queries DNS records or inspects the local DNS cache. Unusual names are investigation leads, not proof of tunnelling or compromise.

```powershell
# Network information
Get-NetIPAddress                         # IP addresses
Get-NetIPAddress -AddressFamily IPv4     # IPv4 only
Get-NetAdapter                           # Network adapters
Get-NetRoute                             # Routing table
Get-DnsClientCache                       # DNS cache
Clear-DnsClientCache                     # Flush DNS

# Connectivity tests
Test-NetConnection -ComputerName google.com -Port 443
Test-NetConnection -ComputerName 8.8.8.8
Test-Connection -ComputerName 8.8.8.8 -Count 1

# Get all TCP connections with process names
Get-NetTCPConnection | ForEach-Object {
    $proc = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
    [PSCustomObject]@{
        LocalPort = $_.LocalPort
        RemoteAddress = $_.RemoteAddress
        RemotePort = $_.RemotePort
        State = $_.State
        ProcessName = $proc.Name
        PID = $_.OwningProcess
    }
} | Sort-Object State | Format-Table
```

<b>What does it do?</b> Queries DNS records or inspects the local DNS cache. Unusual names are investigation leads, not proof of tunnelling or compromise.

---

## 🔍 Security: Detect Suspicious Connections

```powershell
# Find processes making external network connections
function Find-SuspiciousConnections {
    Write-Host "=== Suspicious Network Connections ==" -ForegroundColor Red

    $internalRanges = @(
        '10.', '192.168.',
        '172.16.', '172.17.', '172.18.', '172.19.', '172.20.', '172.21.',
        '172.22.', '172.23.', '172.24.', '172.25.', '172.26.', '172.27.',
        '172.28.', '172.29.', '172.30.', '172.31.',
        '127.', '169.254.', '0.0.0.0', '::1', 'fe80:'
    )

    Get-NetTCPConnection -State Established | ForEach-Object {
        $conn = $_
        $isExternal = $true
        foreach ($range in $internalRanges) {
            if ($conn.RemoteAddress -like "$range*") {
                $isExternal = $false
                break
            }
        }

        if ($isExternal) {
            $proc = Get-Process -Id $conn.OwningProcess -ErrorAction SilentlyContinue
            [PSCustomObject]@{
                Process = $proc.Name
                PID = $conn.OwningProcess
                RemoteIP = $conn.RemoteAddress
                RemotePort = $conn.RemotePort
                LocalPort = $conn.LocalPort
            }
        }
    } | Format-Table -AutoSize
}

Find-SuspiciousConnections
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

---

## 🧪 Lab 1 — Network Port Analysis

```shell
REM Find what process owns each listening port
netstat -ano | findstr LISTENING
REM Then look up the PID:
tasklist /FI "PID eq [PID_from_above]"
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

```powershell
# Match listening ports to processes
Get-NetTCPConnection -State Listen |
  ForEach-Object {
    $proc = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
    [PSCustomObject]@{
      Port = $_.LocalPort
      Process = $proc.Name
      PID = $_.OwningProcess
      Path = $proc.Path
    }
  } | Sort-Object Port | Format-Table
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

---

## ❓ Module 06 Quiz

<b>Q1:</b> What does <code>netstat -ano</code> show?

> All active TCP/UDP connections and listening ports, including the process ID (PID) that owns each connection.

<b>Q2:</b> How would you identify if malware is communicating with an external server?

> Use <code>Get-NetTCPConnection</code> to list established connections, filter out internal IP ranges (10.x, 192.168.x, 172.16-31.x), and map remaining connections to their owning processes using PID.

---

## ✅ Module 06 Summary

- <code>netstat -ano</code> is the first tool to run during incident response
- Map every listening port to its owning process and verify legitimacy
- External connections from unexpected processes are high-priority alerts
- <code>Test-NetConnection</code> is PowerShell's versatile connectivity tester
- DNS cache (<code>Get-DnsClientCache</code>) reveals recently resolved domains

> ➡️ <b>Next Module:</b> Advanced Networking & Troubleshooting
</details>

<details>
<summary>MODULE 07 — ADVANCED NETWORKING & TROUBLESHOOTING</summary>


## 1. Learning objectives

By the end of this module, you should be able to:

- Inspect Windows Defender Firewall profiles and rules.
- Troubleshoot DNS resolution and inspect the DNS client cache.
- Read and modify the IPv4 routing table in a controlled lab.
- Inspect saved wireless profiles and understand credential exposure.
- Enumerate active TCP connections and relate them to processes.
- Recognise possible command-and-control (C2) indicators without treating a single indicator as proof.

## 2. Windows Defender Firewall

Windows Defender Firewall filters network traffic using rules. Rules can apply to inbound or outbound traffic and to Domain, Private or Public profiles.

### Check firewall status

```shell
netsh advfirewall show allprofiles
netsh advfirewall firewall show rule name=all
```

<b>What does it do?</b> Displays Windows Firewall profiles or rules so you can review enabled policy, direction and action.

### Inspect enabled rules with PowerShell

```powershell
Get-NetFirewallRule -Enabled True |
    Select-Object DisplayName, Direction, Action, Profile
```

<b>What does it do?</b> Displays Windows Firewall profiles or rules so you can review enabled policy, direction and action.

### Inspect inbound allow rules and their ports

```powershell
Get-NetFirewallRule -Direction Inbound -Action Allow -Enabled True |
    ForEach-Object {
        $rule = $_
        $port = Get-NetFirewallPortFilter -AssociatedNetFirewallRule $rule
        [PSCustomObject]@{
            Name      = $rule.DisplayName
            Profile   = $rule.Profile
            Protocol  = $port.Protocol
            LocalPort = $port.LocalPort
        }
    } | Format-Table -AutoSize
```

<b>What does it do?</b> Displays Windows Firewall profiles or rules so you can review enabled policy, direction and action.

### Create and remove a lab firewall rule

```shell
netsh advfirewall firewall add rule name="Lab-Allow-8080" dir=in action=allow protocol=TCP localport=8080
netsh advfirewall firewall show rule name="Lab-Allow-8080"
netsh advfirewall firewall delete rule name="Lab-Allow-8080"
```

<b>What does it do?</b> Creates or removes a Windows Firewall rule. This changes network policy; use a disposable lab VM, verify the rule and remove it after testing.

### Add and remove a blocking rule with PowerShell

```powershell
New-NetFirewallRule -DisplayName "Lab-Block-Test-IP" -Direction Outbound -RemoteAddress "203.0.113.10" -Action Block -Protocol TCP
Get-NetFirewallRule -DisplayName "Lab-Block-Test-IP"
Remove-NetFirewallRule -DisplayName "Lab-Block-Test-IP"
```

<b>What does it do?</b> Creates or removes a Windows Firewall rule. This changes network policy; use a disposable lab VM, verify the rule and remove it after testing.

<b>Important:</b> Do not disable all firewall profiles as a troubleshooting shortcut. If a lab requires a policy change, change only the specific test rule and restore the original configuration afterward.

## 3. DNS troubleshooting and security

The Domain Name System (DNS) translates names into records such as IP addresses. DNS also supports record types used by legitimate services, including TXT and MX records.

### Query DNS records with CMD

```shell
nslookup example.com
nslookup -type=A example.com
nslookup -type=MX example.com
nslookup -type=TXT example.com
nslookup -type=NS example.com
nslookup example.com 8.8.8.8
```

<b>What does it do?</b> Queries DNS records or inspects the local DNS cache. Unusual names are investigation leads, not proof of tunnelling or compromise.

### Inspect and refresh the DNS client cache

```shell
ipconfig /displaydns
ipconfig /flushdns
```

<b>What does it do?</b> Queries DNS records or inspects the local DNS cache. Unusual names are investigation leads, not proof of tunnelling or compromise.

### Query DNS with PowerShell

```powershell
Resolve-DnsName example.com
Resolve-DnsName -Name example.com -Type TXT
Get-DnsClientCache |
    Select-Object Entry, RecordName, Type, Data, TimeToLive
```

<b>What does it do?</b> Queries DNS records or inspects the local DNS cache. Unusual names are investigation leads, not proof of tunnelling or compromise.

### Triage unusual DNS cache entries

```powershell
Get-DnsClientCache | ForEach-Object {
    $name = $_.Entry
    if ($name -and ($name.Length -gt 40 -or ($name -split '\.').Count -gt 5)) {
        [PSCustomObject]@{
            Domain = $name
            Data   = $_.Data
            TTL    = $_.TimeToLive
        }
    }
} | Format-Table -AutoSize
```

<b>What does it do?</b> Queries DNS records or inspects the local DNS cache. Unusual names are investigation leads, not proof of tunnelling or compromise.

### DNS tunnelling — what to investigate

DNS tunnelling can encode data inside DNS queries and responses. During an investigation, look for combinations of:

- Unusually long or high-entropy subdomains.
- Repeated queries to the same unusual domain.
- High query volume or regular beacon-like timing.
- Unexpected TXT queries or unusual record patterns.
- A suspicious process responsible for generating the traffic.

<b>Important:</b> TXT records and long domain names are not proof of tunnelling. Correlate DNS telemetry with endpoint process data, network logs and the domain's context.

## 4. Routing and network interface inspection

A routing table tells Windows where to send traffic for different destination networks.

### View the routing table

```shell
route print
route print -4
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

### Inspect interfaces

```shell
netsh interface ipv4 show interfaces
ipconfig /all
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

### Add and remove a test route

```shell
route add 10.10.10.0 mask 255.255.255.0 192.168.1.1
route print -4
route delete 10.10.10.0
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

## 5. Portproxy awareness

Windows portproxy can forward traffic from a local listening address and port to another destination. It is a legitimate administration feature, but unexpected entries can indicate network pivoting.

```shell
netsh interface portproxy show all
netsh interface portproxy show v4tov4
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

```shell
netsh interface portproxy delete v4tov4 listenaddress=127.0.0.1 listenport=8080
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

<b>Investigation:</b> Correlate portproxy entries with listening sockets, firewall rules, process telemetry and change records. A configured proxy alone does not prove malicious activity.

## 5. Saved Wi-Fi profiles and credential exposure

Windows can store wireless network profiles. On a system you own or are authorised to assess, an elevated terminal may display a saved profile's key material.

### List saved wireless profiles

```shell
netsh wlan show profiles
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

### Inspect a specific saved profile

```shell
netsh wlan show profile name="WiFiName"
netsh wlan show profile name="WiFiName" key=clear
```

<b>What does it do?</b> Displays a saved wireless profile and may reveal its stored key. Treat the output as sensitive credentials and only inspect systems you are authorised to assess.

<b>Security note:</b> Treat the output as sensitive credentials. Do not run this against another person's device or collect passwords without explicit authorisation. Avoid copying the key into notes, screenshots or reports.

## 6. Active connections and possible C2 investigation

Command and Control (C2) is communication between a compromised system and infrastructure controlled by an operator. A process making an external connection is not automatically malicious. Investigate the process, destination, timing, user context and related telemetry.

### List established TCP connections

```powershell
Get-NetTCPConnection -State Established |
    Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, OwningProcess |
    Sort-Object RemoteAddress
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

### Map connections to process names and paths

```powershell
Get-NetTCPConnection -State Established | ForEach-Object {
    $connection = $_
    $process = Get-Process -Id $connection.OwningProcess -ErrorAction SilentlyContinue

    [PSCustomObject]@{
        Process     = if ($process) { $process.ProcessName } else { "Unknown" }
        PID         = $connection.OwningProcess
        RemoteIP    = $connection.RemoteAddress
        RemotePort  = $connection.RemotePort
        LocalPort   = $connection.LocalPort
        ProcessPath = if ($process) { $process.Path } else { $null }
    }
} | Format-Table -AutoSize
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

### Investigation checklist

When a connection looks unusual, check:

1. Is the process expected on this host?
2. Is the executable path legitimate and correctly signed?
3. Which user launched the process?
4. Is the remote IP or domain expected for the application?
5. Does the connection repeat at regular intervals?
6. Do DNS, firewall, proxy and endpoint logs support the same timeline?

A suspicious process name, destination port or IP address is only a lead. Validate it with additional evidence before classifying it as C2.

## 7. Practical lab — create, inspect and remove a firewall rule

<b>Objective:</b> Observe how a firewall rule appears in Windows and practise safe cleanup.

### Step 1 — create a test rule

```powershell
New-NetFirewallRule -DisplayName "Lab-Test-Inbound-8080" -Direction Inbound -Protocol TCP -LocalPort 8080 -Action Allow
```

<b>What does it do?</b> Creates or removes a Windows Firewall rule. This changes network policy; use a disposable lab VM, verify the rule and remove it after testing.

### Step 2 — inspect the rule

```powershell
Get-NetFirewallRule -DisplayName "Lab-Test-Inbound-8080" |
    Format-List DisplayName, Enabled, Direction, Action, Profile
```

<b>What does it do?</b> Displays Windows Firewall profiles or rules so you can review enabled policy, direction and action.

### Step 3 — remove the test rule

```powershell
Remove-NetFirewallRule -DisplayName "Lab-Test-Inbound-8080"
Get-NetFirewallRule -DisplayName "Lab-Test-Inbound-8080" -ErrorAction SilentlyContinue
```

<b>What does it do?</b> Creates or removes a Windows Firewall rule. This changes network policy; use a disposable lab VM, verify the rule and remove it after testing.

## 8. Knowledge check

<b>Q1. Which command displays saved Wi-Fi profiles?</b>

<code>netsh wlan show profiles</code>

<b>Q2. What does DNS tunnelling mean?</b>

It is a technique that carries data inside DNS queries and responses. Detection requires analysing query patterns and correlating them with endpoint and network evidence.

<b>Q3. Which PowerShell cmdlet creates a Windows Firewall rule?</b>

<code>New-NetFirewallRule</code>

<b>Q4. Which command displays the IPv4 routing table?</b>

<code>route print -4</code>

<b>Q5. Does an established connection from PowerShell prove C2?</b>

No. It is an investigation lead. Confirm the process path, user, destination, timing and supporting telemetry.

## 9. Module summary

- Inspect firewall profiles and rules before making changes.
- Use <code>Get-NetFirewallRule</code> and its filter cmdlets to review policy.
- Use <code>nslookup</code>, <code>Resolve-DnsName</code> and DNS cache commands for troubleshooting.
- Treat long DNS names and TXT queries as indicators to investigate, not proof of tunnelling.
- Use <code>route print -4</code> to inspect IPv4 routing.
- Saved Wi-Fi keys are sensitive credentials; handle them only with authorisation.
- Map established connections to processes and correlate multiple sources before identifying C2.
- Remove test firewall rules and restore lab settings after each exercise.

<b>Next:</b> Module 08 — CMD Deep Dive.
</details>

<details>
<summary>MODULE 08 — CMD DEEP DIVE — ADVANCED</summary>


<b>Use cases:</b> Build repeatable batch workflows using variables, conditions, loops, pipes and redirection for basic security automation.

> <b>Level:</b> Intermediate \| <b>Track:</b> Red Team + Blue Team

---

## 💻 Advanced CMD Techniques

### Variables and Environment

```shell
REM Set variable
set MyVar=Hello
echo %MyVar%

REM System variables
echo %USERPROFILE%    REM C:\Users\username
echo %APPDATA%        REM C:\Users\username\AppData\Roaming
echo %TEMP%           REM Temp directory
echo %COMSPEC%        REM CMD path
echo %PATH%           REM System PATH

REM Environment dump (attacker technique)
set > C:\Temp\env_dump.txt
```

<b>What does it do?</b> Demonstrates CMD batch scripting, including variables, conditions, loops, filtering or output redirection. Use %%variable in batch files and %variable only at an interactive prompt.

### Redirection and Piping

```shell
REM Output to file
dir > output.txt        REM Overwrite
dir >> output.txt       REM Append
dir > all.txt 2>&1     REM Redirect stdout and stderr to the same file
dir 2>nul              REM Suppress errors

REM Piping
netstat -ano | findstr :4444
tasklist | findstr /I "malware"
dir /S /B *.exe | findstr "Temp"
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### Loops

```shell
REM Interactive CMD examples (use one percent sign)
for %i in (1 2 3 4 5) do echo %i
for %f in (C:\Logs\*.log) do echo %f
for /F "tokens=*" %i in ('net user') do echo %i
for /L %i in (1,1,10) do echo %i

REM In a .bat or .cmd file, double the percent sign:
REM for %%i in (1 2 3 4 5) do echo %%i
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

### Conditional Logic

```shell
REM IF statement
if exist C:\secret.txt echo "File found!"
if not exist C:\file.txt echo "Not found"

REM Compare values
if "%USERNAME%"=="Administrator" echo "Running as admin!"

REM Error level check
ping 8.8.8.8 -n 1 >nul
if %ERRORLEVEL%==0 echo "Network up" else echo "No network"
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

---

## 💻 Batch Scripting for Security

### System Information Script

```javascript
@echo off
echo ===================================
echo    WINDOWS SYSTEM RECON SCRIPT
echo ===================================
echo.

echo [*] System Info:
systeminfo | findstr /C:"OS Name" /C:"OS Version" /C:"System Type"
echo.

echo [*] Network Adapters:
ipconfig /all | findstr /C:"IPv4" /C:"Default Gateway"
echo.

echo [*] Active Connections:
netstat -ano | findstr ESTABLISHED
echo.

echo [*] Logged In Users:
query user 2>nul
echo.

echo [*] Admin Group Members:
net localgroup Administrators
echo.

echo [*] Running Services:
sc query | findstr "SERVICE_NAME\|STATE"
echo.

echo [*] Startup Programs:
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run

echo.
echo [+] Recon complete. Output: %~n0_output.txt
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

### Blue Team Incident Response Script

```javascript
@echo off
set OUTPUT=C:\IR_%COMPUTERNAME%_%DATE:~-4%%DATE:~4,2%%DATE:~7,2%.txt

echo Incident Response Collection > %OUTPUT%
echo ========================= >> %OUTPUT%
echo Date: %DATE% Time: %TIME% >> %OUTPUT%
echo. >> %OUTPUT%

echo [USERS] >> %OUTPUT%
net user >> %OUTPUT%
net localgroup Administrators >> %OUTPUT%

echo [NETWORK] >> %OUTPUT%
netstat -ano >> %OUTPUT%
arp -a >> %OUTPUT%

echo [PROCESSES] >> %OUTPUT%
tasklist /SVC >> %OUTPUT%

echo [SERVICES] >> %OUTPUT%
sc query >> %OUTPUT%

echo [AUTORUNS - Run Keys] >> %OUTPUT%
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run >> %OUTPUT%
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run >> %OUTPUT%

echo Collection complete: %OUTPUT%
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## 🧪 Lab — Automation Script

Create a batch file called <code>security_check.bat</code> that:

1. Checks if firewall is enabled
2. Lists all users
3. Lists all listening ports
4. Lists startup programs
5. Exports to timestamped file

```javascript
@echo off
set TS=%DATE:~-4%%DATE:~4,2%%DATE:~7,2%_%TIME:~0,2%%TIME:~3,2%
set TS=%TS: =0%
set OUT=C:\SecurityCheck_%TS%.txt

echo Security Check Report - %DATE% %TIME% > %OUT%

echo. >> %OUT%
echo === FIREWALL STATUS === >> %OUT%
netsh advfirewall show allprofiles state >> %OUT%

echo. >> %OUT%
echo === USER ACCOUNTS === >> %OUT%
net user >> %OUT%

echo. >> %OUT%
echo === LISTENING PORTS === >> %OUT%
netstat -an | findstr LISTENING >> %OUT%

echo. >> %OUT%
echo === STARTUP PROGRAMS === >> %OUT%
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run >> %OUT% 2>&1
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run >> %OUT% 2>&1

echo Report saved to: %OUT%
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## ❓ Module 08 Quiz

<b>Q1:</b> How do you suppress error output in CMD?

> Redirect stderr to null: <code>command 2>nul</code>

<b>Q2:</b> What CMD command searches for a specific string in output?

> <code>findstr</code> — e.g., <code>netstat -ano | findstr :443</code>

---

## ✅ Module 08 Summary

- CMD scripting enables automation of security tasks at enterprise scale
- Loops and conditionals make batch scripts powerful for incident response
- Environment variables reveal system configuration — useful for both attackers and defenders
- Always redirect output to timestamped files for evidence preservation

> ➡️ <b>Next Module:</b> PowerShell Deep Dive
</details>

<details>
<summary>MODULE 09 — POWERSHELL DEEP DIVE</summary>


<b>Use cases:</b> Filter and transform objects, query system/security data, write reusable functions and handle errors in investigation scripts.

> <b>Level:</b> Intermediate–Advanced \| <b>Track:</b> Red Team + Blue Team

> 🔥 <b>PowerShell is the single most important tool for both attackers and defenders on Windows.</b>

---

## 💻 PowerShell Fundamentals

### Cmdlet Syntax

```powershell
Verb-Noun -Parameter Value

# Examples:
Get-Process -Name "chrome"
Stop-Service -Name "wuauserv"
New-Item -Path C:\Test -ItemType Directory
Remove-Item -Path C:\Test -Recurse -Force
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

### Pipelines

```powershell
# Chain commands with |
Get-Process | Where-Object {$_.CPU -gt 10} | Sort-Object CPU -Descending | Select -First 5

# Select properties
Get-Service | Select-Object Name, Status, StartType

# Measure
Get-ChildItem C:\ -Recurse | Measure-Object -Property Length -Sum

# Export
Get-Process | Export-Csv C:\processes.csv -NoTypeInformation
Get-Process | ConvertTo-Json | Out-File C:\processes.json
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## 🔐 Security-Focused PowerShell Cmdlets

```powershell
# Services
Get-Service
Get-Service | Where-Object {$_.Status -eq 'Running'}
Start-Service -Name "Bits"
Stop-Service -Name "Bits"

# Events (VERY IMPORTANT for SOC)
Get-EventLog -LogName Security -Newest 50
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625}

# Invoke-Command (remote execution)
Invoke-Command -ComputerName Server01 -ScriptBlock {Get-Process}
Invoke-Command -ComputerName Server01 -ScriptBlock {netstat -ano}

# WMI (Windows Management Instrumentation)
Get-CimInstance Win32_Process
Get-CimInstance Win32_Service | Where-Object {$_.PathName -like '*Temp*'}
Get-CimInstance Win32_ComputerSystem
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

---

## 💚 PowerShell Scripting

### Functions and Parameters

```powershell
function Get-SystemInfo {
    param(
        [string]$ComputerName = $env:COMPUTERNAME
    )

    $info = Get-CimInstance Win32_OperatingSystem -ComputerName $ComputerName

    [PSCustomObject]@{
        Computer = $info.CSName
        OS = $info.Caption
        Version = $info.Version
        Architecture = $info.OSArchitecture
        LastBoot = $info.LastBootUpTime
        UptimeDays = ((Get-Date) - $info.LastBootUpTime).Days
    }
}

Get-SystemInfo | Format-List
```

<b>What does it do?</b> Runs a PowerShell check or automation routine for functions and parameters. Review the parameters, permissions and output before using it on a real endpoint.

### Error Handling

```powershell
try {
    $result = Get-Content "C:\nonexistent.txt" -ErrorAction Stop
    Write-Host "File read: $result"
} catch [System.IO.FileNotFoundException] {
    Write-Warning "File not found!"
} catch {
    Write-Error "Unexpected error: $_"
} finally {
    Write-Host "Cleanup complete"
}
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## 🔥 Security: PowerShell Attack Techniques (Educational)

### Common Attack Patterns (Know to Defend)

```javascript
Attack Technique          | Indicator to Detect
────────────────────────────────────────────────────────────
Encoded commands          | -enc or -EncodedCommand
Download cradles          | DownloadString, WebClient
Execution policy bypass   | -ExecutionPolicy Bypass
Hidden window             | -WindowStyle Hidden
Fileless execution        | IEX, Invoke-Expression
Reflective loading        | [Reflection.Assembly]
```

<b>What does it do?</b> Provides a quick reference for common attack patterns know to defend; use the surrounding explanation for context and prerequisites.

### PowerShell Logging (Blue Team Defense)

```powershell
# Check if Script Block Logging is enabled
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging'

# Enable Script Block Logging
New-Item -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell' -Force
New-Item -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging' -Force
Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging' `
  -Name 'EnableScriptBlockLogging' -Value 1

# Read PowerShell script block logs
Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' | Where-Object {$_.Id -eq 4104} |
  Where-Object {
    $_.Message -like '*DownloadString*' -or
    $_.Message -like '*Invoke-Expression*' -or
    $_.Message -like '*IEX*'
  } |
  Select TimeCreated, Message | Format-List
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## 🧪 Lab — System Monitoring Script

```powershell
# Periodic security-monitoring example
function Invoke-SecurityMonitor {
    param([int]$IntervalSeconds = 30)

    Write-Host "[*] Security Monitor Started - Press Ctrl+C to stop" -ForegroundColor Green

    while ($true) {
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        Write-Host "`n[$timestamp] Checking..." -ForegroundColor Cyan

        # Check for new high-CPU processes
        $highCPU = Get-Process | Where-Object {$_.CPU -gt 30} |
          Select Name, Id, CPU, Path
        if ($highCPU) {
            Write-Host "[!] High CPU Processes:" -ForegroundColor Yellow
            $highCPU | Format-Table
        }

        # Check for new listening ports
        $listening = Get-NetTCPConnection -State Listen |
          Select LocalPort, OwningProcess |
          Sort-Object LocalPort
        Write-Host "[+] Listening Ports: $($listening.Count)" -ForegroundColor Green

        # Check for suspicious processes
        $suspicious = Get-CimInstance Win32_Process |
          Where-Object {$_.ExecutablePath -like '*Temp*' -or $_.ExecutablePath -like '*AppData*'} |
          Select Name, ProcessId, ExecutablePath
        if ($suspicious) {
            Write-Host "[!!!] SUSPICIOUS PROCESSES FOUND:" -ForegroundColor Red
            $suspicious | Format-Table
        }

        Start-Sleep -Seconds $IntervalSeconds
    }
}

# Run it
Invoke-SecurityMonitor -IntervalSeconds 10
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

---

## ❓ Module 09 Quiz

<b>Q1:</b> What PowerShell flag is a major red flag for malicious activity?

> <code>-EncodedCommand</code> (or <code>-enc</code>) allows running base64-encoded commands, commonly used to obfuscate malicious PowerShell.

<b>Q2:</b> How do you enable PowerShell Script Block Logging?

> Set registry value <code>EnableScriptBlockLogging = 1</code> at <code>HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging</code>

<b>Q3:</b> What PowerShell cmdlet executes commands on remote computers?

> <code>Invoke-Command -ComputerName RemotePC -ScriptBlock {commands}</code>

---

## ✅ Module 09 Summary

- PowerShell is the most powerful Windows administration and attack tool
- Script Block Logging (Event 4104) captures full script content for detection
- <code>-enc</code>, <code>-WindowStyle Hidden</code>, and <code>IEX</code> are major attack indicators
- <code>Invoke-Command</code> enables remote execution across the enterprise
- Always enable PowerShell logging in production environments

> ➡️ <b>Next Module:</b> Windows Logs & Monitoring
</details>

<details>
<summary>MODULE 10 — WINDOWS LOGS & MONITORING</summary>


<b>Use cases:</b> Query Windows event channels, identify authentication/process events and collect evidence for incident response.

> <b>Level:</b> Intermediate \| <b>Track:</b> Blue Team (Primary)

---

## 📺 Event Viewer Overview

```javascript
Key Log Locations:
├─ Windows Logs
│   ├─ Security     ← Authentication, access (SOC's #1 source)
│   ├─ System       ← OS events, crashes, service installs
│   ├─ Application  ← App crashes, errors
│   └─ Setup        ← Windows Update
└─ Applications and Services Logs
    ├─ Microsoft\Windows\PowerShell  ← PS attacks
    ├─ Microsoft\Windows\Sysmon      ← Advanced detection
    └─ Microsoft\Windows\TaskScheduler ← Scheduled tasks
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for event viewer overview; correlate actions, telemetry and response decisions rather than treating it as an executable command.

---

## 💻 Log Query Commands

```shell
REM wevtutil
wevtutil qe Security /c:10 /f:text
wevtutil qe Security /q:"*[System[(EventID=4625)]]" /c:20 /f:text
wevtutil el    REM List all logs
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

```powershell
# Get-WinEvent (preferred method)
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} | Select -First 10
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624,4625,4672}
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4625
    StartTime=(Get-Date).AddHours(-1)
}

# Get-EventLog (older method)
Get-EventLog -LogName Security -InstanceId 4625 -Newest 20
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 🔥 Key Security Event IDs

| Event ID | Event | Detection use |
| --- | --- | --- |
| 4624 | Successful logon | Review account, logon type and source |
| 4625 | Failed logon | Identify repeated failures and source |
| 4648 | Explicit credentials used | Investigate alternate credentials |
| 4672 | Special privileges assigned to a new logon | Review privileged logon context |
| 4688 | Process creation | Review parent and command line when configured |
| 4698 | Scheduled task created | Investigate task persistence |
| 4720 | User account created | Review account creation |
| 4726 | User account deleted | Review account deletion |
| 7045 | Service installed (System log) | Investigate new services |
| 1102 | Security audit log cleared | Preserve central copies and investigate |

<b>Logging prerequisites:</b> Event availability depends on audit policy, log permissions and system configuration. For example, 4688 requires process-creation auditing; 4663 requires object-access auditing and an applicable SACL. Event IDs are evidence sources, not conclusions.

---

## 🧪 Lab 1 — Brute Force Detection

```powershell
# Brute force detector
function Find-BruteForce {
    param([int]$Threshold = 5, [int]$MinutesBack = 10)

    $startTime = (Get-Date).AddMinutes(-$MinutesBack)
    Write-Host "Analyzing failed logins in last $MinutesBack minutes..." -ForegroundColor Yellow

    $failures = Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4625; StartTime=$startTime
    } -ErrorAction SilentlyContinue

    Write-Host "Total failures: $($failures.Count)"

    if ($failures.Count -ge $Threshold) {
        Write-Host "ALERT: Possible brute force! ($($failures.Count) failures)" -ForegroundColor Red
    }

    # Check if brute force succeeded
    $successes = Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4624; StartTime=$startTime
    } -ErrorAction SilentlyContinue

    if ($failures.Count -ge $Threshold -and $successes.Count -gt 0) {
        Write-Host "Possible attack pattern: failures and a success occurred in the same time window. Correlate account, source IP and logon details before concluding." -ForegroundColor Red
    }
}

Find-BruteForce -Threshold 3 -MinutesBack 60
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 🧪 Lab 2 — Incident Response Log Collection

```powershell
# Full IR log collection
$outputDir = "C:\IR_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
New-Item -ItemType Directory -Path $outputDir -Force | Out-Null

# Failed logins (last 24h)
Get-WinEvent -FilterHashtable @{
    LogName='Security'; Id=4625
    StartTime=(Get-Date).AddDays(-1)
} | Select TimeCreated, Id, Message |
Export-Csv "$outputDir\failed_logins.csv" -NoTypeInformation

# Successful logins
Get-WinEvent -FilterHashtable @{
    LogName='Security'; Id=4624
    StartTime=(Get-Date).AddDays(-1)
} | Select TimeCreated, Id, Message |
Export-Csv "$outputDir\successful_logins.csv" -NoTypeInformation

# Process creation
Get-WinEvent -FilterHashtable @{
    LogName='Security'; Id=4688
    StartTime=(Get-Date).AddDays(-1)
} | Select TimeCreated, Id, Message |
Export-Csv "$outputDir\process_creation.csv" -NoTypeInformation

Write-Host "IR collection saved to: $outputDir" -ForegroundColor Green
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## ❓ Module 10 Quiz

<b>Q1:</b> What Event ID indicates an audit log was cleared?

> Event ID 1102. This is a critical indicator — attackers clear logs to remove evidence.

<b>Q2:</b> Write a PowerShell command to find all failed logins in the last hour.

> <code>Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625; StartTime=(Get-Date).AddHours(-1)}</code>

---

## ✅ Module 10 Summary

- Security Event Log is the SOC analyst's primary data source
- 4624/4625 = login success/failure; 4688 = process creation; 1102 = log cleared
- <code>Get-WinEvent</code> with FilterHashtable is the most efficient log query method
- Always export findings to CSV for timeline analysis
- Event 1102 (log cleared) should always be a P1 alert

> ➡️ <b>Next Module:</b> Windows Internals for Security
</details>

<details>
<summary>MODULE 11 — WINDOWS INTERNALS DEEP DIVE — DLLS, TOKENS & HANDLES</summary>


<b>Use cases:</b> Understand DLL loading, token contents, integrity levels, handles and process-inspection tooling.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team + Blue Team \| <b>Course:</b> Windows Cybersecurity

---

## 🎯 What You Will Learn

- DLL architecture and how injection/hijacking works
- Security tokens and integrity levels in depth
- Handles and what they reveal during analysis
- Process memory layout
- Sysinternals toolkit mastery

---

## 💾 DLL Architecture

```javascript
DLL (Dynamic Link Library) = Shared Code Library

without DLLs:                    with DLLs:
┌────────────────────┐     ┌────────────────────┐
│ App 1: print code  │     │ App 1 (tiny)         │
│ App 2: print code  │     │ App 2 (tiny)         │  both use:
│ App 3: print code  │     │ App 3 (tiny)         │  ┌──────────────┐
└────────────────────┘     └────────────────────┘  │ print.dll    │
                                                     └──────────────┘
DLL Benefits: shared memory, smaller apps, easy patching
```

<b>What does it do?</b> Provides a visual reference for dll architecture, showing the main components or relationships.

### Key System DLLs

| DLL | Purpose | Attack Relevance |
| --- | --- | --- |
| <code>ntdll.dll</code> | Native API, syscall interface | All attacks go through this |
| <code>kernel32.dll</code> | Core Windows API | Process, memory, file ops |
| <code>advapi32.dll</code> | Security, registry, services | Privilege and token ops |
| <code>ws2_32.dll</code> | Winsock (networking) | C2 communications |
| <code>wininet.dll</code> | Internet access | Download cradles |
| <code>msvcrt.dll</code> | C runtime library | Many programs depend on this |

### DLL Attack Techniques

```javascript
1. DLL INJECTION
   Attacker forces legitimate process to load their DLL
   Tools: CreateRemoteThread + LoadLibrary
   Detect: Sysmon Event 7 (ImageLoad), if configured; review image path and signature fields. Event 7 records image loads and is not an unsigned-DLL alert by itself. It is disabled by default in Sysmon and can generate high event volume.

2. DLL HIJACKING
   Process searches for DLL in wrong order → attacker plants theirs
   DLL search order depends on the application, Windows version, SafeDllSearchMode, KnownDLLs, package context and API-set handling. The application directory and configured search locations may be relevant; verify the actual load path with Process Monitor.
   Detect: Unexpected DLL path, publisher, signature or hash for that application. Many legitimate DLLs load outside System32; validate the application context.

3. DLL SIDE-LOADING
   Legitimate app loads a DLL by name → attacker replaces it
   Example: Rename malware to 'version.dll' next to a legitimate app
   Detect: Hash verification of DLLs

4. REFLECTIVE DLL LOADING
   DLL loaded from memory, never touches disk
   Very hard to detect without memory forensics
   Detect: Sysmon Event 7 with no FileName path
```

<b>What does it do?</b> Provides a visual reference for dll attack techniques, showing the main components or relationships.

```powershell
# List all DLLs loaded by a specific process
$proc = Get-Process -Name 'notepad' -EA SilentlyContinue
if ($proc) {
    $proc.Modules | Select-Object ModuleName, FileName, FileVersion |
    Format-Table -AutoSize
}

# Find unsigned DLLs in a process
Get-Process | ForEach-Object {
    $p = $_
    $_.Modules | ForEach-Object {
        $sig = Get-AuthenticodeSignature $_.FileName -EA SilentlyContinue
        if ($sig -and $sig.Status -ne 'Valid' -and $_.FileName -like '*.dll') {
            [PSCustomObject]@{
                Process    = $p.Name
                PID        = $p.Id
                DLL        = $_.FileName
                Signed     = $sig.Status
                Suspicious = $_.FileName -like '*Temp*' -or $_.FileName -like '*AppData*'
            }
        }
    }
} | Where-Object { $_.Suspicious } | Format-Table -AutoSize
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

---

## 🔑 Security Tokens Deep Dive

### Token Contents

```javascript
Security Token
├─ User SID:          S-1-5-21-...-1001    (who you are)
├─ Group SIDs:        Administrators, Users, etc.
├─ Privileges:        SeDebugPrivilege, SeImpersonatePrivilege
├─ Integrity Level:   System > High > Medium > Low > Untrusted
├─ Session ID:        Which logon session
└─ Type:              Primary (process) or Impersonation (thread)
```

<b>What does it do?</b> Provides a visual reference for token contents, showing the main components or relationships.

### Integrity Levels in Practice

```javascript
System      → System-integrity processes (not the kernel itself)
High        → Elevated administrative processes
Medium      → Standard user processes
Low         → Low-integrity sandboxed processes
Untrusted   → Highly restricted processes

Mandatory Integrity Control (MIC) primarily enforces "no write up": a lower-integrity process is restricted from writing to higher-integrity objects. Discretionary ACLs, privileges and protected-process rules also affect access.
```

<b>What does it do?</b> Provides a visual reference for integrity levels in practice, showing the main components or relationships.

### Inspect process ownership and command lines

```powershell
# Check current token privileges
whoami /priv | Format-Table

# Inventory process ownership and command lines (this does not read integrity levels)
Get-CimInstance Win32_Process | ForEach-Object {
    $proc = Get-Process -Id $_.ProcessId -EA SilentlyContinue
    if ($proc) {
        [PSCustomObject]@{
            Name      = $_.Name
            PID       = $_.ProcessId
            Owner     = "$($_.GetOwner().Domain)\$($_.GetOwner().User)"
            CmdLine   = ($_.CommandLine -replace '\s+', ' ')
        }
    }
} | Format-Table -AutoSize
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

---

## 🔍 Handles

A <b>handle</b> is a reference to an open resource (file, registry key, process, event). Every process has handles to the resources it's currently using.

```shell
REM Sysinternals handle.exe
handle.exe -p notepad.exe          REM All handles held by notepad
handle.exe C:\secret.txt           REM Who has this file open?
handle.exe -a -p [PID]             REM All handles for a PID
```

<b>What does it do?</b> Provides a quick reference for handles; use the surrounding explanation for context and prerequisites.

### Security Use Case

> If malware has a handle to <code>lsass.exe</code>, it can read LSASS memory for credential dumping. Sysmon Event 10 captures exactly this — which process opened a handle to which other process.

---

## 🛠️ Sysinternals Master Guide

### Process Explorer

```javascript
Key Features:
✔ Shows full process tree with parent-child visualization
✔ Color coding: purple = packed (suspicious), red = deleted executable
✔ Right-click → Check VirusTotal (instant malware check!)
✔ Shows all DLLs and handles for each process
✔ Lower pane: DLLs tab (what libraries are loaded)
```

<b>What does it do?</b> Provides a visual reference for process explorer, showing the main components or relationships.

### Procmon (Process Monitor)

```javascript
Captures in real-time:
✔ File system operations (read, write, create, delete)
✔ Registry operations (query, set, delete)
✔ Network operations (TCP, UDP connections)
✔ Process/Thread creation

Power Filter Examples:
  Process Name: malware.exe        → See everything malware does
  Operation: RegSetValue            → All registry writes
  Path contains: \Temp              → Activity in Temp folders
  Result: ACCESS DENIED             → Permission errors
```

<b>What does it do?</b> Provides a visual reference for procmon process monitor, showing the main components or relationships.

### Autoruns

```javascript
Checks these locations automatically:
✔ Run/RunOnce keys (HKCU + HKLM)
✔ Startup folders
✔ Services
✔ Scheduled Tasks
✔ Drivers
✔ Browser extensions
✔ WMI subscriptions

Color coding:
  Yellow  = File not found (broken persistence)
  Red     = Not verified by VirusTotal
  Pink    = Unsigned entry (suspicious!)
```

<b>What does it do?</b> Provides a quick reference for autoruns; use the surrounding explanation for context and prerequisites.

---

## 🧪 Labs

### Lab 1: DLL Analysis

```powershell
# Open notepad, then analyze its DLLs
Start-Process notepad
Start-Sleep 2
$notepad = Get-Process notepad
$notepad.Modules | Select-Object ModuleName, FileName |
    Sort-Object ModuleName | Format-Table
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

### Lab 2: Suspicious Process Analysis

```powershell
# Full process security analysis
Get-CimInstance Win32_Process | ForEach-Object {
    $owner = $_.GetOwner()
    [PSCustomObject]@{
        PID        = $_.ProcessId
        PPID       = $_.ParentProcessId
        Name       = $_.Name
        Owner      = "$($owner.Domain)\$($owner.User)"
        Path       = $_.ExecutablePath
        Suspicious = ($_.ExecutablePath -like '*Temp*') -or
                     ($_.ExecutablePath -like '*AppData*') -or
                     ($_.Name -like 'svchost*' -and $_.ExecutablePath -notlike '*System32*')
    }
} | Where-Object { $_.Suspicious } | Format-Table -AutoSize
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

---

## ❓ Module 11 Quiz

<b>Q1: What is the DLL search order and why does it matter for security?</b>

> Windows searches for DLLs in this order: (1) Application directory, (2) System32, (3) Windows directory, (4) Current directory, (5) PATH entries. If an attacker places a malicious DLL with the right name in the application directory, Windows loads it before the legitimate System32 version — this is DLL hijacking.

<b>Q2: What does SeImpersonatePrivilege allow and why is it dangerous?</b>

> SeImpersonatePrivilege allows a process to impersonate another user's security token. Services running as LOCAL SERVICE or NETWORK SERVICE typically have it. Attackers (especially through web shell access) exploit it using Potato-family attacks to steal the SYSTEM token and escalate to SYSTEM privileges.

<b>Q3: What Sysinternals tool should you use to check all auto-start locations for persistence?</b>

> Autoruns — it comprehensively checks Registry Run keys, Startup folders, Services, Scheduled Tasks, Drivers, WMI subscriptions, and more. It highlights unsigned entries and can verify against VirusTotal.

---

## ✅ Module Summary

- DLLs are shared code libraries loaded into process memory at runtime
- DLL hijacking exploits search order; DLL injection forces malicious DLL into legitimate process
- Security tokens contain identity, groups, and privileges — token theft enables privilege escalation
- Handles are references to open resources; LSASS handle access = credential dump attempt
- Sysinternals (Process Explorer + Procmon + Autoruns) = essential SOC toolkit

> ➡️ <b>Next Module:</b> Persistence Techniques
</details>

<details>
<summary>MODULE 12 — PERSISTENCE TECHNIQUES</summary>


<b>Use cases:</b> Inventory and detect common auto-start mechanisms such as Run keys, startup folders, scheduled tasks and services; any creation example is lab-only.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team (awareness) + Blue Team (detection)

> ⚠️ <b>Lab environments only. For educational awareness and detection purposes.</b>

---

## 📍 What Is Persistence?

Persistence ensures an attacker maintains access even after system reboots, password changes, or basic cleanup attempts.

---

## 📂 Persistence Techniques Overview

| Technique | Requires Admin | Detection Difficulty | Event ID |
| --- | --- | --- | --- |
| Registry Run Keys | No (HKCU) | Low | Sysmon 13 |
| Startup Folder | No | Low | Sysmon 11 |
| Scheduled Tasks | Yes | Medium | 4698 |
| Windows Services | Yes | Medium | 7045 |
| WMI Subscriptions | Yes | High | Sysmon 19-21 |
| DLL Hijacking | Sometimes | High | Sysmon 7 |

---

## 📁 Startup Folder

```shell
REM Lab only: create a harmless startup marker script for the current user.
echo @echo off > "%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\lab-marker.bat"
echo echo Lab startup marker >> "%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\lab-marker.bat"

REM Optional all-users startup location (requires appropriate permissions).
REM Create only in a disposable VM and remove it immediately after testing.
```

<b>What does it do?</b> Provides a quick reference for startup folder; use the surrounding explanation for context and prerequisites.

```powershell
# Blue Team: Check startup folders
$startupPaths = @(
    "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup",
    "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
)

foreach ($path in $startupPaths) {
    Write-Host "Checking: $path" -ForegroundColor Yellow
    Get-ChildItem $path -Force -ErrorAction SilentlyContinue |
        Select Name, LastWriteTime, @{N='Size';E={$_.Length}} | Format-Table
}
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## 🗓️ Scheduled Tasks

```shell
REM Create scheduled task (Lab only)
schtasks /create /tn "WindowsSystemUpdate" /tr "C:\Temp\payload.bat" /sc onlogon /ru SYSTEM

REM List all scheduled tasks
schtasks /query /fo LIST /v
schtasks /query /fo CSV > C:\tasks.csv

REM Delete task
schtasks /delete /tn "WindowsSystemUpdate" /f
```

<b>What does it do?</b> Creates or inspects scheduled-task persistence. Task creation changes the host and may require elevation; use a lab task and delete it after the exercise.

```powershell
# Blue Team: Scheduled task audit
Get-ScheduledTask | Where-Object {$_.State -ne 'Disabled'} | ForEach-Object {
    $task = $_
    $action = $task.Actions | Select -First 1
    [PSCustomObject]@{
        TaskName = $task.TaskName
        TaskPath = $task.TaskPath
        State = $task.State
        Execute = $action.Execute
        Arguments = $action.Arguments
        UserId = ($task.Principal).UserId
    }
} | Where-Object {
    $_.Execute -like "*Temp*" -or
    $_.Execute -like "*AppData*" -or
    $_.Execute -like "*Downloads*"
} | Format-Table -AutoSize
```

<b>What does it do?</b> Enumerates scheduled tasks and reviews their actions, triggers and run-as identities for unexpected configuration.

---

## ⚙️ Windows Services Persistence

```shell
REM Create malicious service (Lab only)
sc create FakeService binPath= "C:\Temp\payload.exe" start= auto
sc description FakeService "Windows System Update Helper"
sc start FakeService

REM Detect: List non-Microsoft services
sc query | findstr "SERVICE_NAME" 
powershell -NoProfile -Command "Get-CimInstance Win32_Service | Select-Object Name,PathName,StartMode,State | Format-Table -AutoSize"
```

<b>What does it do?</b> Inspects or changes Windows service configuration and state. Start, stop, create and delete operations modify the host; use a clearly identified lab service.

```powershell
# Detect non-standard services
Get-CimInstance Win32_Service | Where-Object {
    $_.PathName -ne $null -and
    $_.PathName -notlike "*\Windows\*" -and
    $_.PathName -notlike "*\Program Files\*"
} | Select Name, PathName, StartMode, State | Format-Table -AutoSize
```

<b>What does it do?</b> Lists services and inspects their state, startup mode, executable path and run-as account.

---

## 🧪 Lab — Complete Persistence Detector

```powershell
function Find-AllPersistence {
    Write-Host "=== PERSISTENCE HUNTER ==" -ForegroundColor Red
    Write-Host "Checking all common persistence locations..." -ForegroundColor Yellow

    # 1. Registry Run Keys
    Write-Host "`n[1] Registry Run Keys:" -ForegroundColor Cyan
    @(
        "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run",
        "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
    ) | ForEach-Object {
        try {
            $vals = Get-ItemProperty $_ -ErrorAction Stop
            $vals.PSObject.Properties | Where-Object {$_.Name -notlike "PS*"} |
              Select @{N='Key';E={$_}},@{N='Name';E={$_.Name}},@{N='Value';E={$_.Value}} |
              Format-Table
        } catch {}
    }

    # 2. Startup Folders
    Write-Host "[2] Startup Folders:" -ForegroundColor Cyan
    Get-ChildItem "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup" -Force -EA SilentlyContinue
    Get-ChildItem "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup" -Force -EA SilentlyContinue

    # 3. Scheduled Tasks
    Write-Host "[3] Suspicious Scheduled Tasks:" -ForegroundColor Cyan
    Get-ScheduledTask | Where-Object {$_.State -ne 'Disabled'} | ForEach-Object {
        $a = $_.Actions | Select -First 1
        if ($a.Execute -like "*Temp*" -or $a.Execute -like "*AppData*" -or $a.Execute -like "*Public*") {
            Write-Host "  SUSPICIOUS TASK: $($_.TaskName) -> $($a.Execute)" -ForegroundColor Red
        }
    }

    # 4. Services
    Write-Host "[4] Non-Standard Services:" -ForegroundColor Cyan
    Get-CimInstance Win32_Service | Where-Object {
        $_.PathName -and
        $_.PathName -notlike "*Windows*" -and
        $_.PathName -notlike "*Program Files*"
    } | Select Name, PathName, State | Format-Table
}

Find-AllPersistence
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## ❓ Module 12 Quiz

<b>Q1:</b> Which persistence mechanism requires no admin rights?

> Registry Run key at HKCU and Startup folder in the user's AppData profile both require no admin privileges.

<b>Q2:</b> What Event ID is generated when a new scheduled task is created?

> Event ID 4698 (A scheduled task was created).

---

## ✅ Module 12 Summary

- Persistence ensures continued access after reboots and basic remediation
- Registry Run keys and Startup folder require no admin — easy for low-privilege malware
- Scheduled tasks and services require admin but are harder to detect
- WMI persistence is the stealthiest — no files on disk
- The persistence hunter script covers all major locations in one sweep

> ➡️ <b>Next Module:</b> Privilege Escalation
</details>

<details>
<summary>MODULE 13 — WINDOWS PRIVILEGE ESCALATION</summary>


<b>Use cases:</b> Assess privilege boundaries and misconfigurations, validate candidate escalation paths and understand defensive hardening.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team (awareness) + Blue Team (hardening & detection)

> ⚠️ <b>Lab environments ONLY. Educational purposes for defense.</b>

---

## 🎯 What Is Privilege Escalation?

Privilege escalation is gaining higher-level access than originally granted — typically moving from standard user to Administrator or SYSTEM.

### Real-World Analogy

> You enter a building as a visitor (standard user). You find an unlocked admin office door (misconfiguration), put on an admin uniform (privilege escalation), and now have full building access (SYSTEM).

---

## 📊 Common PrivEsc Techniques

| Technique | Description | Detection |
| --- | --- | --- |
| Weak service permissions | Modify service binary | Event 7045, Sysmon 1 |
| Unquoted service paths | Inject executable in path | Event 7045 |
| AlwaysInstallElevated | MSI runs as SYSTEM | Registry check |
| Token impersonation | Steal SYSTEM token | Sysmon 10 |
| DLL hijacking | Elevated process loads your DLL | Sysmon 7 |
| Kernel exploits | CVEs for kernel elevation | Sysmon 1 |

---

## 🔍 Detection & Enumeration

```shell
REM Check current privileges
whoami /priv
whoami /all

REM Check for AlwaysInstallElevated vulnerability
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
REM If both = 1, VULNERABLE!

REM Unquoted service paths check
powershell -NoProfile -Command "Get-CimInstance Win32_Service | Select-Object Name,DisplayName,PathName,StartMode |"
findstr /i /v "C:\Windows\\" | findstr /i /v "\""
REM Any result without quotes around path = potential vulnerability

REM Check service permissions
sc sdshow ServiceName
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

```powershell
# Privilege escalation checks
function Invoke-PrivEscCheck {
    Write-Host "=== Privilege Escalation Checks ==" -ForegroundColor Red

    # Current privileges
    Write-Host "`n[1] Current User: $(whoami)" -ForegroundColor Yellow
    whoami /priv

    # AlwaysInstallElevated
    Write-Host "`n[2] AlwaysInstallElevated Check:" -ForegroundColor Yellow
    $hkcu = (Get-ItemProperty "HKCU:\SOFTWARE\Policies\Microsoft\Windows\Installer" `
      -Name AlwaysInstallElevated -ErrorAction SilentlyContinue).AlwaysInstallElevated
    $hklm = (Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\Installer" `
      -Name AlwaysInstallElevated -ErrorAction SilentlyContinue).AlwaysInstallElevated

    if ($hkcu -eq 1 -and $hklm -eq 1) {
        Write-Host "  VULNERABLE: AlwaysInstallElevated is enabled!" -ForegroundColor Red
    } else {
        Write-Host "  Safe: AlwaysInstallElevated not enabled" -ForegroundColor Green
    }

    # Unquoted service paths
    Write-Host "`n[3] Unquoted Service Paths:" -ForegroundColor Yellow
    Get-CimInstance Win32_Service | Where-Object {
        $_.PathName -and
        $_.PathName -notlike '"*' -and
        $_.PathName -like '* *' -and
        $_.PathName -notlike '*\\Windows\\*'
    } | Select Name, PathName | Format-Table

    # Writable service directories
    Write-Host "`n[4] Auto-start services with non-Windows paths:" -ForegroundColor Yellow
    Get-CimInstance Win32_Service | Where-Object {
        $_.StartMode -eq 'Auto' -and
        $_.PathName -notlike '*\Windows\*'
    } | Select Name, PathName, State | Format-Table
}

Invoke-PrivEscCheck
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## 🛡️ Hardening Against PrivEsc

```powershell
# Disable AlwaysInstallElevated in both policy hives.
# Run in an elevated PowerShell session. Create policy keys if absent.
$paths = @(
    "HKCU:\SOFTWARE\Policies\Microsoft\Windows\Installer",
    "HKLM:\SOFTWARE\Policies\Microsoft\Windows\Installer"
)
foreach ($path in $paths) {
    New-Item -Path $path -Force | Out-Null
    New-ItemProperty -Path $path -Name AlwaysInstallElevated -PropertyType DWord -Value 0 -Force | Out-Null
}

# UAC: enable the policy and prompt for consent on the secure desktop.
$uacPath = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"
New-ItemProperty -Path $uacPath -Name EnableLUA -PropertyType DWord -Value 1 -Force | Out-Null
New-ItemProperty -Path $uacPath -Name ConsentPromptBehaviorAdmin -PropertyType DWord -Value 2 -Force | Out-Null
New-ItemProperty -Path $uacPath -Name PromptOnSecureDesktop -PropertyType DWord -Value 1 -Force | Out-Null

# Credential Guard status check (this does NOT enable Credential Guard).
Get-CimInstance -Namespace root\Microsoft\Windows\DeviceGuard -ClassName Win32_DeviceGuard |
    Select-Object -ExpandProperty SecurityServicesRunning
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## ❓ Module 13 Quiz

<b>Q1:</b> What is the AlwaysInstallElevated vulnerability?

> When both HKCU and HKLM <code>AlwaysInstallElevated</code> registry values are set to 1, any user can install MSI packages with SYSTEM privileges, allowing privilege escalation without admin rights.

<b>Q2:</b> What is an unquoted service path vulnerability?

> When a Windows service path contains spaces and isn't enclosed in quotes, Windows searches each space-separated component as a potential executable. An attacker can place a malicious executable at a searched location to execute with the service's privileges.

---

## ✅ Module 13 Summary

- Privilege escalation moves an attacker from standard user to admin/SYSTEM
- Most PrivEsc stems from misconfiguration, not exploits
- AlwaysInstallElevated and unquoted service paths are common misconfigs
- <code>whoami /priv</code> reveals dangerous privileges like SeImpersonatePrivilege
- Harden by fixing misconfigs, enabling UAC, and deploying Credential Guard

> ➡️ <b>Next Module:</b> Windows for Pentesting (Red Team)
</details>

<details>
<summary>MODULE 14 — WINDOWS FOR PENTESTING — POST-ACCESS</summary>


<b>Use cases:</b> Follow an authorised post-access workflow to enumerate the host, identity, network and configuration without assuming that discovery proves exploitability.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team (educational, authorized testing only)

> ⚠️ <b>CRITICAL: Only perform these techniques in authorized lab environments with explicit written permission. Unauthorized use is illegal.</b>

---

## 🎯 Post-Exploitation Enumeration

After gaining initial access, attackers enumerate the environment to understand what they've compromised.

### System Reconnaissance

```shell
REM System information
systeminfo
systeminfo | findstr /C:"OS" /C:"Hotfix"

REM Who am I?
whoami
whoami /all
net user %USERNAME%

REM What domain am I in?
net config workstation
nltest /domain_trusts

REM Computer name and info
hostname
set COMPUTERNAME

REM What can I see on the network?
net view
net view /domain
arp -a
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

```powershell
# PowerShell enumeration
# System overview
[PSCustomObject]@{
    Hostname = $env:COMPUTERNAME
    Domain = $env:USERDOMAIN
    Username = $env:USERNAME
    OS = (Get-CimInstance Win32_OperatingSystem).Caption
    Architecture = $env:PROCESSOR_ARCHITECTURE
    LocalAdmins = (Get-LocalGroupMember -Group 'Administrators').Name -join ', '
} | Format-List

# Find interesting files
Get-ChildItem -Path C:\Users -Recurse -ErrorAction SilentlyContinue |
  Where-Object {
    $_.Name -like "*password*" -or
    $_.Name -like "*cred*" -or
    $_.Name -like "*.kdbx" -or    # KeePass
    $_.Name -like "*vpn*" -or
    $_.Name -like "id_rsa"         # SSH keys
  } |
  Select FullName, LastWriteTime | Format-Table

# Check for stored credentials
cmdkey /list
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## 🔐 Credential Harvesting Awareness

> <b>For Blue Team awareness — know what attackers look for to better protect it.</b>

### Common Credential Locations Attackers Target

```javascript
Location                          | What's There
────────────────────────────────────────────────────────────
LSASS process memory              | Authentication secrets and credential material, subject to Windows protections; not all passwords are stored as plaintext
SAM database (C:\Windows\System32\config\SAM) | Local account hashes
NTDS.DIT (on domain controllers)  | All domain credentials
Credential Manager                | Saved passwords
Browser data                      | Stored passwords, cookies
Text files named *password*       | Careless storage
```

<b>What does it do?</b> Provides a quick reference for common credential locations attackers target; use the surrounding explanation for context and prerequisites.

### Blue Team: Credential Protection Status

```powershell
# Check Credential Guard status; this command does not enable it.
# Availability depends on Windows edition, hardware, firmware and policy configuration.
Get-CimInstance -Namespace 'root\Microsoft\Windows\DeviceGuard' -ClassName Win32_DeviceGuard |
  Select SecurityServicesRunning

# Check if Credential Guard is active
# SecurityServicesRunning includes 1 when Credential Guard is running; verify current Microsoft documentation and policy state.
```

<b>What does it do?</b> Checks or changes Microsoft Defender or Windows security controls. Policy changes affect endpoint protection; confirm prerequisites and use an approved test system.

---

## 💻 Network Enumeration Commands

```shell
REM Map the network
net view                   REM Visible computers
net view /domain           REM Domain computers
nbtstat -r                 REM NetBIOS name-cache statistics
arp -a                     REM ARP cache (recently seen hosts)

REM Shares
net share                  REM Local shares
net use                    REM Connected shares

REM Domain users/groups (if domain-joined)
net user /domain
net group /domain
net group "Domain Admins" /domain
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

---

## 🧪 Lab — Authorized Enumeration Exercise

```powershell
# Authorized lab enumeration script
# Run this ONLY on your own lab VM
function Invoke-LabEnum {
    Write-Host "=== LAB ENUMERATION (AUTHORIZED ONLY) ==" -ForegroundColor Red

    Write-Host "`n[IDENTITY]" -ForegroundColor Yellow
    Write-Host "User: $(whoami)"
    Write-Host "Computer: $env:COMPUTERNAME"
    Write-Host "Domain: $env:USERDOMAIN"

    Write-Host "`n[LOCAL ADMINS]" -ForegroundColor Yellow
    Get-LocalGroupMember -Group "Administrators" -ErrorAction SilentlyContinue |
      Select Name, ObjectClass | Format-Table

    Write-Host "`n[NETWORK CONNECTIONS]" -ForegroundColor Yellow
    Get-NetTCPConnection -State Established |
      Select LocalPort, RemoteAddress, RemotePort, OwningProcess | Format-Table

    Write-Host "`n[STORED CREDENTIALS]" -ForegroundColor Yellow
    cmdkey /list 2>&1

    Write-Host "`n[ENVIRONMENT VARIABLES]" -ForegroundColor Yellow
    Get-ChildItem Env: | Select Name, Value | Format-Table
}

Invoke-LabEnum
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## ❓ Module 14 Quiz

<b>Q1:</b> What command lists stored Windows credentials?

> <code>cmdkey /list</code>

<b>Q2:</b> What is LSASS and why do attackers target it?

> LSASS (Local Security Authority Subsystem Service) is the process that handles Windows authentication. It caches credentials (passwords, hashes, Kerberos tickets) in memory, making it a high-value target for credential theft.

---

## ✅ Module 14 Summary

- Post-exploitation enumeration reveals what can be accessed and escalated
- Credential locations are predictable — defenders must harden them
- Network enumeration maps the attacker's potential lateral movement paths
- Always perform testing with written authorization ONLY
- Blue Team: monitor for enumeration commands (net user, net group, whoami /all)

> ➡️ <b>Next Module:</b> Windows for Blue Team (SOC)
</details>

<details>
<summary>MODULE 15 — WINDOWS FOR BLUE TEAM — SOC</summary>


<b>Use cases:</b> Triage endpoint activity, detect suspicious logons/processes and structure incident response and containment decisions.

> <b>Level:</b> Advanced \| <b>Track:</b> Blue Team (Primary)

---

## 🎯 Blue Team Mission

The Blue Team's job is to:

1. <b>Detect</b> attacks in progress
2. <b>Contain</b> the attacker before they spread
3. <b>Eradicate</b> malware and backdoors
4. <b>Recover</b> systems to known-good state
5. <b>Learn</b> from every incident

---

## 📊 SOC Monitoring Dashboard Concept

```javascript
┌──────────────────────────────────────────────═
│              SOC DETECTION MATRIX                  │
├─────────────┬─────────────┬────────────────────┤
│ Attack Phase │ Event IDs    │ Detection Rule           │
├─────────────┼─────────────┼────────────────────┤
│ Initial Access│ 4625, 4624  │ Brute force then success │
│ Execution     │ 4688, 4104  │ Office spawning PS/CMD   │
│ Persistence   │ 4698, 7045  │ New task/service         │
│ Escalation    │ 4672, 4624  │ Regular user gets admin  │
│ Lateral Move  │ 4624 T3/T10 │ Login chain 3+ machines  │
│ Exfiltration  │ 4663       │ Mass file access         │
└─────────────┴─────────────┴────────────────────┘
```

<b>What does it do?</b> Provides a visual reference for soc monitoring dashboard concept, showing the main components or relationships.

---

## 💻 SIEM Integration (Splunk Basics)

```javascript
# Brute force detection
index=windows EventCode=4625
| stats count by src_ip, Account_Name
| where count > 10
| sort -count

# Successful brute force
index=windows (EventCode=4625 OR EventCode=4624)
| sort _time
| where EventCode=4624
  AND [search index=windows EventCode=4625 | stats count by src_ip | where count > 5 | fields src_ip]

# Suspicious process creation
index=windows EventCode=4688
| where ParentImage like "%WINWORD%" OR ParentImage like "%EXCEL%"
  AND (Image like "%powershell%" OR Image like "%cmd%")
| table _time, Computer, User, ParentImage, Image, CommandLine

# Lateral movement
index=windows EventCode=4624 Logon_Type=3
| stats dc(Computer) as machines by Account_Name, Source_Network_Address
| where machines >= 3
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 📝 Incident Response Playbook

### Phase 1: Detection

```powershell
# Initial triage script
function Invoke-IncidentTriage {
    param([string]$OutputPath = "C:\IR_Triage_$(Get-Date -Format 'yyyyMMdd_HHmmss')")
    New-Item -ItemType Directory -Path $OutputPath -Force | Out-Null

    Write-Host "=== INCIDENT RESPONSE TRIAGE ==" -ForegroundColor Red
    Write-Host "Output: $OutputPath"

    # Collect running processes
    Get-CimInstance Win32_Process | Select ProcessId, ParentProcessId,
      Name, ExecutablePath, CommandLine |
      Export-Csv "$OutputPath\processes.csv" -NoTypeInformation

    # Collect network connections
    Get-NetTCPConnection | ForEach-Object {
        $proc = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
        [PSCustomObject]@{
            LocalPort = $_.LocalPort; RemoteAddress = $_.RemoteAddress
            RemotePort = $_.RemotePort; State = $_.State
            Process = $proc.Name; PID = $_.OwningProcess
        }
    } | Export-Csv "$OutputPath\network.csv" -NoTypeInformation

    # Collect recent security events
    Get-WinEvent -FilterHashtable @{
        LogName='Security'
        Id=4624,4625,4688,4698,4720,4726,4672
        StartTime=(Get-Date).AddHours(-4)
    } -ErrorAction SilentlyContinue |
    Select TimeCreated, Id, Message |
    Export-Csv "$OutputPath\security_events.csv" -NoTypeInformation

    # Check persistence
    Get-ItemProperty "HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" -ErrorAction SilentlyContinue |
      Export-Csv "$OutputPath\run_keys.csv" -NoTypeInformation

    Write-Host "Triage complete! Review: $OutputPath" -ForegroundColor Green
}

Invoke-IncidentTriage
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

### Phase 2: Containment

```powershell
# Quick containment actions. Review the impact and run with -WhatIf first.
function Invoke-QuickContainment {
    [CmdletBinding(SupportsShouldProcess)]
    param(
        [string]$AttackerIP,
        [string]$MaliciousProcess
    )

    if ($AttackerIP) {
        $outName = "BLOCK_C2_OUT_$AttackerIP"
        $inName  = "BLOCK_C2_IN_$AttackerIP"

        if ($PSCmdlet.ShouldProcess($AttackerIP, "Create inbound and outbound firewall block rules")) {
            New-NetFirewallRule -DisplayName $outName -Direction Outbound -RemoteAddress $AttackerIP -Action Block
            New-NetFirewallRule -DisplayName $inName -Direction Inbound -RemoteAddress $AttackerIP -Action Block
        }
    }

    if ($MaliciousProcess) {
        $targets = Get-Process -Name $MaliciousProcess -ErrorAction SilentlyContinue
        foreach ($target in $targets) {
            if ($PSCmdlet.ShouldProcess("$($target.ProcessName) (PID $($target.Id))", "Stop process")) {
                Stop-Process -Id $target.Id -Force
            }
        }
    }
}

# Preview changes first; remove -WhatIf only after approval and validation.
# Invoke-QuickContainment -AttackerIP "203.0.113.10" -MaliciousProcess "sample" -WhatIf
```

<b>What does it do?</b> Creates or removes a Windows Firewall rule. This changes network policy; use a disposable lab VM, verify the rule and remove it after testing.

---

## ❓ Module 15 Quiz

<b>Q1:</b> What are the 5 phases of incident response?

> Detection, Containment, Eradication, Recovery, and Lessons Learned (also called the IR lifecycle).

<b>Q2:</b> Write a Splunk query to detect brute force attacks.

> <code>index=windows EventCode=4625 | stats count by src_ip, Account_Name | where count > 10 | sort -count</code>

---

## ✅ Module 15 Summary

- Blue Team detects, contains, eradicates, and recovers from attacks
- SIEM correlates events across thousands of machines for enterprise detection
- Incident response follows a defined lifecycle: triage → contain → eradicate → recover
- Pre-built detection queries dramatically speed up SOC response time
- Document everything — evidence collection is critical for forensics

> ➡️ <b>Next Module:</b> Automation & Scripting for Security
</details>

<details>
<summary>MODULE 16 — AUTOMATION & SCRIPTING FOR SECURITY</summary>


<b>Use cases:</b> Automate repeatable security checks, alerts and daily reporting while handling prerequisites and script errors.

<blockquote>
<b>Level:</b> Advanced \| <b>Track:</b> Blue Team + Red Team 
</blockquote>

---

## 🎯 Why Automation Matters in Security

Manual analysis of thousands of events is impossible. Automation enables:

- Real-time threat detection
- Consistent, repeatable analysis
- Enterprise-scale coverage
- Faster response times

---

## 💚 PowerShell Security Automation

<h3>
Real-Time Alert System 
</h3>

```powershell
# Security alert monitor with local log notifications
function Start-SecurityAlertMonitor {
    param(
        [int]$CheckIntervalSeconds = 60,
        [int]$BruteForceThreshold = 5,
        [string]$LogPath = "C:\SecurityAlerts.log"
    )

    function Write-Alert {
        param([string]$Message, [string]$Severity = "HIGH")
        $entry = "[$((Get-Date).ToString('yyyy-MM-dd HH:mm:ss'))] [$Severity] $Message"
        Add-Content -Path $LogPath -Value $entry
        $color = switch ($Severity) {
            "CRITICAL" { "Red" }
            "HIGH" { "Yellow" }
            default { "White" }
        }
        Write-Host $entry -ForegroundColor $color
    }

    Write-Host "Security Monitor Active. Log: $LogPath" -ForegroundColor Green
    Write-Host "Checking every $CheckIntervalSeconds seconds. Ctrl+C to stop.`n"

    $lastCheck = Get-Date

    while ($true) {
        $now = Get-Date

        # Check 1: Brute force
        $failures = Get-WinEvent -FilterHashtable @{
            LogName='Security'; Id=4625; StartTime=$lastCheck
        } -ErrorAction SilentlyContinue

        if ($failures.Count -ge $BruteForceThreshold) {
            Write-Alert "Brute force: $($failures.Count) failures since last check" "CRITICAL"
        }

        # Check 2: Suspicious processes
        $suspProcs = Get-CimInstance Win32_Process | Where-Object {
            $_.ExecutablePath -like "*Temp*" -or $_.ExecutablePath -like "*AppData\Local\Temp*"
        }
        if ($suspProcs) {
            $names = ($suspProcs | Select -ExpandProperty Name) -join ', '
            Write-Alert "Suspicious processes in Temp: $names" "HIGH"
        }

        # Check 3: New admin accounts
        $newUsers = Get-WinEvent -FilterHashtable @{
            LogName='Security'; Id=4720; StartTime=$lastCheck
        } -ErrorAction SilentlyContinue
        if ($newUsers) {
            Write-Alert "New user accounts created: $($newUsers.Count)" "HIGH"
        }

        # Check 4: Log cleared
        $logCleared = Get-WinEvent -FilterHashtable @{
            LogName='Security'; Id=1102; StartTime=$lastCheck
        } -ErrorAction SilentlyContinue
        if ($logCleared) {
            Write-Alert "AUDIT LOG CLEARED! Immediate investigation required!" "CRITICAL"
        }

        $lastCheck = $now
        Start-Sleep -Seconds $CheckIntervalSeconds
    }
}

# Start monitor
Start-SecurityAlertMonitor -CheckIntervalSeconds 30 -BruteForceThreshold 3
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 📊 Automated Daily Security Report

```powershell
function New-DailySecurityReport {
    $report = "C:\SecurityReport_$(Get-Date -Format 'yyyy-MM-dd').txt"
    $yesterday = (Get-Date).AddDays(-1)

    @"
===============================================
    DAILY SECURITY REPORT
    Generated: $(Get-Date)
    Computer: $env:COMPUTERNAME
===============================================
"@ | Out-File $report

    # Failed logins
    $failures = (Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4625; StartTime=$yesterday
    } -EA SilentlyContinue).Count
    Add-Content $report "Failed Logins (24h): $failures"

    # Successful logins
    $success = (Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4624; StartTime=$yesterday
    } -EA SilentlyContinue).Count
    Add-Content $report "Successful Logins (24h): $success"

    # New accounts
    $newAccts = (Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4720; StartTime=$yesterday
    } -EA SilentlyContinue).Count
    Add-Content $report "New User Accounts Created: $newAccts"

    # Running processes count
    Add-Content $report "Running Processes: $((Get-Process).Count)"
    Add-Content $report "Active Network Connections: $((Get-NetTCPConnection -State Established).Count)"

    Write-Host "Report saved: $report" -ForegroundColor Green
    Get-Content $report
}

New-DailySecurityReport
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## ❓ Module 16 Quiz

<b>Q1:</b> Why is Event ID 1102 a critical alert trigger?

> Event 1102 means the Security audit log was cleared. Attackers clear logs to remove evidence. It should always trigger an immediate P1 investigation.

<b>Q2:</b> How do you schedule a PowerShell security script to run automatically?

> Use <code>schtasks</code> or <code>Register-ScheduledTask</code> to create a scheduled task that runs the script at a defined interval.

---

## ✅ Module 16 Summary

- Automation enables enterprise-scale security monitoring impossible to do manually
- Real-time monitoring catches attacks as they happen, not days later
- Daily security reports provide baseline awareness and trend detection
- Always log alerts with timestamps for forensic trail
- Scheduled tasks can run monitoring scripts continuously

> ➡️ <b>Next Module:</b> Real-World Scenarios
</details>

<details>
<summary>MODULE 17 — REAL-WORLD SCENARIOS</summary>


<b>Use cases:</b> Correlate Windows evidence from phishing, RDP brute-force and reverse-shell scenarios; focus on detection and response.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team (understand) + Blue Team (detect & respond)

---

## 🔴 Scenario 1: Simulated Phishing-Related Execution

### What the Attacker Did

```javascript
STEP 1: Send phishing email
  "Your invoice is attached — please review"
  Attachment: Invoice_March2024.docx (contains malicious macro)

STEP 2: User opens document, enables macros
  Macro executes: powershell -enc [base64 encoded downloader]

STEP 3: PowerShell downloads and executes payload
  payload.exe dropped to C:\Users\victim\AppData\Local\Temp\

STEP 4: Payload establishes C2 connection
  Connects to: 185.220.101.45:4444 every 30 seconds

STEP 5: Attacker gets interactive shell
  Runs: whoami, net user, ipconfig
  Dumps credentials, moves laterally
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

### Windows Event Evidence Trail

```javascript
[14:23:01] Event 4688: WINWORD.EXE started by explorer.exe
[14:23:45] Event 4688: powershell.exe started by WINWORD.EXE  ← RED FLAG!
[14:23:45] Event 4104: PowerShell script block logged (encoded command)
[14:23:47] Sysmon 3:  powershell.exe → 185.220.101.45:4444     ← C2 TRAFFIC!
[14:23:48] Sysmon 11: payload.exe created in AppData\Temp      ← MALWARE DROPPED!
[14:23:49] Event 4688: payload.exe started by powershell.exe
[14:24:01] Event 4688: whoami.exe, net.exe (enumeration begins)
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for windows event evidence trail; correlate actions, telemetry and response decisions rather than treating it as an executable command.

### Blue Team Detection

```powershell
# Detect macro malware execution
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} |
  Where-Object {
    $_.Message -like '*WINWORD*' -or $_.Message -like '*EXCEL*'
  } | ForEach-Object {
    # Check if Office spawned suspicious child processes
    if ($_.Message -like '*powershell*' -or $_.Message -like '*cmd.exe*') {
      Write-Host "ALERT: Office application spawned shell!" -ForegroundColor Red
      $_ | Format-List TimeCreated, Message
    }
  }
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

### Blue Team Response Steps

```javascript
1. ISOLATE: Disconnect machine from network immediately
2. IDENTIFY: What process is communicating out? (netstat -ano)
3. BLOCK: Add firewall rule to block C2 IP
4. COLLECT: Image memory before killing processes (volatility)
5. ANALYZE: Check all persistence mechanisms
6. ERADICATE: Remove malware, clean persistence
7. RECOVER: Restore from clean backup if needed
8. REPORT: Document full incident timeline
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## 🔴 Scenario 2: RDP Brute-Force Investigation

### What the Attacker Did

```javascript
STEP 1: Scan internet for port 3389 (RDP)
  Tool: Shodan, masscan, nmap
  Found: company-server.com with RDP exposed

STEP 2: Brute force attack begins
  Tool: Crowbar or Hydra
  Target: administrator account
  Rate: 100 attempts/minute over 3 hours

STEP 3: Success! administrator:Company2022! found
  Logged in via RDP at 02:14 AM

STEP 4: Attacker has full admin access
  Disables AV, installs tools
  Creates backdoor account
  Deploys ransomware
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for what the attacker did; correlate actions, telemetry and response decisions rather than treating it as an executable command.

### Windows Event Evidence

```javascript
[02:00:00 - 02:14:00] 1,847x Event 4625 (failed login)
  Account: administrator
  Logon Type: 10 (RDP)
  Source IP: 185.220.0.1

[02:14:01] Event 4624 (SUCCESS!)
  Account: administrator  
  Logon Type: 10 (RDP)
  Source IP: 185.220.0.1

[02:14:02] Event 4672 (Admin privileges assigned)
[02:14:45] Event 7036 (Security service stopped!)
[02:15:00] Event 4720 (New user: svc_backup99 created)
[02:15:01] Event 4732 (svc_backup99 added to the local Administrators group)
[02:20:00] Event 4688 (ransomware.exe executed)
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for windows event evidence; correlate actions, telemetry and response decisions rather than treating it as an executable command.

### Detection Query

```powershell
# Complete brute force to ransomware detection
function Detect-RDPBruteForce {
    $window = (Get-Date).AddHours(-1)

    $failures = Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4625; StartTime=$window
    } -EA SilentlyContinue | Where-Object {$_.Message -like '*Logon Type:*10*'}

    $successes = Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4624; StartTime=$window
    } -EA SilentlyContinue | Where-Object {$_.Message -like '*Logon Type:*10*'}

    Write-Host "RDP Failed Logins (1h): $($failures.Count)" -ForegroundColor $(if($failures.Count -gt 10){'Red'}else{'Green'})
    Write-Host "RDP Successful Logins (1h): $($successes.Count)" -ForegroundColor $(if($successes.Count -gt 0){'Yellow'}else{'Green'})

    if ($failures.Count -gt 20 -and $successes.Count -gt 0) {
        Write-Host "`nPossible RDP attack pattern: many failures and a success occurred in the same time window. Correlate source IP, account and logon details before concluding." -ForegroundColor Red
        Write-Host "Immediate incident response required!" -ForegroundColor Red
    }
}

Detect-RDPBruteForce
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 🔴 Scenario 3: Reverse-Shell Detection

### What the Attacker Did

```javascript
STEP 1: Exploit web vulnerability on DMZ server
STEP 2: Upload webshell to web server
STEP 3: Execute reverse shell from webshell:
  powershell -nop -w hidden -enc [encoded reverse shell to 185.220.0.5:443]
STEP 4: Attacker receives shell on port 443 (looks like HTTPS!)
STEP 5: Now has interactive access to internal network
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for what the attacker did; correlate actions, telemetry and response decisions rather than treating it as an executable command.

### Detection Strategy

```powershell
# Reverse shell detection
function Find-ReverseShell {
    # PowerShell processes making outbound connections
    $suspConnections = Get-NetTCPConnection -State Established | ForEach-Object {
        $proc = Get-Process -Id $_.OwningProcess -EA SilentlyContinue
        if ($proc.Name -in @('powershell','cmd','bash','sh')) {
            [PSCustomObject]@{
                Process = $proc.Name
                PID = $_.OwningProcess
                RemoteIP = $_.RemoteAddress
                Port = $_.RemotePort
                Suspicious = $true
            }
        }
    }

    if ($suspConnections) {
        Write-Host "ALERT: Shell process with network connection detected!" -ForegroundColor Red
        $suspConnections | Format-Table
    }
}

Find-ReverseShell
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

---

## ❓ Module 17 Quiz

<b>Q1:</b> What event sequence indicates a successful RDP brute force?

> Multiple Event 4625 (Logon Type 10) failures from the same IP, followed by a single Event 4624 (Logon Type 10) success from that IP.

<b>Q2:</b> What is the first thing you should do when you detect active malware?

> Isolate the machine from the network (disconnect network cable or disable NIC) to stop C2 communication and prevent lateral movement, before collecting forensic evidence.

---

## ✅ Module 17 Summary

- Phishing → macro → PowerShell → C2 is the most common enterprise attack chain
- RDP brute force → ransomware is the most damaging attack pattern
- Event logs tell the complete story — every attack step leaves evidence
- Isolate first, investigate second — stop the bleeding before forensics
- Reverse shells on port 443 blend in with HTTPS traffic — inspect by process, not port

> ➡️ <b>Next Module:</b> Tools & Practical Setup
</details>

<details>
<summary>MODULE 18 — TOOLS & PRACTICAL LAB SETUP</summary>


<b>Use cases:</b> Build a safe Windows telemetry lab with Sysmon, Splunk and Wireshark, then validate event collection and searches.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team + Blue Team

---

## 🎯 Lab Architecture

```javascript
┌──────────────────────────────────────────────────┐
│              YOUR HOST MACHINE                       │
│                                                       │
│  ┌────────────┐   ┌────────────┐   ┌──────────┐   │
│  │ Windows 10 │   │   Splunk    │   │ Kali Linux │   │
│  │    VM      │   │    VM      │   │    VM     │   │
│  │ + Sysmon  │   │ (Free 60d) │   │ (attacker) │   │
│  │ + Winlogbeat│   │            │   │            │   │
│  └────────────┘   └────────────┘   └──────────┘   │
│       ↑ Logs forwarded            ↑ Lab attacks     │
└──────────────────────────────────────────────────┘
```

<b>What does it do?</b> Provides a visual reference for lab architecture, showing the main components or relationships.

---

## ⚙️ Sysmon Setup

```shell
REM Step 1: Download from Microsoft Sysinternals
REM https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

REM Step 2: Install using a reviewed, version-compatible Sysmon configuration.
REM Validate the configuration and event volume before deploying it beyond the lab.
powershell -command "Invoke-WebRequest -Uri 'https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml' -OutFile C:\Tools\sysmonconfig.xml"

REM Step 3: Install Sysmon
Sysmon64.exe -i C:\Tools\sysmonconfig.xml -accepteula

REM Step 4: Verify
sc query Sysmon64
```

<b>What does it do?</b> Downloads and installs Sysmon with the selected configuration, then checks whether the Sysmon service is present.

```powershell
# Verify Sysmon logs
Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' -MaxEvents 5 | Format-List
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 📊 Splunk Setup

```javascript
Step 1: Download a currently supported Splunk Enterprise build and review its current license terms
  https://www.splunk.com/en_us/download/splunk-enterprise.html

Step 2: Install on Windows VM
  Default port: 8000
  Access: http://localhost:8000

Step 3: Install Splunk Universal Forwarder on target machine
  https://www.splunk.com/en_us/download/universal-forwarder.html

Step 4: Configure forwarder to send to Splunk
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for splunk setup; correlate actions, telemetry and response decisions rather than treating it as an executable command.

```shell
REM Configure the forwarder to send data to the Splunk receiver.
"C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe" add forward-server 192.168.1.100:9997
"C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe" restart
```

<b>What does it do?</b> Registers the Splunk receiver and restarts the Universal Forwarder so it can begin forwarding the configured event data.

```txt
# Example inputs.conf — collect selected Windows Event Log channels.
# Place in %SPLUNK_HOME%\etc\system\local\inputs.conf or a managed app.
[WinEventLog://Security]
disabled = 0
index = windows

[WinEventLog://System]
disabled = 0
index = windows

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = windows

[WinEventLog://Microsoft-Windows-PowerShell/Operational]
disabled = 0
index = windows
```

<b>What does it do?</b> Configures the Universal Forwarder to subscribe to named Windows Event Log channels. Confirm the receiver, index, permissions and deployment method in your Splunk environment before restarting the service.

### Splunk Key Searches for Lab

```javascript
# All Windows events
index=windows

# Sysmon events
index=windows source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"

# Security events only
index=windows source="WinEventLog:Security"

# Failed logins
index=windows EventCode=4625 | stats count by Account_Name

# Process creation with command line
index=windows EventCode=4688 | table _time, Computer, Account_Name, New_Process_Name, Process_Command_Line
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 🔭 Wireshark Basics for Windows

```javascript
Capture Filters (before capture):
  tcp port 4444           - Capture only port 4444 traffic
  host 192.168.1.100      - Capture traffic to/from specific IP
  tcp port 3389           - RDP traffic

Display Filters (during/after capture):
  tcp.port == 4444        - Show C2 traffic
  http                    - HTTP traffic only
  dns                     - DNS queries only
  ip.addr == 185.220.0.1  - Traffic to/from suspicious IP
  tcp.flags.syn == 1      - SYN packets (port scans)
```

<b>What does it do?</b> Provides a quick reference for wireshark basics for windows; use the surrounding explanation for context and prerequisites.

---

## 📚 Essential Tool List

| Tool | Purpose | Free? | Download |
| --- | --- | --- | --- |
| <b>Sysmon</b> | Advanced endpoint logging | ✅ Free | Sysinternals |
| <b>Splunk</b> | SIEM platform | ✅ Trial | [splunk.com](http://splunk.com) |
| <b>Wireshark</b> | Network packet analysis | ✅ Free | [wireshark.org](http://wireshark.org) |
| <b>Process Explorer</b> | Enhanced task manager | ✅ Free | Sysinternals |
| <b>Autoruns</b> | Autostart analysis | ✅ Free | Sysinternals |
| <b>Procmon</b> | System activity monitor | ✅ Free | Sysinternals |
| <b>Wazuh</b> | Open source SIEM/EDR | ✅ Free | [wazuh.com](http://wazuh.com) |
| <b>Volatility</b> | Memory forensics | ✅ Free | [github.com/volatilityfoundation](http://github.com/volatilityfoundation) |
| <b>x64dbg</b> | Debugger for malware analysis | ✅ Free | [x64dbg.com](http://x64dbg.com) |

---

## 🏆 Final Project: SOC Lab + Attack Simulation

### Project Requirements

<b>Setup Phase:</b>

1. Build Windows 10 VM with Sysmon (SwiftOnSecurity config)
2. Configure PowerShell Script Block Logging
3. Install Splunk and forward Windows logs

<b>Attack Simulation Phase:</b>

1. Simulate a brute force attack (generate 10+ failed logins)
2. Create a backdoor user account
3. Add a persistence Run key
4. Simulate a suspicious process chain (CMD → PowerShell)

<b>Detection Phase:</b>

1. Write Splunk queries to detect each attack step
2. Write PowerShell scripts to automate detection
3. Create a timeline of the attack from logs

<b>Report Phase:</b>

1. Write an incident report including:
   - Attack timeline
   - Indicators of Compromise (IOCs)
   - Evidence (log entries, screenshots)
   - Remediation steps taken
   - Lessons learned

---

## ❓ Module 18 Quiz

<b>Q1:</b> What is Winlogbeat and why use it with Splunk?

> Winlogbeat is an Elastic Beat (lightweight forwarder) that ships Windows Event Logs to Elasticsearch or Splunk. It's a free alternative to Splunk Universal Forwarder for sending Windows logs to a centralized SIEM.

<b>Q2:</b> What Wireshark display filter shows only DNS traffic?

> <code>dns</code>

---

## ✅ Module 18 Summary

- A complete SOC lab needs: Windows VM + Sysmon + SIEM + Packet capture
- Sysmon with a reviewed configuration provides process, network and image-load telemetry when those event types are enabled
- Splunk licensing and trial availability vary; check current terms before building the lab
- Wireshark captures network evidence that supplements log analysis
- The final project combines every skill learned in the course into a real SOC simulation

---

## Course skills covered

This module completes the lab setup. The remaining modules cover:

- Windows internals, persistence and privilege escalation
- Post-access enumeration and incident response
- PowerShell automation and event-log analysis
- Sysmon, Splunk and network investigation
- Scenario-based detection and the final SOC capstone

Assess proficiency through successful lab execution, accurate interpretation of evidence and clear reporting—not by assigning an unsupported skill level.
</details>

<details>
<summary>MODULE 19 — AI-ERA THREATS & MODERN DEFENSES</summary>


<b>Use cases:</b> Understand AI-enabled threat patterns and use defensive analytics/Sentinel KQL to triage suspicious activity.

> <b>Level:</b> Advanced \| <b>Estimated Time:</b> 90 minutes \| <b>Track:</b> Red Team + Blue Team \| <b>Added:</b> March 2026

> ⚠️ <b>CRITICAL: Only perform these techniques in authorized lab environments with explicit written permission. Unauthorized use is illegal.</b>

---

## 🎯 Learning Objectives

- Understand how attackers use AI/LLM tools to enhance attacks on Windows systems
- Recognize AI-generated phishing, deepfake-assisted social engineering, and AI-powered malware
- Use Microsoft Copilot for Security and AI-powered SIEM to detect modern threats
- Harden Windows environments against AI-assisted attacks

---

## 🧠 How Attackers Use AI (Red Team Awareness)

### AI-Assisted Reconnaissance

Modern threat actors use LLMs to automate and scale reconnaissance:

```powershell
# What attackers automate with AI:
# 1. OSINT aggregation (LinkedIn, GitHub, job postings)
# 2. Generating convincing spear-phishing emails
# 3. Writing custom malware variants to evade signature detection
# 4. Analyzing stolen data at scale
# 5. Automating vulnerability research
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for ai assisted reconnaissance; correlate actions, telemetry and response decisions rather than treating it as an executable command.

### AI-Generated Phishing (2026 Landscape)

| Old Phishing | AI-Assisted Phishing |
| --- | --- |
| Generic templates | Hyper-personalized per target |
| Grammar mistakes | Flawless, context-aware writing |
| Static lures | Dynamic content from victim's social media |
| One email blast | Ongoing conversation simulations |

> <b>Blue Team:</b> Grammar and template checks alone are insufficient. Combine sender authentication, URL and attachment analysis, identity signals and user-reported context.

### AI-Related Malware Claims — Defensive Context

```javascript
Possible or claimed AI-assisted patterns (not guaranteed capabilities):
─────────────────────────────────────────────────────────
1. Faster variant generation       → May increase sample volume; not guaranteed evasion
2. Environment-aware behaviour     → Can also be implemented without AI
3. Traffic adaptation               → Must be evaluated with network and endpoint evidence
4. Workflow assistance              → Human operators still validate targets and actions
```

<b>What does it do?</b> Provides a visual reference for ai powered malware awareness, showing the main components or relationships.

---

## 🛡️ Blue Team: AI-Powered Detection

### Microsoft Copilot for Security

Microsoft Sentinel and Copilot for Security can support investigation workflows; exact features depend on licensing, configuration and service updates:

```powershell
# Connect Microsoft Sentinel workspace
# (Run in Azure Cloud Shell or PowerShell with Az module)

# Check Sentinel workspace
Get-AzOperationalInsightsWorkspace -ResourceGroupName "SOC-RG"

# Query recent AI-flagged incidents via KQL in Sentinel:
# SecurityIncident
# | where TimeGenerated > ago(24h)
# | where ProviderName == "Microsoft Sentinel"
# | where Severity in ("High", "Medium")
# | project Title, Description, Status, Severity, CreatedTime
# | order by CreatedTime desc
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

### KQL Queries for AI-Era Threat Detection

```sql
// Geographic variance triage — multiple locations are not proof of impossible travel.
SigninLogs
| where TimeGenerated > ago(1h)
| summarize Locations = make_set(Location), IPAddresses = make_set(IPAddress)
    by UserPrincipalName
| where array_length(Locations) > 1
| project UserPrincipalName, Locations, IPAddresses

// High-volume sender triage. Tune the threshold to your tenant baseline.
EmailEvents
| where Timestamp > ago(1h)
| summarize EmailCount = count() by SenderFromAddress
| where EmailCount > 50
| order by EmailCount desc

// PowerShell command-line triage in Defender XDR Advanced Hunting.
DeviceProcessEvents
| where Timestamp > ago(24h)
| where FileName in~ ("powershell.exe", "pwsh.exe")
| where ProcessCommandLine has_any ("-EncodedCommand", "Invoke-Expression", "DownloadString")
| project Timestamp, DeviceName, AccountName, FileName,
          ProcessCommandLine, InitiatingProcessFileName
| order by Timestamp desc
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

### Windows Defender XDR (2026 Features)

```powershell
# Check Defender for Endpoint AI protection status
Get-MpPreference | Select-Object `
    CloudBlockLevel,
    CloudExtendedTimeout,
    EnableNetworkProtection,
    PUAProtection

# Enable maximum AI-cloud protection
Set-MpPreference -CloudBlockLevel 6  # ZeroTolerance
Set-MpPreference -CloudExtendedTimeout 50
Set-MpPreference -EnableNetworkProtection Enabled
```

<b>What does it do?</b> Checks or changes Microsoft Defender or Windows security controls. Policy changes affect endpoint protection; confirm prerequisites and use an approved test system.

---

## 🔴 Red Team: Modern C2 Frameworks (Lab Only)

> For educational awareness — understanding attacker tools helps Blue Teams detect them.

### Sliver C2 — defensive awareness

```javascript
Sliver C2 Framework — Defender Awareness
─────────────────────────────────────────
Feature              | What to Detect
---------------------|------------------------------------------
HTTPS C2             | Unusual SSL certs, rare domains, beaconing
DNS tunneling        | High DNS query volume, long TXT records
WireGuard pivots     | Unusual UDP traffic on port 51820
Stager generation    | Shellcode injected into legit processes
```

<b>What does it do?</b> Checks Defender preferences or Windows security-feature status so you can assess endpoint protection.

### Blue Team: Triage Repeated Sliver/Havoc-Like Connections

```sql
// Repeated-connection triage. This finds frequency, not periodic timing.
DeviceNetworkEvents
| where Timestamp > ago(1h)
| summarize ConnectionCount = count(),
            FirstSeen = min(Timestamp),
            LastSeen = max(Timestamp)
    by DeviceName, InitiatingProcessFileName, RemoteIP, RemotePort
| where ConnectionCount > 10
| order by ConnectionCount desc
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 🧪 Lab — AI Threat Simulation (Authorized Lab Only)

### Lab Setup Requirements

- A supported Windows client VM with current updates and a clean snapshot
- Microsoft Sentinel (free trial or Azure student account)
- Sysmon with SwiftOnSecurity config
- Optional: Defender for Endpoint trial

### Lab 1 — Simulate and Detect Encoded PowerShell

```powershell
# RED: Simulate suspicious encoded command (your lab VM only)
$cmd = "Write-Host 'Simulated attacker payload'"
$encoded = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($cmd))
powershell.exe -EncodedCommand $encoded

# BLUE: Detect it in Windows Event Log
Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" |
  Where-Object { $_.Message -like "*EncodedCommand*" } |
  Select-Object TimeCreated, Message |
  Format-List
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

### Lab 2 — Sysmon: Detect Process Injection Patterns

```powershell
# Check Sysmon Event ID 8 (CreateRemoteThread) — classic injection indicator
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" |
  Where-Object { $_.Id -eq 8 } |
  Select-Object TimeCreated, Message |
  Format-List

# Check Sysmon Event ID 10 (ProcessAccess) — LSASS access attempts
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" |
  Where-Object { $_.Id -eq 10 -and $_.Message -like "*lsass*" } |
  Select-Object TimeCreated, Message
```

<b>What does it do?</b> Queries event or SIEM telemetry for the activity shown in the filter. Confirm the log source, audit policy, field names and time range before relying on the results.

---

## 🏋️ Hardening Windows Against AI-Era Threats

```powershell
# 1. Enable Attack Surface Reduction (ASR) rules
# Block Office apps from creating child processes (stops macro malware)
Add-MpPreference -AttackSurfaceReductionRules_Ids D4F940AB-401B-4EFC-AADC-AD5F3C50688A `
                 -AttackSurfaceReductionRules_Actions Enabled

# Block credential stealing from LSASS
Add-MpPreference -AttackSurfaceReductionRules_Ids 9E6C4E1F-7D60-472F-BA1A-A39EF669E4B0 `
                 -AttackSurfaceReductionRules_Actions Enabled

# 2. Enable Windows LAPS (cloud-backed, 2023+)
# Prevents lateral movement with local admin reuse
Get-LapsAADPassword -DeviceIds (Get-AzureADDevice -SearchString $env:COMPUTERNAME).ObjectId

# 3. Enable Smart App Control (Windows 11 only)
# Blocks untrusted/unsigned apps including AI-generated malware
# Settings > Privacy & Security > Windows Security > App & Browser Control > Smart App Control

# 4. Verify Vulnerable Driver Blocklist is enabled
(Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\CI\Config').VulnerableDriverBlocklistEnable
# Should return: 1
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## ❓ Module 19 Quiz

<b>Q1:</b> What makes AI-generated phishing harder to detect than traditional phishing?

> AI phishing is hyper-personalized using OSINT, grammatically perfect, and can simulate ongoing conversations — rendering template and grammar-based email filters ineffective.

<b>Q2:</b> What KQL event would you query to detect LSASS access attempts in Sysmon?

> Sysmon Event ID 10 (ProcessAccess) filtered for messages containing "lsass".

<b>Q3:</b> What is Smart App Control and why does it matter for AI-era malware?

> A Windows 11 feature that blocks execution of unsigned or untrusted applications. Since AI can generate novel malware binaries that evade signature detection, requiring app trust (via certificates or ML reputation) provides a behavioral baseline defense.

<b>Q4:</b> Name two modern C2 frameworks replacing Cobalt Strike in 2026.

> Sliver and Havoc Framework — both open-source and actively used in red team engagements.

---

## ✅ Module 19 Summary

- AI has fundamentally changed the threat landscape — attacks are faster, more personalized, and harder to detect with legacy tools
- Microsoft Copilot for Security + Sentinel provides AI-powered detection to match AI-powered attacks
- KQL is essential for hunting modern C2 beaconing, credential access, and lateral movement
- ASR rules, Smart App Control, and Windows LAPS v2 are the 2026 hardening baseline
- Defenders must think like AI-augmented attackers to stay ahead

> <b>Next:</b> Review the supplementary deep dives, then complete Module 23 — Final SOC Lab Capstone.
</details>

<details>
<summary>SUPPLEMENTARY MODULE 20 — PERSISTENCE TECHNIQUES — COMPLETE DEEP GUIDE</summary>


<b>Use cases:</b> Detailed reference for detecting and auditing registry, startup, task, service, WMI and DLL-related persistence indicators.

> <b>Why this supplement is retained:</b> The source division contains a second, more detailed version of this topic. This material is preserved so no source commands, scripts, comments or examples are lost. Compare overlapping procedures with the main module and use the clearer/corrected explanation as the primary study path.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team (awareness) + Blue Team (detection) \| <b>Course:</b> Windows Cybersecurity

> ⚠️ <b>Lab environments ONLY. For educational and defense awareness purposes.</b>

---

## 🎯 What You Will Learn

- 8+ Windows persistence mechanisms with full detail
- Red Team: How each technique is implemented
- Blue Team: How to detect and remediate each one
- Sysmon event IDs for each mechanism
- Full persistence sweep automation

---

## 📍 Persistence Technique 1: Registry Run Keys

### Red Team Implementation

```shell
REM User-level (no admin)
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "LegitService" /t REG_SZ /d "C:\Temp\malware.exe" /f

REM System-level (admin required)
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "SystemHelper" /t REG_SZ /d "C:\Windows\Temp\svc.exe" /f
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

### Blue Team Detection

```powershell
# Detect non-standard Run key entries
@('HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run',
  'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run') | ForEach-Object {
    $key = $_
    Get-ItemProperty $key -EA SilentlyContinue | Select-Object * -ExcludeProperty PS* |
    Get-Member -MemberType NoteProperty | ForEach-Object {
        $val = (Get-ItemProperty $key).$($_.Name)
        $exe = $val -split '"?' -replace '^\s+','' | Select-Object -First 1
        $signed = (Get-AuthenticodeSignature $exe -EA SilentlyContinue).Status
        [PSCustomObject]@{ Key=$key.Split('\')[-2]; Name=$_.Name; Value=$val; Signed=$signed }
    }
} | Format-Table -AutoSize
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## 📍 Persistence Technique 2: Startup Folder

```powershell
# Red Team: Drop shortcut in startup folder
$startup = "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup"
$wsh = New-Object -ComObject WScript.Shell
$shortcut = $wsh.CreateShortcut("$startup\UpdateService.lnk")
$shortcut.TargetPath = 'C:\Temp\malware.exe'
$shortcut.Save()

# Blue Team: Detect
Get-ChildItem "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup" -Force
Get-ChildItem 'C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup' -Force
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## 📍 Persistence Technique 3: Scheduled Tasks

```shell
REM Red Team: Create stealthy scheduled task
schtasks /create /tn "\Microsoft\Windows\UpdateOrchestrator\Reboot" ^  
  /tr "C:\Temp\payload.exe" /sc onlogon /ru SYSTEM /f

REM Query all tasks
schtasks /query /fo CSV /v > C:\all_tasks.csv

REM Blue Team: Find suspicious tasks
schtasks /query /fo LIST /v | findstr /i /C:"Task To Run" /C:"Temp" /C:"AppData"
```

<b>What does it do?</b> Creates or inspects scheduled-task persistence. Task creation changes the host and may require elevation; use a lab task and delete it after the exercise.

```powershell
# PowerShell: Comprehensive task auditor
Get-ScheduledTask | Where-Object { $_.State -ne 'Disabled' } | ForEach-Object {
    $task   = $_
    $action = $task.Actions | Select-Object -First 1
    $trigger = $task.Triggers | Select-Object -First 1
    [PSCustomObject]@{
        Name     = $task.TaskName
        Execute  = $action.Execute
        Args     = $action.Arguments
        Trigger  = $trigger.GetType().Name
        RunLevel = $task.Principal.RunLevel
        Risky    = $action.Execute -like '*Temp*' -or $action.Execute -like '*AppData*' -or
                   $action.Execute -like '*Public*' -or $action.Execute -like '*Downloads*'
    }
} | Where-Object { $_.Risky } | Format-Table -AutoSize
```

<b>What does it do?</b> Enumerates scheduled tasks and reviews their actions, triggers and run-as identities for unexpected configuration.

---

## 📍 Persistence Technique 4: Windows Services

```shell
REM Red Team: Create malicious service
sc create MalSvc binPath= "C:\Temp\svc.exe" start= auto DisplayName= "Windows Helper Service"
sc description MalSvc "Provides system optimization services"
sc start MalSvc

REM Blue Team: Find non-standard services
sc query type= all state= all | findstr "SERVICE_NAME"
powershell -NoProfile -Command "Get-CimInstance Win32_Service | Select-Object Name,PathName,StartMode"
```

<b>What does it do?</b> Inspects or changes Windows service configuration and state. Start, stop, create and delete operations modify the host; use a clearly identified lab service.

```powershell
# Comprehensive service audit
Get-CimInstance Win32_Service | ForEach-Object {
    [PSCustomObject]@{
        Name        = $_.Name
        DisplayName = $_.DisplayName
        Path        = $_.PathName
        StartMode   = $_.StartMode
        State       = $_.State
        StartAs     = $_.StartName
        Suspicious  = $_.PathName -and (
            $_.PathName -notlike '*\Windows\*' -and
            $_.PathName -notlike '*\Program Files*' -and
            $_.PathName -notlike '*\Program Files (x86)*'
        )
    }
} | Where-Object { $_.Suspicious } | Format-Table Name, Path, StartMode, State, StartAs -AutoSize
```

<b>What does it do?</b> Lists services and inspects their state, startup mode, executable path and run-as account.

---

## 📍 Persistence Technique 5: WMI Event Subscriptions

```powershell
# Red Team: Create WMI persistence (Lab VM only!).
# Compatibility: legacy WMI cmdlets below require Windows PowerShell 5.1; they are not included by default in PowerShell 7.
# Step 1: Filter (WHEN) - on system idle
$filter = Set-WmiInstance -Namespace root\subscription -Class __EventFilter `
  -Arguments @{
    Name          = 'SystemIdleFilter'
    QueryLanguage = 'WQL'
    Query         = 'SELECT * FROM Win32_ProcessStartTrace WHERE ProcessName="notepad.exe"'
  }

# Step 2: Consumer (WHAT to do)
$consumer = Set-WmiInstance -Namespace root\subscription -Class CommandLineEventConsumer `
  -Arguments @{
    Name             = 'LaunchMalware'
    CommandLineTemplate = 'C:\Temp\payload.exe'
  }

# Step 3: Binding (connect them)
$binding = Set-WmiInstance -Namespace root\subscription -Class __FilterToConsumerBinding `
  -Arguments @{ Filter = $filter; Consumer = $consumer }

# Blue Team: Detect ALL WMI subscriptions
Write-Host 'WMI Event Filters:'
Get-WMIObject -Namespace root\subscription -Class __EventFilter |
    Select Name, Query | Format-Table

Write-Host 'WMI Consumers:'
Get-WMIObject -Namespace root\subscription -Class __EventConsumer |
    Select Name, CommandLineTemplate, ScriptText | Format-Table

# Cleanup (if lab)
Get-WMIObject -Namespace root\subscription -Class __EventFilter |
    Where-Object Name -eq 'SystemIdleFilter' | Remove-WmiObject
Get-WMIObject -Namespace root\subscription -Class CommandLineEventConsumer |
    Where-Object Name -eq 'LaunchMalware' | Remove-WmiObject
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

---

## 📍 Persistence Technique 6: DLL Hijacking

```javascript
DLL Search Order for Persistence:
1. App's own directory          ← PLANT HERE!
2. C:\Windows\System32
3. C:\Windows\System
4. C:\Windows
5. Current directory
6. Directories in %PATH%

Example: If app.exe in C:\Program Files\App\ uses version.dll,
         place malicious version.dll in C:\Program Files\App\
         → App loads malware on every start!
```

<b>What does it do?</b> Provides a visual reference for persistence technique 6 dll hijacking, showing the main components or relationships.

```shell
REM Find DLL hijacking opportunities (Blue Team diagnostic)
REM Look for applications loading DLLs from their own dir
REM Use Process Monitor and filter for: Result = NAME NOT FOUND AND Path ends with .dll
```

<b>What does it do?</b> Summarises the scenario or investigation sequence for persistence technique 6 dll hijacking; correlate actions, telemetry and response decisions rather than treating it as an executable command.

---

## 🕵️ Complete Persistence Hunt Script

```powershell
function Complete-PersistenceHunt {
    $results = [System.Collections.ArrayList]::new()
    Write-Host '`n' + '='*55 -ForegroundColor Red
    Write-Host '  COMPLETE PERSISTENCE HUNT  ' -ForegroundColor Red
    Write-Host '='*55 + '`n' -ForegroundColor Red

    # 1. Registry
    '==[1] REGISTRY RUN KEYS==' | Write-Host -ForegroundColor Cyan
    @(
        'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run',
        'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce',
        'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run',
        'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce'
    ) | ForEach-Object {
        $key = $_
        try {
            $v = Get-ItemProperty $key -EA Stop
            $v.PSObject.Properties | Where-Object Name -notlike 'PS*' | ForEach-Object {
                Write-Host "  [RUN] $($_.Name) = $($_.Value)" -ForegroundColor Yellow
                [void]$results.Add("RUN KEY: $($_.Name) = $($_.Value)")
            }
        } catch {}
    }

    # 2. Startup folders
    '`n==[2] STARTUP FOLDERS==' | Write-Host -ForegroundColor Cyan
    @(
        "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup",
        'C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup'
    ) | ForEach-Object {
        Get-ChildItem $_ -Force -EA SilentlyContinue | ForEach-Object {
            Write-Host "  [STARTUP] $($_.FullName)" -ForegroundColor Yellow
            [void]$results.Add("STARTUP: $($_.FullName)")
        }
    }

    # 3. Scheduled tasks
    '`n==[3] SUSPICIOUS SCHEDULED TASKS==' | Write-Host -ForegroundColor Cyan
    Get-ScheduledTask | Where-Object { $_.State -ne 'Disabled' } | ForEach-Object {
        $a = $_.Actions | Select-Object -First 1
        if ($a.Execute -like '*Temp*' -or $a.Execute -like '*AppData*') {
            Write-Host "  [TASK] $($_.TaskName) -> $($a.Execute)" -ForegroundColor Red
            [void]$results.Add("TASK: $($_.TaskName) -> $($a.Execute)")
        }
    }

    # 4. Services
    '`n==[4] NON-STANDARD SERVICES==' | Write-Host -ForegroundColor Cyan
    Get-CimInstance Win32_Service | Where-Object {
        $_.PathName -and $_.PathName -notlike '*\Windows\*' -and
        $_.PathName -notlike '*\Program Files*'
    } | ForEach-Object {
        Write-Host "  [SVC] $($_.Name): $($_.PathName)" -ForegroundColor Yellow
        [void]$results.Add("SERVICE: $($_.Name) -> $($_.PathName)")
    }

    # 5. WMI
    '`n==[5] WMI SUBSCRIPTIONS==' | Write-Host -ForegroundColor Cyan
    $wmif = Get-CimInstance -Namespace root\subscription -Class __EventFilter -EA SilentlyContinue
    if ($wmif) {
        Write-Host "  WMI Filters: $($wmif.Count) found!" -ForegroundColor Red
        $wmif | ForEach-Object { [void]$results.Add("WMI: $($_.Name)") }
    } else { Write-Host '  No WMI filters' -ForegroundColor Green }

    # 6. IFEO
    '`n==[6] IFEO DEBUGGER HIJACKS==' | Write-Host -ForegroundColor Cyan
    Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options' -EA SilentlyContinue |
        ForEach-Object {
            $d = (Get-ItemProperty $_.PsPath -EA SilentlyContinue).Debugger
            if ($d) {
                Write-Host "  [IFEO] $($_.PSChildName) -> $d" -ForegroundColor Red
                [void]$results.Add("IFEO: $($_.PSChildName) -> $d")
            }
        }

    Write-Host '`n==[RESULTS SUMMARY]==' -ForegroundColor Green
    Write-Host "Total persistence indicators found: $($results.Count)"
    $results | Export-Csv 'C:\PersistenceReport.csv' -NoTypeInformation
    Write-Host 'Report: C:\PersistenceReport.csv' -ForegroundColor Green
}

Complete-PersistenceHunt
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## ❓ Module 12 Quiz

<b>Q1: Which persistence technique requires no admin rights at all?</b>

> Registry Run keys under HKCU and the user's Startup folder (<code>%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup</code>) both require no admin rights. Any standard user or malware running as that user can use them.

<b>Q2: Why is WMI persistence the stealthiest mechanism?</b>

> WMI persistence is stored in the WMI repository (a binary database, not a file), not in registry Run keys or startup folders. Standard persistence scanners won't find it. It requires no files on disk (scriptable consumers), survives reboots, and only appears in Sysmon Events 19/20/21 or direct WMI namespace queries.

<b>Q3: How does DLL hijacking differ from DLL injection?</b>

> Hijacking exploits Windows' DLL search order — the malicious DLL already exists on disk in a path Windows searches before the legitimate location. Injection actively forces a running process to load a DLL using Windows API calls (CreateRemoteThread + LoadLibrary). Hijacking is passive and more persistent; injection is active and targets running processes.

---

## ✅ Module Summary

- 6+ persistence techniques exist — attackers use multiple simultaneously
- Run keys (no admin), Startup folder (no admin), Tasks/Services/WMI (admin)
- WMI = stealthiest: no disk files, survives reboots, Sysmon 19-21 only evidence
- Always run the complete hunt script during incident response
- After removing one persistence mechanism, always scan ALL others

> ➡️ <b>Next Module:</b> Privilege Escalation
</details>

<details>
<summary>SUPPLEMENTARY MODULE 21 — WINDOWS INTERNALS FOR SECURITY — SUPPLEMENTARY NOTES</summary>


<b>Use cases:</b> Additional process/DLL/token inspection examples and Sysinternals workflows; use this to deepen Module 11.

> <b>Why this supplement is retained:</b> The source division contains a second, more detailed version of this topic. This material is preserved so no source commands, scripts, comments or examples are lost. Compare overlapping procedures with the main module and use the clearer/corrected explanation as the primary study path.

> <b>Level:</b> Advanced \| <b>Track:</b> Red Team + Blue Team

---

## 🧠 DLLs (Dynamic Link Libraries)

DLLs are shared code libraries loaded into processes at runtime.

### Real-World Analogy

> DLLs are like <b>shared kitchen appliances in an apartment building</b> — a blender (DLL) shared by multiple residents (processes). Each resident uses it without owning their own copy.

### Security Context

```javascript
DLL Security Issues:
├─ DLL Hijacking: Process looks for DLL in wrong location → attacker plants malicious DLL
├─ DLL Injection: Attacker forces DLL into legitimate process memory
├─ Reflective DLL Loading: DLL loaded from memory, never touches disk (fileless!)
└─ DLL Side-Loading: Legitimate app + malicious DLL with same name
```

<b>What does it do?</b> Provides a visual reference for security context, showing the main components or relationships.

```powershell
# List DLLs loaded by a process
Get-Process -Name "notepad" | Select -ExpandProperty Modules | Select ModuleName, FileName

# Find unsigned DLLs (suspicious)
Get-Process | ForEach-Object {
    $proc = $_
    $_.Modules | ForEach-Object {
        $sig = Get-AuthenticodeSignature $_.FileName -ErrorAction SilentlyContinue
        if ($sig.Status -ne 'Valid' -and $_.FileName -like "*.dll") {
            [PSCustomObject]@{
                Process = $proc.Name
                DLL = $_.FileName
                SignatureStatus = $sig.Status
            }
        }
    }
} | Where-Object {$_.DLL -like "*Temp*" -or $_.DLL -like "*AppData*"}
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

---

## 🔑 Security Tokens

Every process and thread has a <b>security token</b> containing:

- User SID (who you are)
- Group SIDs (what groups you belong to)
- Privileges (what you can do)
- Integrity level (trust level)

### Integrity Levels

```javascript
Integrity Level   | What runs there
────────────────────────────────────────────────
System            | SYSTEM processes, kernel
High              | Administrator processes (elevated)
Medium            | Standard user processes (default)
Low               | Protected processes (IE Enhanced Mode)
Untrusted         | Highly restricted
```

<b>What does it do?</b> Provides a quick reference for integrity levels; use the surrounding explanation for context and prerequisites.

```powershell
# Check integrity level of running processes
Get-CimInstance Win32_Process | ForEach-Object {
    $proc = Get-Process -Id $_.ProcessId -ErrorAction SilentlyContinue
    [PSCustomObject]@{
        Name = $_.Name
        PID = $_.ProcessId
        CommandLine = $_.CommandLine
    }
} | Format-Table
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

---

## 🔍 Sysinternals Tools

### Process Explorer

- Enhanced task manager showing process trees, DLLs, handles
- Run as admin to see all processes
- Check against VirusTotal (right-click → Check VirusTotal)

### Process Monitor (Procmon)

- Real-time file, registry, network activity monitoring
- Filter by process name, operation type
- Essential for malware analysis

### Autoruns

- Shows everything that starts automatically
- Highlights unsigned entries in yellow
- Red = missing file (broken persistence)

```shell
REM Download Sysinternals Suite
REM https://learn.microsoft.com/en-us/sysinternals/

REM Use via command line
psexec.exe \\remotepc cmd      REM Remote shell
handle.exe -p processname      REM Show process handles
listdlls.exe processname       REM List DLLs
strings.exe malware.exe        REM Extract strings from binary
```

<b>What does it do?</b> Provides a quick reference for autoruns; use the surrounding explanation for context and prerequisites.

---

## 🧪 Lab — Analyze Running Processes

```powershell
# Comprehensive process integrity analyzer
function Invoke-ProcessAudit {
    Write-Host "=== Process Security Audit ==" -ForegroundColor Cyan

    Get-CimInstance Win32_Process | ForEach-Object {
        [PSCustomObject]@{
            PID = $_.ProcessId
            Name = $_.Name
            PPID = $_.ParentProcessId
            Path = $_.ExecutablePath
            CommandLine = $_.CommandLine
            Owner = $_.GetOwner().User
        }
    } | Where-Object {
        # Flag suspicious indicators
        $_.Path -like "*Temp*" -or
        $_.Path -like "*AppData*" -or
        ($_.Name -like "svchost*" -and $_.Path -notlike "*System32*") -or
        $_.Path -eq $null
    } | Format-Table PID, Name, Path, Owner -AutoSize
}

Invoke-ProcessAudit
```

<b>What does it do?</b> Lists running processes and relevant identifiers, paths or parent-child relationships for host investigation.

---

## ❓ Module 11 Quiz

<b>Q1:</b> What is DLL hijacking?

> When a process searches for a DLL and an attacker places a malicious DLL with the same name in a directory that the process searches before the legitimate location. The process loads the malicious DLL instead.

<b>Q2:</b> What Sysinternals tool shows everything that starts automatically and highlights suspicious entries?

> Autoruns — it shows all autostart locations and highlights unsigned entries, making it essential for persistence detection.

---

## ✅ Module 11 Summary

- DLLs are shared code libraries — hijacking and injection are major attack vectors
- Security tokens control what a process can do — token theft enables privilege escalation
- Integrity levels enforce trust separation between processes
- Sysinternals (Process Explorer, Procmon, Autoruns) are essential SOC tools
- Always verify process paths — legitimate Windows processes have known, standard locations

> ➡️ <b>Next Module:</b> Persistence Techniques
</details>

<details>
<summary>SUPPLEMENTARY MODULE 22 — WINDOWS REGISTRY — DEEP SECURITY GUIDE</summary>


<b>Use cases:</b> Expanded registry query, permission and startup-location review for investigation and hardening.

> <b>Why this supplement is retained:</b> The source division contains a second, more detailed version of this topic. This material is preserved so no source commands, scripts, comments or examples are lost. Compare overlapping procedures with the main module and use the clearer/corrected explanation as the primary study path.

> <b>Level:</b> Intermediate \| <b>Track:</b> Red Team + Blue Team \| <b>Course:</b> Windows Cybersecurity

---

## 🎯 What You Will Learn

- Complete registry hive structure with security context
- All major attacker persistence locations
- Registry-based credential storage locations
- Full detection and hardening guide
- CMD + PowerShell commands

---

## 🏗️ Complete Registry Structure

```javascript
REGISTRY ROOT HIVES
├─ HKEY_LOCAL_MACHINE (HKLM)          Physical file: SYSTEM, SAM, SECURITY, SOFTWARE
│   ├─ SAM          ← Password hashes for local accounts
│   ├─ SECURITY     ← LSA secrets, cached domain credentials
│   ├─ SOFTWARE     ← Installed software, machine-wide settings
│   └─ SYSTEM       ← Device drivers, services, boot config
├─ HKEY_CURRENT_USER (HKCU)           Physical file: NTUSER.DAT (per user)
├─ HKEY_USERS (HKU)                   All user profile hives
├─ HKEY_CLASSES_ROOT (HKCR)           File associations + COM registration
└─ HKEY_CURRENT_CONFIG (HKCC)         Current hardware profile
```

<b>What does it do?</b> Provides a visual reference for complete registry structure, showing the main components or relationships.

---

## 💻 Essential Registry Commands

```shell
REM ===== QUERYING =====
reg query HKLM\SOFTWARE                              REM List keys
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run  REM Run keys
reg query HKLM /s /f "password"                       REM Search ALL for 'password'
reg query HKCU /s /f "password"
reg query HKLM /s /f "VNC"                            REM Search for VNC creds

REM ===== ADDING =====
reg add "HKCU\SOFTWARE\TestKey" /v MyValue /t REG_SZ /d "MyData" /f

REM ===== DELETING =====
reg delete "HKCU\SOFTWARE\TestKey" /f

REM ===== BACKUP / RESTORE =====
reg export HKLM C:\backup_hklm.reg          REM Full HKLM backup
reg export "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" C:\run_backup.reg
reg import C:\run_backup.reg
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

```powershell
# ===== QUERY =====
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion'
Get-Item 'HKLM:\SYSTEM\CurrentControlSet\Services'

# Search registry for keywords (attacker technique)
Get-ChildItem -Path 'HKCU:' -Recurse -ErrorAction SilentlyContinue |
  Get-ItemProperty -ErrorAction SilentlyContinue |
  Where-Object { $_ -like '*password*' }

# ===== ADD VALUE =====
New-ItemProperty -Path 'HKCU:\SOFTWARE\TestKey' -Name 'TestVal' -Value 'TestData' -PropertyType String -Force

# ===== REMOVE =====
Remove-ItemProperty -Path 'HKCU:\SOFTWARE\TestKey' -Name 'TestVal'
Remove-Item -Path 'HKCU:\SOFTWARE\TestKey' -Recurse -Force

# ===== MONITOR CHANGES (Blue Team) =====
$watcher = New-Object System.Management.ManagementEventWatcher
$watcher.Query = "SELECT * FROM RegistryValueChangeEvent WHERE Hive='HKEY_LOCAL_MACHINE' AND KeyPath='SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Run'"
$watcher.Start()
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## 🔥 All Attacker-Favored Registry Locations

### Persistence — Run Keys

```javascript
[USER LEVEL — No admin needed]
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices

[SYSTEM LEVEL — Admin required]
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run
```

<b>What does it do?</b> Provides a quick reference for persistence run keys; use the surrounding explanation for context and prerequisites.

### Persistence — Winlogon

```javascript
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
  Userinit   = C:\Windows\system32\userinit.exe,   [LEGITIMATE]
  Shell      = explorer.exe                         [LEGITIMATE]

  ATTACK: Userinit = C:\Windows\system32\userinit.exe,C:\Temp\backdoor.exe
  ATTACK: Shell    = explorer.exe,C:\malware\shell.exe
```

<b>What does it do?</b> Provides a quick reference for persistence winlogon; use the surrounding explanation for context and prerequisites.

### Persistence — Image File Execution Options

```javascript
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options
  ATTACK: \notepad.exe → Debugger = C:\Temp\malware.exe
  Effect: Every time notepad.exe runs, malware runs instead!
```

<b>What does it do?</b> Provides a visual reference for persistence image file execution options, showing the main components or relationships.

### Credential Storage

```javascript
HKLM\SAM\SAM\Domains\Account\Users   ← Password hashes (requires SYSTEM)
HKLM\SECURITY\Policy\Secrets          ← LSA secrets, service account passwords
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\DefaultPassword  ← AutoLogon!

Check AutoLogon credentials:
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## 🛡️ Red Team: Add Persistence

```shell
REM ⚠️ Lab VM only!
REM Create fake batch script
echo @echo off > C:\Temp\update.bat
echo echo Running update... >> C:\Temp\update.bat

REM Add to Run key
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "WindowsUpdateHelper" /t REG_SZ /d "C:\Temp\update.bat" /f

REM Verify
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"

REM Cleanup!
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "WindowsUpdateHelper" /f
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## 🛡️ Blue Team: Full Registry Persistence Detector

```powershell
function Invoke-RegistryPersistenceDetector {
    Write-Host '=== REGISTRY PERSISTENCE SCANNER ===' -ForegroundColor Red

    # All persistence key locations
    $locations = @{
        'HKCU Run'        = 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run'
        'HKCU RunOnce'    = 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce'
        'HKLM Run'        = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run'
        'HKLM RunOnce'    = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce'
        'HKLM Run WOW64'  = 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run'
    }

    foreach ($name in $locations.Keys) {
        Write-Host "`n[$name]" -ForegroundColor Yellow
        try {
            $props = Get-ItemProperty $locations[$name] -EA Stop
            $props.PSObject.Properties | Where-Object { $_.Name -notlike 'PS*' } | ForEach-Object {
                $filePath = $_.Value -replace '"', '' -split ' ' | Select-Object -First 1
                $exists   = Test-Path $filePath -EA SilentlyContinue
                $signed   = if ($exists) { (Get-AuthenticodeSignature $filePath -EA SilentlyContinue).Status } else { 'FileNotFound' }

                $color = if ($signed -eq 'Valid') { 'Green' } elseif (!$exists) { 'Red' } else { 'Yellow' }
                Write-Host "  $($_.Name) = $($_.Value)" -ForegroundColor $color
                Write-Host "    Signed: $signed | Exists: $exists" -ForegroundColor $color
            }
        } catch { Write-Host '  (empty)' -ForegroundColor Gray }
    }

    # Check Winlogon
    Write-Host "`n[Winlogon]" -ForegroundColor Yellow
    $wl = Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon' -EA SilentlyContinue
    Write-Host "  Userinit: $($wl.Userinit)"
    Write-Host "  Shell:    $($wl.Shell)"

    # Check AutoLogon password stored (bad!)
    $autoPass = (Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon' -EA SilentlyContinue).DefaultPassword
    if ($autoPass) { Write-Host '  !! AutoLogon password stored in registry!!' -ForegroundColor Red }

    # Check IFEO
    Write-Host "`n[Image File Execution Options - Debuggers]" -ForegroundColor Yellow
    Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options' -EA SilentlyContinue |
      ForEach-Object {
        $d = (Get-ItemProperty $_.PsPath -EA SilentlyContinue).Debugger
        if ($d) { Write-Host "  $($_.PSChildName) => $d" -ForegroundColor Red }
      }
}

Invoke-RegistryPersistenceDetector
```

<b>What does it do?</b> Queries Registry keys and values to inspect configuration, startup entries and security-related settings.

---

## ❓ Module 04 Quiz

<b>Q1: Which registry key stores local Windows password hashes?</b>

> <code>HKLM\SAM</code> — specifically under <code>SAM\Domains\Account\Users</code>. Only the SYSTEM account can read it by default. Attackers use tools like <code>reg save HKLM\SAM</code> to extract it for offline cracking.

<b>Q2: What makes HKCU persistence require no admin rights?</b>

> HKCU (HKEY\_CURRENT\_USER) maps to the current user's profile. Any user has full write access to their own hive. Malware running as a standard user can add a Run key to HKCU to execute at every login without needing admin elevation.

<b>Q3: How does IFEO (Image File Execution Options) hijacking work?</b>

> By adding a <code>Debugger</code> value under an executable's IFEO registry key, Windows will run the specified debugger instead of (or in addition to) the target executable. Attackers set malware as the debugger for a legitimate process, ensuring the malware runs whenever that process starts.

---

## ✅ Module Summary

- Registry is the central config database — every persistence mechanism leaves a registry trail
- SAM and SECURITY hives contain credentials — protect with tight ACLs
- Run/RunOnce keys, Winlogon, and IFEO are the top persistence locations
- AutoLogon passwords in plaintext registry are a critical misconfiguration
- Always use the full registry persistence scanner during incident response

> ➡️ <b>Next Module:</b> Users, Groups & Privileges
</details>

<details>
<summary>MODULE 23 — FINAL SOC LAB CAPSTONE & ATTACK-SIMULATION PROJECT</summary>


<quote color="yellow_bg">
<b>Use case:</b> Apply the course in a disposable Windows VM: prepare telemetry, run controlled local simulations, collect events, correlate evidence and write an incident report.

<b>Safety boundary:</b> This source project includes account creation, registry autorun changes, encoded PowerShell simulation and cleanup commands. Run only on a disposable VM you own, snapshot it first, disconnect it from production networks, and verify every cleanup step. Never use the simulation against a real user, third-party system or production endpoint.
</blockquote>

> <b>Level:</b> Advanced \| <b>Course:</b> Windows for Cybersecurity

> ⚠️ <b>Authorized lab environments ONLY.</b>

---

## 🎯 Project Goal

Build an isolated SOC lab, simulate selected Windows attack behaviours, detect the telemetry that is enabled in the lab, and produce an evidence-based incident report.

---

## 🖥️ Lab Infrastructure

```javascript
┌──────────────────────────────────────────────┐
│   YOUR HOST MACHINE (VirtualBox / VMware)       │
│                                                  │
│  ┌────────────┐  ┌────────────┐   │
│  │ Windows 10 │  │   Splunk   │   │
│  │ Target VM  │  │ (optional) │   │
│  │ + Sysmon   │  │ localhost  │   │
│  │ + PS Logs  │  │ :8000      │   │
│  └────────────┘  └────────────┘   │
└──────────────────────────────────────────────┘
```

<b>What does it do?</b> Provides a visual reference for lab infrastructure, showing the main components or relationships.

---

## 🛠️ Phase 1 — Environment Setup

```powershell
# Full lab setup script
# Run as Administrator on your Windows 10 VM

Write-Host '=== SOC LAB SETUP ===' -ForegroundColor Cyan

# 1. Install Sysmon
Write-Host '[1/5] Installing Sysmon...' -ForegroundColor Yellow
if (!(Test-Path 'C:\Tools')) { New-Item -ItemType Directory -Path 'C:\Tools' | Out-Null }
# Download Sysmon from https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
# Then run: Sysmon64.exe -i sysmonconfig.xml -accepteula
if (Get-Service 'Sysmon64' -EA SilentlyContinue) {
    Write-Host '  Sysmon: INSTALLED' -ForegroundColor Green
} else {
    Write-Host '  Sysmon: NOT INSTALLED - download and install manually' -ForegroundColor Red
}

# 2. Enable PowerShell Script Block Logging
Write-Host '[2/5] Enabling PowerShell logging...' -ForegroundColor Yellow
$sbPath = 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging'
New-Item -Path $sbPath -Force | Out-Null
Set-ItemProperty -Path $sbPath -Name 'EnableScriptBlockLogging' -Value 1
Write-Host '  PowerShell Script Block Logging: ENABLED' -ForegroundColor Green

# 3. Enable Process Creation auditing
Write-Host '[3/5] Enabling process auditing...' -ForegroundColor Yellow
auditpol /set /subcategory:'Process Creation' /success:enable /failure:enable | Out-Null
Write-Host '  Process Creation auditing: ENABLED' -ForegroundColor Green

# 4. Enable Account Management auditing
Write-Host '[4/5] Enabling account auditing...' -ForegroundColor Yellow
auditpol /set /category:'Account Management' /success:enable /failure:enable | Out-Null
Write-Host '  Account Management auditing: ENABLED' -ForegroundColor Green

# 5. Increase log sizes
Write-Host '[5/5] Increasing log sizes...' -ForegroundColor Yellow
wevtutil sl Security /ms:1073741824
wevtutil sl System   /ms:536870912
Write-Host '  Log sizes increased' -ForegroundColor Green

Write-Host '`n=== SETUP COMPLETE ===' -ForegroundColor Green
Write-Host 'Your lab is ready for the attack simulation!' -ForegroundColor Cyan
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## 🔴 Phase 2 — Attack Simulation

### Stage 1: Reconnaissance

```shell
REM Attacker simulates reconnaissance after gaining initial shell
systeminfo
whoami /all
net user
net localgroup Administrators
ipconfig /all
netstat -ano
```

<b>What does it do?</b> Lists the current identity, local accounts, group membership or assigned privileges for access review.

### Stage 2: Credential Attack (Brute Force Simulation)

```shell
REM Generate failed logins (simulates brute force)
net use \\localhost\IPC$ /user:Administrator BadPass001
net use \\localhost\IPC$ /user:Administrator BadPass002
net use \\localhost\IPC$ /user:Administrator BadPass003
net use \\localhost\IPC$ /user:Administrator BadPass004
net use \\localhost\IPC$ /user:Administrator BadPass005
```

<b>What does it do?</b> Provides a quick reference for stage 2 credential attack brute force simulation; use the surrounding explanation for context and prerequisites.

### Stage 3: Malware Execution

```powershell
# Simulate a harmless PowerShell command.
# Event 4104 requires Script Block Logging; Sysmon Event 1 requires Sysmon
# to be installed and configured to collect process-creation events.
$payload = 'Write-Host "[SIM] Payload executed" -ForegroundColor Red; Start-Sleep 2'
$encoded = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($payload))
powershell.exe -ExecutionPolicy Bypass -WindowStyle Hidden -EncodedCommand $encoded
```

<b>What does it do?</b> Runs a PowerShell check or automation routine for stage 3 malware execution. Review the parameters, permissions and output before using it on a real endpoint.

### Stage 4: Persistence

```shell
REM Create a temporary lab account (Event 4720).
REM Adding it to the local Administrators group generates Event 4732
REM when the relevant account-management auditing is enabled.
net user proj_svc * /add
net localgroup Administrators proj_svc /add

REM Registry persistence (generates Sysmon Event 13)
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "SvcUpdate" /t REG_SZ /d "C:\Temp\update.bat" /f
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

### Stage 5: Lateral Movement Simulation

```shell
REM Simulates network authentication (generates 4624 Type 3)
net use \\127.0.0.1\IPC$ /user:proj_svc *
net use \\127.0.0.1\IPC$ /delete
```

<b>What does it do?</b> Provides a quick reference for stage 5 lateral movement simulation; use the surrounding explanation for context and prerequisites.

### Stage 6: Lab cleanup

```shell
net user proj_svc /delete
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "SvcUpdate" /f
```

<b>What does it do?</b> Reads or changes Registry configuration. Write and delete operations can affect startup or system behaviour; use a disposable VM and document cleanup.

---

## 🔍 Phase 3 — Detection & Investigation

```powershell
# Full attack detection script
$simStart = Read-Host 'Enter simulation start time (YYYY-MM-DD HH:MM:SS)'
$startTime = [datetime]::Parse($simStart)
$outputDir = "C:\ProjectIR_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
New-Item -ItemType Directory -Path $outputDir | Out-Null

Write-Host '=== DETECTING ATTACK STAGES ===' -ForegroundColor Red

# Stage 1: Brute force
$bruteForce = Get-WinEvent -FilterHashtable @{
    LogName='Security'; Id=4625; StartTime=$startTime
} -EA SilentlyContinue
Write-Host "Stage 2 - Brute Force: $($bruteForce.Count) failed logins" -ForegroundColor Yellow
$bruteForce | Select-Object TimeCreated, Message | Export-Csv "$outputDir\brute_force.csv" -NoTypeInformation

# Stage 2: Malware execution
$malware = Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -EA SilentlyContinue |
  Where-Object { $_.Id -eq 4104 -and $_.TimeCreated -ge $startTime -and
    ($_.Message -like '*EncodedCommand*' -or $_.Message -like '*Bypass*') }
Write-Host "Stage 3 - Malware PS Events: $($malware.Count)" -ForegroundColor Yellow
$malware | Select-Object TimeCreated, Message | Export-Csv "$outputDir\powershell_attacks.csv" -NoTypeInformation

# Stage 3: Backdoor account
$accounts = Get-WinEvent -FilterHashtable @{
    LogName='Security'; Id=4720,4728,4726; StartTime=$startTime
} -EA SilentlyContinue
Write-Host "Stage 4 - Account Events: $($accounts.Count)" -ForegroundColor Yellow
$accounts | Select-Object TimeCreated, Id, Message | Export-Csv "$outputDir\account_events.csv" -NoTypeInformation

# Stage 4: Registry persistence
$regEvents = Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' -EA SilentlyContinue |
  Where-Object { $_.Id -eq 13 -and $_.TimeCreated -ge $startTime -and $_.Message -like '*Run*' }
Write-Host "Stage 4 - Registry Persistence: $($regEvents.Count) events" -ForegroundColor Yellow

# Stage 5: Network logons
$netLogons = Get-WinEvent -FilterHashtable @{
    LogName='Security'; Id=4624; StartTime=$startTime
} -EA SilentlyContinue | Where-Object { $_.Message -like '*Logon Type:*3*' }
Write-Host "Stage 5 - Network Logons: $($netLogons.Count)" -ForegroundColor Yellow

Write-Host "`nAll evidence exported to: $outputDir" -ForegroundColor Green
```

<b>What does it do?</b> Navigates, inventories or modifies files and directories. Confirm the target path before running any command that creates, moves or deletes data.

---

## 📝 Phase 4 — Incident Report

```javascript
════════════════════════════════════════════════════
FINAL PROJECT INCIDENT REPORT
════════════════════════════════════════════════════

Incident ID   : IR-FINAL-PROJECT-001
Analyst       : [Your Name]
Date          : [Date]
Target System : [Your VM hostname]
Severity      : HIGH (Simulated)

EXECUTIVE SUMMARY
────────────────────────────────────────────────────
A simulated attack was conducted on [hostname] to test
detection capabilities. All 6 attack stages were
successfully detected using Windows Event Logs + Sysmon.

ATTACK TIMELINE
────────────────────────────────────────────────────
[Time] Recon: systeminfo, whoami, netstat run
[Time] Brute Force: [N] x Event 4625 (failed logins)
[Time] Execution: powershell.exe -EncodedCommand (Event 4104)
[Time] Persistence: proj_svc account created (Event 4720)
[Time] Persistence: reg Run key added (Sysmon Event 13)
[Time] Lateral: Network logon (Event 4624 Type 3)
[Time] Cleanup: Account deleted (Event 4726)

KEY FINDINGS
────────────────────────────────────────────────────
1. Brute force: [N] failures detected
2. Encoded PS: Successfully decoded and identified
3. Account: proj_svc backdoor found and removed
4. Registry: Run key identified via Sysmon

REMEDIATION STEPS TAKEN
────────────────────────────────────────────────────
1. Deleted backdoor account proj_svc
2. Removed malicious Run registry key
3. Reset target account passwords
4. Verified no other persistence remains
5. Ran complete persistence hunt script

LESSONS LEARNED
────────────────────────────────────────────────────
1. Sysmon detected attack stages Windows logs missed
2. Encoded commands must be decoded before analysis
3. Account deletion (4726) still reveals prior access
4. Comprehensive persistence sweep is mandatory in IR
════════════════════════════════════════════════════
```

<b>What does it do?</b> Displays or tests Windows network configuration, routes, ports or connections for host enumeration and troubleshooting.

---

## 🏆 Project Grading

| Task | Points |
| --- | --- |
| Lab setup verified (Sysmon + PS logging + auditing) | 20 |
| All 6 attack stages simulated | 20 |
| Brute force detected with event IDs cited | 15 |
| PowerShell attack detected + command decoded | 15 |
| Backdoor account found and removed | 10 |
| Registry persistence detected | 10 |
| Incident report complete and professional | 10 |
| <b>TOTAL</b> | <b>100</b> |

---

## Capstone completion

> This capstone brings together the Windows administration, enumeration, detection and incident-response topics covered in the course. Use the submitted evidence and report to demonstrate what you completed in the lab.

| Skill area | Capstone evidence |
| --- | --- |
| Windows enumeration | Host, identity, process, service and network output |
| Authentication monitoring | Relevant failed and successful logon events |
| Persistence investigation | Registry and startup artefact review |
| PowerShell telemetry | Script-block and process-creation evidence when enabled |
| SIEM analysis | Saved searches and correlated event timeline |
| Incident response | Containment rationale, cleanup record and incident report |

<i>Always use these skills ethically, only in authorized environments.</i>
</details>

<details>
<summary>REFERENCE — COURSE OVERVIEW & MODULE INDEX</summary>


<blockquote>
<b>Mission:</b> Build practical Windows knowledge for authorised penetration testing, host investigation and defensive monitoring.

<b>Study order:</b> Complete Modules 01–19 in sequence, then review Modules 20–22 as supplementary deep dives and finish with Module 23.

<b>Lab rule:</b> Use isolated virtual machines that you own or are authorised to test. Snapshot before state-changing exercises and verify cleanup afterward.
</blockquote>

## Who this course is for

| Stage | Main focus |
| --- | --- |
| Beginner | Windows fundamentals, file systems, users and permissions |
| Intermediate | Networking, CMD, PowerShell and event logs |
| Advanced | Windows internals, persistence, privilege escalation and post-access enumeration |
| Applied | SOC workflows, automation, real-world scenarios and lab tooling |

## Ordered module index

### Foundation — Modules 01–05

1. Module 01 — Windows Fundamentals
2. Module 02 — Windows File System Deep Dive
3. Module 03 — Processes, Services & Memory
4. Module 04 — Windows Registry
5. Module 05 — Users, Groups & Privileges

### Networking and command-line skills — Modules 06–10

6. Module 06 — Networking Fundamentals in Windows
7. Module 07 — Advanced Networking & Troubleshooting
8. Module 08 — CMD Deep Dive
9. Module 09 — PowerShell Deep Dive
10. Module 10 — Windows Logs & Monitoring

### Windows internals and security — Modules 11–15

11. Module 11 — Windows Internals: DLLs, Tokens & Handles
12. Module 12 — Persistence Techniques
13. Module 13 — Windows Privilege Escalation
14. Module 14 — Windows for Pentesting: Post-Access
15. Module 15 — Windows for Blue Team: SOC

### Applied security — Modules 16–19

16. Module 16 — Automation & Scripting for Security
17. Module 17 — Real-World Scenarios
18. Module 18 — Tools & Practical Lab Setup
19. Module 19 — AI-Era Threats & Modern Defenses

### Supplementary deep dives and capstone — Modules 20–23

20. Supplementary Module 20 — Persistence Techniques: Complete Deep Guide
21. Supplementary Module 21 — Windows Internals for Security
22. Supplementary Module 22 — Windows Registry: Deep Security Guide
23. Module 23 — Final SOC Lab Capstone & Attack-Simulation Project

## Skills covered

| Skill area | Red Team understanding | Blue Team application |
| --- | --- | --- |
| Windows internals | Understand process, token and DLL abuse concepts | Inspect process behavior and integrity boundaries |
| PowerShell and CMD | Understand command execution and scripting tradecraft | Automate checks and investigate command activity |
| Registry and persistence | Recognise common auto-start mechanisms | Audit and detect unexpected persistence |
| Windows logs | Understand which actions generate telemetry | Query, correlate and preserve event evidence |
| Networking | Understand routes, services and command-and-control concepts | Investigate connections, DNS and firewall activity |
| Privileges | Assess permission boundaries and misconfigurations | Harden accounts, tokens and service permissions |
| AI-era threats | Understand emerging attacker use cases | Apply telemetry-driven detection and response |

## Technical references

Use current vendor documentation when a command, event ID, policy or feature depends on the Windows build or product version.

- [Microsoft Learn — Sysmon events](https://learn.microsoft.com/en-us/windows/security/operating-system-security/sysmon/sysmon-events)
- [Microsoft Sysinternals — Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Microsoft Learn — PowerShell logging](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_logging?view=powershell-5.1)
- [Microsoft Learn — Advanced Audit Policy Configuration](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/advanced-audit-policy-configuration)

## Source-version notes

The imported source describes Windows 11 24H2, AI-assisted threat scenarios, Sliver/Havoc command-and-control awareness and Microsoft Sentinel. These are retained as source context, not independently verified compatibility or current-product guidance. Check vendor documentation before relying on version-specific steps.

<blockquote>
<b>Original source page:</b> [temp — Complete Course: Penetration Testing + Blue Teaming](https://app.notion.com/p/4227cb933e3180d49a49fa759b5c112e)

<b>Attribution:</b> This reference preserves the useful course scope and source context while removing the old roadmap graphic and duplicate overview wording.
</blockquote>
</details>