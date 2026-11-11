# Lab Notes: PowerShell Commands for Checking Users and System Info

**Lab:** Windows Server VM (DC01) in VirtualBox on my Mac
**Domain:** MOISE.LAB (simulated healthcare org "Atlanta Medical")
**Type:** Learning lab, not a production environment

These are my notes on the first PowerShell commands I learned for looking at a Windows Server: who has an account on it, and what system it is. I'm learning PowerShell now so I can automate the user and group build later.

## Commands I learned

### 1. `Get-ComputerInfo`
Shows details about the machine itself: Windows version, computer name, domain, and hardware. I use it to confirm which server I'm on before changing anything.

```powershell
Get-ComputerInfo
```

### 2. `Get-LocalUser`
Lists the local user accounts on the server, with each account's name, whether it's enabled, and its description.

```powershell
Get-LocalUser
```

### 3. `Select-Object`
Picks which properties to show, so I only see the columns I care about.

```powershell
Get-LocalUser | Select-Object Name, Enabled
```

The `|` (pipe) sends the output of the first command into the second one.

### 4. `Where-Object`
Filters results to only the items that match a condition.

```powershell
Get-LocalUser | Where-Object { $_.Enabled -eq $true }
```

This shows only enabled accounts. `$_` means "the current item" going through the pipe.

### Putting them together

```powershell
Get-LocalUser | Where-Object { $_.Enabled -eq $true } | Select-Object Name, Enabled
```

Get the users, keep only the enabled ones, and show just the name and status.

## What I saw in the lab

Running `Get-LocalUser` on DC01 listed the built-in accounts plus the test users I created:

- **Administrator** is enabled. It's the built-in account for administering the computer/domain.
- **Guest** is disabled, which is the right default for an account nobody should be using.
- **krbtgt** is disabled. It's the Key Distribution Center service account, used by Kerberos.
- A handful of test users (jsmith, abrown, mlopez, rking, mjames) are enabled.

Screenshot: `screenshots/01-get-localuser.png`. I covered one account name before posting.

## Why this matters for IAM

Knowing exactly which accounts exist and which are enabled is the starting point of access reviews. Disabled built-in accounts, accounts that shouldn't be active, and accounts nobody can explain are all things an IAM or GRC analyst checks. Filtering with `Where-Object` and trimming output with `Select-Object` also makes it practical to review hundreds of accounts instead of reading them one by one.

## Still to learn

- `Get-ADUser` and `Get-ADGroup` for domain accounts, since `Get-LocalUser` only covers local ones
- Creating users and groups with PowerShell scripts
- Building the 150-user OU with access-driven groups
- Exporting results to CSV for audit evidence
