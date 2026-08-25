# Standard Operating Procedure (SOP)
## Disk Usage, Mount Point Check & Ulimit Configuration

| Author | Created on | Version | Last updated by | Last edited on |
|--------|------------|---------|------------------|-----------------|
| `<Name>` | `<DD-MM-YY>` | Version 1 | `<Name>` | `<DD-MM-YY>` |

## Table of Contents

- [1. Purpose](#1-purpose)
- [2. Scope](#2-scope)
- [3. Pre-requisites](#3-pre-requisites)
  - [3.1 Access & Permissions](#31-access--permissions)
  - [3.2 System Requirements](#32-system-requirements)
  - [3.3 Tools Used](#33-tools-used)
- [4. Roles & Responsibilities](#4-roles--responsibilities)
- [5. Screenshot Attachment Guidelines](#5-screenshot-attachment-guidelines)
- [6. Procedure](#6-procedure)
  - [6.1 Check Disk Usage](#61-check-disk-usage)
  - [6.2 Check Mount Points](#62-check-mount-points)
  - [6.3 Configure ulimit Settings for Users and Processes](#63-configure-ulimit-settings-for-users-and-processes)
- [7. Verification / Validation](#7-verification--validation)
- [8. Rollback Procedure](#8-rollback-procedure)
- [9. Troubleshooting](#9-troubleshooting)
- [10. FAQs](#10-faqs)
- [11. Contact Information](#11-contact-information)
- [12. References](#12-references)

---

## 1. Purpose

This SOP defines the standard steps to be followed to check disk usage and mount points on a Linux server, and to configure ulimit (resource limit) settings for users and processes. It ensures that disk space and file/process resource limits are monitored and configured consistently across all environments, reducing the risk of application failures caused by disk exhaustion or resource-limit errors (e.g., "too many open files").

## 2. Scope

This procedure applies to all Linux (Ubuntu/CentOS/RHEL) servers — physical, virtual, or cloud-hosted — used for application, database, or utility workloads that require periodic disk-usage checks and user/process-level ulimit configuration.

## 3. Pre-requisites

### 3.1 Access & Permissions

| # | Requirement |
|---|---|
| 1 | SSH access to the target server |
| 2 | `sudo` / root privileges to view disk, mount and ulimit configuration files |
| 3 | Access to edit `/etc/security/limits.conf` and related systemd configuration files, where applicable |

### 3.2 System Requirements

| Requirement | Minimum Recommendation |
|---|---|
| OS | Ubuntu 20.04/22.04 or RHEL/CentOS 7+ |
| Shell access | Bash/SSH terminal |
| Utilities | `coreutils`, `util-linux`, `procps` (pre-installed on most distros) |

### 3.3 Tools Used

| Command/File | Purpose |
|---|---|
| `df` | Reports disk space usage of mounted filesystems |
| `du` | Reports disk usage of files/directories |
| `mount` / `findmnt` | Displays currently mounted filesystems |
| `lsblk` | Lists block devices and their mount points |
| `/etc/fstab` | Persistent mount point configuration file |
| `ulimit` | Shell built-in to view/set per-session resource limits |
| `/etc/security/limits.conf` | Persistent per-user/group ulimit configuration |
| `/proc/<pid>/limits` | Shows effective resource limits of a running process |
| `systemd` (`LimitNOFILE=`, etc.) | Resource limit configuration for services managed by systemd |

## 4. Roles & Responsibilities

| Role | Responsibility |
|---|---|
| System Administrator / DevOps Engineer | Executes the checks, applies ulimit configuration, and attaches screenshots as evidence |
| Application Owner | Confirms the resource-limit values required for the application/process |
| Reviewer/Approver | Reviews the completed SOP checklist and evidence before sign-off |

## 5. Screenshot Attachment Guidelines

Every command in Section 6 has a dedicated placeholder immediately under it. Screenshots must be attached **at that exact placeholder** — not bundled at the end of the document — so each image stays next to the command/output it evidences.

| Guideline | Details |
|---|---|
| Storage location | `screenshots/` folder alongside this README (e.g. `docs/screenshots/`) |
| Reference syntax | `![Step 6.1.1 - df -hT](./screenshots/6.1.1-df-hT.png)` at the matching placeholder |
| Capture scope | Full terminal window, including the command typed and its complete output |
| File naming convention | `<step-number>-<short-description>.png` (e.g. `6.1.1-df-hT.png`, `6.3.4-limits-conf.png`) |
| Repeated steps | One screenshot per instance, with a descriptive suffix (e.g. `6.3.7-proc-limits-nginx.png`) |
| Data hygiene | No sensitive data (IPs, credentials, customer data) visible before committing to the repo |

> 📷 Placeholder format used throughout this document:
> ```
> [SCREENSHOT: <what to capture>]
> ![<alt text>](./screenshots/<file-name>.png)
> ```
> Replace the placeholder line with the actual image link once the screenshot is captured.

## 6. Procedure

### 6.1 Check Disk Usage

**Quick reference**

| Step | Command | Purpose | Screenshot File |
|---|---|---|---|
| 6.1.1 | `df -hT` | Overall disk space usage of all mounted filesystems | `6.1.1-df-hT.png` |
| 6.1.2 | `du -sh /* \| sort -rh \| head -n 15` | Top 15 largest top-level directories | `6.1.2-du-top15.png` |
| 6.1.3 | `du -sh /path/* \| sort -rh \| head -n 10` | Drill down into a high-usage directory | `6.1.3-du-drilldown.png` |

**Step 6.1.1: Check overall disk space usage of all mounted filesystems**

```bash
df -hT
```

The `-h` flag prints sizes in human-readable form (K/M/G) and `-T` additionally shows the filesystem type. Confirm no filesystem is above the agreed threshold (commonly 80–90% used).

```
[SCREENSHOT: output of `df -hT`]
![df -hT output](./screenshots/6.1.1-df-hT.png)
```

**Step 6.1.2: Identify directories/files consuming the most disk space**

```bash
du -sh /* 2>/dev/null | sort -rh | head -n 15
```

This lists the top 15 largest top-level directories, sorted in descending order of size.

```
[SCREENSHOT: output of `du -sh /* | sort -rh | head -n 15`]
![du top directories](./screenshots/6.1.2-du-top15.png)
```

**Step 6.1.3: Drill down into a specific directory (as required)**

```bash
du -sh /path/to/directory/* | sort -rh | head -n 10
```

Replace `/path/to/directory` with the directory identified as high-usage in the previous step.

```
[SCREENSHOT: output of drill-down `du` command]
![du drill-down output](./screenshots/6.1.3-du-drilldown.png)
```

### 6.2 Check Mount Points

**Quick reference**

| Step | Command | Purpose | Screenshot File |
|---|---|---|---|
| 6.2.1 | `mount \| column -t` | List all currently mounted filesystems | `6.2.1-mount.png` |
| 6.2.2 | `findmnt` | View mount points as a tree/summary | `6.2.2-findmnt.png` |
| 6.2.3 | `lsblk -f` | Cross-check block devices and mount points | `6.2.3-lsblk.png` |
| 6.2.4 | `cat /etc/fstab` | Verify persistent mount configuration | `6.2.4-fstab.png` |

**Step 6.2.1: List all currently mounted filesystems**

```bash
mount | column -t
```

```
[SCREENSHOT: output of `mount | column -t`]
![mount output](./screenshots/6.2.1-mount.png)
```

**Step 6.2.2: View mount points in a tree/summary format**

```bash
findmnt
```

`findmnt` presents mount points in a hierarchical tree, making parent/child relationships easier to review than the raw `mount` output.

```
[SCREENSHOT: output of `findmnt`]
![findmnt output](./screenshots/6.2.2-findmnt.png)
```

**Step 6.2.3: Cross-check block devices and their mount points**

```bash
lsblk -f
```

Confirms which physical/virtual disks and partitions map to which mount points and filesystem types.

```
[SCREENSHOT: output of `lsblk -f`]
![lsblk -f output](./screenshots/6.2.3-lsblk.png)
```

**Step 6.2.4: Verify persistent mount configuration**

```bash
cat /etc/fstab
```

Confirm that every mount point required to survive a reboot is correctly listed in `/etc/fstab` with the correct device UUID, mount path, filesystem type and options.

```
[SCREENSHOT: output of `cat /etc/fstab`]
![fstab contents](./screenshots/6.2.4-fstab.png)
```

### 6.3 Configure ulimit Settings for Users and Processes

**Quick reference**

| Step | Command | Purpose | Screenshot File |
|---|---|---|---|
| 6.3.1 | `ulimit -a` | Check current limits for the logged-in shell/user | `6.3.1-ulimit-a.png` |
| 6.3.2 | `ulimit -Sn` / `ulimit -Hn` | Check soft/hard limit for open files | `6.3.2-ulimit-Sn-Hn.png` |
| 6.3.3 | `ulimit -n 65536` | Set a temporary (session-level) ulimit | `6.3.3-ulimit-temp.png` |
| 6.3.4 | edit `/etc/security/limits.conf` | Configure a persistent ulimit for a user/group | `6.3.4-limits-conf.png` |
| 6.3.5 | `grep pam_limits /etc/pam.d/common-session` | Ensure PAM limits module is enabled | `6.3.5-pam-limits.png` |
| 6.3.6 | `ulimit -a` (after re-login) | Verify persistent ulimit took effect | `6.3.6-ulimit-a-relogin.png` |
| 6.3.7 | `cat /proc/<pid>/limits` | Check effective limits of a running process | `6.3.7-proc-limits.png` |
| 6.3.8 | `systemctl edit <service-name>` | Configure ulimit for a systemd-managed service | `6.3.8-systemd-override.png` |
| 6.3.9 | `systemctl show <service-name> \| grep Limit` | Verify ulimit configuration for the service | `6.3.9-systemd-verify.png` |

**Step 6.3.1: Check current ulimit values for the logged-in shell/user**

```bash
ulimit -a
```

Displays all current soft limits for the active shell session, e.g., open files, max user processes, stack size, core file size, etc.

```
[SCREENSHOT: output of `ulimit -a`]
![ulimit -a output](./screenshots/6.3.1-ulimit-a.png)
```

**Step 6.3.2: Check the hard limit for a specific resource (e.g., open files)**

```bash
ulimit -Sn      # current soft limit for open files
ulimit -Hn      # current hard limit for open files
```

```
[SCREENSHOT: output of `ulimit -Sn` and `ulimit -Hn`]
![ulimit soft and hard limits](./screenshots/6.3.2-ulimit-Sn-Hn.png)
```

**Step 6.3.3: Set a temporary (session-level) ulimit**

```bash
ulimit -n 65536
```

This changes the open-files limit only for the current shell session; it does not persist after logout or reboot. Use this to validate a value before making it permanent.

```
[SCREENSHOT: confirmation of the temporary ulimit applied, i.e. output of `ulimit -n`]
![temporary ulimit applied](./screenshots/6.3.3-ulimit-temp.png)
```

**Step 6.3.4: Configure a persistent ulimit for a specific user or group**

Edit the limits configuration file:

```bash
sudo vi /etc/security/limits.conf
```

Add entries in the following format, then save and exit:

```
#<domain>      <type>   <item>   <value>
appuser        soft     nofile   65536
appuser        hard     nofile   65536
appuser        soft     nproc    4096
appuser        hard     nproc    4096
```

| Field | Meaning |
|---|---|
| `<domain>` | Username, `@groupname`, or `*` for all users |
| `<type>` | `soft` (adjustable up to hard limit) or `hard` (ceiling, root-only) |
| `<item>` | Resource name, e.g. `nofile` (open files), `nproc` (processes) |
| `<value>` | Numeric limit, or `unlimited` |

```
[SCREENSHOT: edited /etc/security/limits.conf entries]
![limits.conf entries](./screenshots/6.3.4-limits-conf.png)
```

**Step 6.3.5: Ensure the PAM limits module is enabled (required for limits.conf to take effect)**

```bash
cat /etc/pam.d/common-session | grep pam_limits
```

Confirm the line `session required pam_limits.so` is present. If missing, add it and save the file.

```
[SCREENSHOT: confirmation that pam_limits.so is present]
![pam_limits.so check](./screenshots/6.3.5-pam-limits.png)
```

**Step 6.3.6: Re-login and verify the persistent ulimit took effect**

```bash
exit
# log back in as the target user, then run:
ulimit -a
```

```
[SCREENSHOT: output of `ulimit -a` after re-login, showing updated values]
![ulimit -a after relogin](./screenshots/6.3.6-ulimit-a-relogin.png)
```

**Step 6.3.7: Check effective limits of a running process**

```bash
ps -ef | grep <process-name>
cat /proc/<pid>/limits
```

Replace `<pid>` with the process ID obtained from the `ps` command. This shows the actual soft/hard limits the running process is operating under.

```
[SCREENSHOT: output of `cat /proc/<pid>/limits`]
![process limits](./screenshots/6.3.7-proc-limits.png)
```

**Step 6.3.8: Configure ulimit for a systemd-managed service (if applicable)**

For processes started by systemd, `/etc/security/limits.conf` is bypassed. Edit the service unit instead:

```bash
sudo systemctl edit <service-name>
# In the override file, add:
[Service]
LimitNOFILE=65536
LimitNPROC=4096
```

```bash
sudo systemctl daemon-reload
sudo systemctl restart <service-name>
```

```
[SCREENSHOT: systemd override file and the restart confirmation]
![systemd override and restart](./screenshots/6.3.8-systemd-override.png)
```

**Step 6.3.9: Verify the ulimit configuration for the systemd service**

```bash
systemctl show <service-name> | grep Limit
```

```
[SCREENSHOT: output of `systemctl show <service-name> | grep Limit`]
![systemd limit verification](./screenshots/6.3.9-systemd-verify.png)
```

## 7. Verification / Validation

| # | Check Item | Expected Result | Status |
|---|---|---|---|
| 1 | Disk usage on all mounted filesystems | Below agreed threshold (e.g. < 80–90%) | ☐ |
| 2 | Mount points vs `/etc/fstab` | All expected mount points present and correctly configured | ☐ |
| 3 | `ulimit -a` for target user | Shows the configured soft/hard limits | ☐ |
| 4 | `/proc/<pid>/limits` or `systemctl show` | Reflects the intended values for the process/service | ☐ |
| 5 | Screenshots | Attached at their respective placeholders as evidence | ☐ |

## 8. Rollback Procedure

If the configured ulimit values cause unexpected application or system behaviour, revert as follows:

| Step | Action | Command |
|---|---|---|
| 1 | Revert user-level change | Remove/comment out the added entries in `/etc/security/limits.conf`, then ask the user to re-login |
| 2 | Revert systemd-level change | `sudo systemctl revert <service-name>` |
| 3 | Reload systemd | `sudo systemctl daemon-reload` |
| 4 | Restart the service | `sudo systemctl restart <service-name>` |

```
[SCREENSHOT: confirmation that rollback was applied and verified]
![rollback confirmation](./screenshots/8.1-rollback-verify.png)
```

## 9. Troubleshooting

| Issue | Possible Cause / Resolution |
|---|---|
| `ulimit -n` change does not persist after logout | Value was set only at shell level (temporary). Configure it in `/etc/security/limits.conf` and confirm `pam_limits.so` is enabled. |
| Changes in `limits.conf` have no effect on a systemd service | systemd services do not read `limits.conf`. Configure `Limit*` directives in the service unit via `systemctl edit` instead. |
| "Operation not permitted" when raising the hard limit | Only root can raise a hard limit. Re-attempt as root/sudo, or ask an administrator to update `limits.conf`. |
| Disk shows 100% used but `du` totals do not match | A deleted file may still be held open by a running process. Identify it with `lsof \| grep deleted` and restart the owning process. |
| `/etc/fstab` entry causes boot failure | Verify UUID/device path with `blkid` and correct the entry, or add the `nofail` option to prevent boot from stalling. |

## 10. FAQs

| Question | Answer |
|---|---|
| What is the difference between a soft and a hard ulimit? | A soft limit is the currently enforced value and can be raised by the user up to the hard limit. A hard limit is the ceiling, and can only be raised by root. |
| Do I need to restart the server after editing `/etc/security/limits.conf`? | No. A fresh login (new shell session) for the affected user is sufficient; a full server restart is not required. |
| Why does my systemd service still show the old limit after editing `limits.conf`? | systemd-managed processes do not inherit `/etc/security/limits.conf`. Configure limits directly on the service unit as shown in Step 6.3.8. |

## 11. Contact Information

| Name | Email address |
|---|---|
| `<Name>` | `<email@company.com>` |

## 12. References

| Links | Description |
|---|---|
| [df man page](https://man7.org/linux/man-pages/man1/df.1.html) | `df` command reference |
| [du man page](https://man7.org/linux/man-pages/man1/du.1.html) | `du` command reference |
| [limits.conf man page](https://man7.org/linux/man-pages/man5/limits.conf.5.html) | `limits.conf` configuration reference |
| [systemd.exec man page](https://www.freedesktop.org/software/systemd/man/systemd.exec.html) | systemd resource-limit directives (`LimitNOFILE`, etc.) |
| [Application Template](https://github.com/OT-MICROSERVICES/documentation-template/wiki/Application-Template) | Documentation format/index followed for this SOP |
| [Software Template](https://github.com/OT-MICROSERVICES/documentation-template/wiki/Software-Template) | Documentation format/index followed for this SOP |
