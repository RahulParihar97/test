# Common Stack | Operating System | Ubuntu | SOP for Disk & Ulimit

## Author Table

| **Author** | **Created On** | **Version** | **Last Updated By** | **Last Edited On** | **L0 Reviewer** | **L1 Reviewer** | **L2 Reviewer** |
|---|---|---|---|---|---|---|---|
| Rahul Parihar | 24-08-26 | 1.0 | Rahul Parihar | 25-08-26 | Annitha | Prashant/Prince | Sandeep Rawat / Ravindra |

## Table of Contents

1. [Introduction](#1-introduction)
2. [Purpose](#2-purpose)
3. [Prerequisites](#3-prerequisites)
4. [Check Disk Usage](#4-check-disk-usage)
5. [Check Mount Points](#5-check-mount-points)
6. [Configure ulimit Settings](#6-configure-ulimit-settings)
7. [Rollback Procedure](#7-rollback-procedure)
8. [Validation](#8-validation)
9. [Use Cases](#9-use-cases)
10. [Troubleshooting](#10-troubleshooting)
11. [Best Practices](#11-best-practices)
12. [Conclusion](#12-conclusion)
13. [Contact Information](#13-contact-information)
14. [References](#14-references)

## 1. Introduction

This SOP covers checking disk usage, verifying mount points, and configuring ulimit (resource limit) settings on Linux (Ubuntu/CentOS/RHEL) servers, including configuration, validation, and troubleshooting.

**Procedure flow:**

```text
Check Disk Usage
       |
       v
Check Mount Points
       |
       v
Configure ulimit
       |
       v
    Validate
       |
       v
Rollback (if needed)
```

## 2. Purpose

This SOP standardizes how to:

- Check disk usage and identify high-usage directories.
- Verify mount points are active and persisted in `/etc/fstab`.
- Configure ulimit values at the session, user, process, and systemd levels.
- Validate the applied limits and roll back safely if needed.

## 3. Prerequisites

| **Prerequisite** | **Details** |
|---|---|
| Access | SSH access with `sudo`/root privileges |
| Edit access | Ability to edit `/etc/security/limits.conf` and systemd unit files |
| OS | Ubuntu 20.04/22.04 or RHEL/CentOS 7+ |
| Packages | `coreutils`, `util-linux`, `procps` (usually pre-installed) |
| Other | Ability to reload `systemd` and re-login as the target user |

### Tools Used

| **Command/File** | **Purpose** |
|---|---|
| `df` / `du` | Disk space and directory usage |
| `mount` / `findmnt` / `/etc/fstab` | View and persist mount points |
| `ulimit` / `/etc/security/limits.conf` | Session and persistent resource limits |
| `/proc/<pid>/limits` | Effective limits of a running process |
| `systemd` (`LimitNOFILE=`, etc.) | Resource limits for systemd services |

## 4. Check Disk Usage

### Step 4.1: Overall disk space usage

```bash
df -hT
```

```text
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/sda1      ext4   50G   32G   16G  67% /
/dev/sdb1      xfs   100G   40G   55G  43% /data
```

Confirm no filesystem is above the agreed threshold (commonly 80-90%).

<details>
<summary>Screenshot - df -hT output</summary>

<img width="827" height="342" alt="image" src="https://github.com/user-attachments/assets/3ed5f903-c9b7-4722-93d9-b6d81db13c95" />

</details>

### Step 4.2: Largest directories

```bash
du -sh /* 2>/dev/null | sort -rh | head -n 15
```

<details>
<summary>Screenshot - du top 15 directories</summary>

<img width="620" height="352" alt="image" src="https://github.com/user-attachments/assets/f5b823f2-61a6-4785-9d65-2eff75c48e40" />

</details>

### Step 4.3: Drill down into a high-usage directory

```bash
du -sh /path/to/directory/* | sort -rh | head -n 10
```

<details>
<summary>Screenshot - drill-down du output</summary>

<img width="690" height="287" alt="image" src="https://github.com/user-attachments/assets/0aa03beb-b902-4e71-9339-c9feeb5c9b81" />

</details>

## 5. Check Mount Points

### Step 5.1: List currently mounted filesystems

```bash
mount | column -t
```

<details>
<summary>Screenshot - mount output</summary>

<img width="2276" height="1335" alt="image" src="https://github.com/user-attachments/assets/ea0f81a2-0137-460a-851b-93b8694e77b1" />

</details>

### Step 5.2: View mount points as a tree

```bash
findmnt
```

<details>
<summary>Screenshot - findmnt output</summary>

<img width="2061" height="1052" alt="image" src="https://github.com/user-attachments/assets/215d7764-b256-4dee-939e-83400423879b" />

</details>

### Step 5.3: Cross-check block devices

```bash
lsblk -f
```

<details>
<summary>Screenshot - lsblk -f output</summary>

<img width="1417" height="485" alt="image" src="https://github.com/user-attachments/assets/f0830148-71ab-4a02-9a38-f711bc421a87" />

</details>

### Step 5.4: Verify persistent mount configuration

```bash
cat /etc/fstab
```

Confirm every mount point required to survive a reboot is listed with the correct UUID, path, filesystem type, and options.

<details>
<summary>Screenshot - /etc/fstab contents</summary>

<img width="770" height="245" alt="image" src="https://github.com/user-attachments/assets/e9d78a54-da84-4cae-b368-34a9a91642e6" />

</details>

## 6. Configure ulimit Settings

Resource limits exist at four layers. Configuring one layer while another is actually in effect is a common mistake.

| **Layer** | **Configured At** | **Persistence** |
|---|---|---|
| Session | `ulimit -n 65536` | Lost on logout |
| User | `/etc/security/limits.conf` + PAM | Persists across logins |
| Process (read-only) | `/proc/<pid>/limits` | Reflects whichever layer actually applied |
| systemd service | `systemctl edit` (`LimitNOFILE=`) | Persists, bypasses `limits.conf` entirely |

> [!NOTE]
> A systemd-managed service never reads `/etc/security/limits.conf`. Configure its limits directly on the service unit (Step 6.5).

### Step 6.1: Check current limits for the shell

```bash
ulimit -a
ulimit -Sn      # soft limit for open files
ulimit -Hn      # hard limit for open files
```

<details>
<summary>Screenshot - ulimit -a output</summary>

<img width="597" height="387" alt="image" src="https://github.com/user-attachments/assets/973b0fcf-b63f-4967-8b8c-6047c16328d0" />

</details>

<details>
<summary>Screenshot - soft and hard limit output</summary>

<img width="267" height="107" alt="image" src="https://github.com/user-attachments/assets/c170f10d-2ee8-450e-a0c5-3761b2930d31" />

</details>

### Step 6.2: Set a temporary (session-level) limit

```bash
ulimit -n 65536
```

This applies only to the current shell and does not persist after logout. Use it to test a value before making it permanent.

<details>
<summary>Screenshot - temporary ulimit applied</summary>

<img width="322" height="52" alt="image" src="https://github.com/user-attachments/assets/417386b4-6d6c-4056-b18a-76cb84e98984" />

</details>

### Step 6.3: Set a persistent limit for a user or group

```bash
sudo vi /etc/security/limits.conf
```

```text
#<domain>      <type>   <item>   <value>
appuser        soft     nofile   65536
appuser        hard     nofile   65536
appuser        soft     nproc    4096
appuser        hard     nproc    4096
```

| **Field** | **Meaning** |
|---|---|
| `<domain>` | Username, `@groupname`, or `*` for all users |
| `<type>` | `soft` (adjustable) or `hard` (ceiling, root-only) |
| `<item>` | Resource name, e.g. `nofile`, `nproc` |
| `<value>` | Numeric limit or `unlimited` |

<details>
<summary>Screenshot - edited limits.conf entries</summary>

<img width="855" height="210" alt="Screenshot 2026-08-30 203134" src="https://github.com/user-attachments/assets/d989fb61-ea1f-4258-a700-fbdb33708908" />

</details>

Confirm the PAM limits module is enabled, or the entries above will be silently ignored:

```bash
cat /etc/pam.d/common-session | grep pam_limits
```

The line `session required pam_limits.so` must be present.

<details>
<summary>Screenshot - pam_limits.so check</summary>

<img width="917" height="130" alt="image" src="https://github.com/user-attachments/assets/2047d6f3-481a-429b-8b2d-6cdf53984ea7" />

</details>

Re-login as the target user and confirm the value took effect:

```bash
ulimit -a
```

<details>
<summary>Screenshot - ulimit -a after re-login</summary>

<img width="1111" height="961" alt="Screenshot 2026-08-30 203206" src="https://github.com/user-attachments/assets/3e7befe4-b02b-4f61-9cdb-cc9f514d04af" />

</details>

### Step 6.4: Check effective limits of a running process

```bash
ps -ef | grep <process-name>
cat /proc/<pid>/limits
```

This is the source of truth for what limit a running process is actually under, regardless of what was configured elsewhere.

<details>
<summary>Screenshot - /proc/pid/limits output</summary>

<img width="1027" height="497" alt="image" src="https://github.com/user-attachments/assets/588f1a2d-13dc-4040-9fd3-f8fdf26eab12" />

</details>

### Step 6.5: Configure ulimit for a systemd-managed service

```bash
sudo systemctl edit <service-name>
```

```text
[Service]
LimitNOFILE=65536
LimitNPROC=4096
```

```bash
sudo systemctl daemon-reload
sudo systemctl restart <service-name>
```

<details>
<summary>Screenshot - systemd override and restart</summary>

<img width="600" height="61" alt="image" src="https://github.com/user-attachments/assets/5b934a13-26dc-4df1-8ad4-ac44ef8883e4" />

<img width="1125" height="1221" alt="image" src="https://github.com/user-attachments/assets/255260b1-53c0-46c7-ac98-af197bdfb1d2" />

<img width="722" height="70" alt="image" src="https://github.com/user-attachments/assets/2a0ab969-818e-4190-9c26-84012d234507" />

</details>

Verify the applied service limits:

```bash
systemctl show <service-name> | grep Limit
```

<details>
<summary>Screenshot - systemd limit verification</summary>

<img src="<SCREENSHOT_URL_SYSTEMD_LIMIT_VERIFICATION>" alt="systemctl show output confirming service limits" width="70%" />

</details>

## 7. Rollback Procedure

| **Step** | **Action** |
|---|---|
| 1 | Remove/comment the added entries in `/etc/security/limits.conf`, then ask the user to re-login |
| 2 | `sudo systemctl revert <service-name>` |
| 3 | `sudo systemctl daemon-reload` |
| 4 | `sudo systemctl restart <service-name>` |

## 8. Validation

```bash
df -hT                      # Expected: no filesystem above the agreed threshold
findmnt                     # Expected: all required mount points match /etc/fstab
ulimit -a                   # Expected: configured limits shown for the shell
cat /proc/<pid>/limits      # Expected: configured limits shown for the process/service
```

| **Validation** | **Expected Result** |
|---|---|
| Disk usage | Below agreed threshold (e.g. < 80-90%) |
| Mount points | All expected entries present and match `/etc/fstab` |
| ulimit (shell) | `ulimit -a` shows the configured values |
| ulimit (process/service) | `/proc/<pid>/limits` or `systemctl show` reflects the intended values |

## 9. Use Cases

| **Scenario** | **Action** |
|---|---|
| "Too many open files" error | Check `/proc/<pid>/limits` (6.4), raise `nofile` via 6.3 or 6.5 |
| Disk nearing capacity | Run Section 4 to locate large directories, clean up, and re-validate |
| New mount added | Add to `/etc/fstab`, run `mount -a`, re-run Section 5 |
| Service restarts under load | Check limits via 6.5, raise if being hit |

## 10. Troubleshooting

| **Issue** | **Solution** |
|---|---|
| ulimit change doesn't persist | Set it in `limits.conf`, not just the shell, and confirm `pam_limits.so` is enabled |
| `limits.conf` has no effect on a systemd service | Configure `Limit*` directives on the service unit instead |
| "Operation not permitted" raising a hard limit | Only root can raise a hard limit |
| Disk shows 100% but `du` totals don't match | A deleted file may still be held open; check with `lsof \| grep deleted` |
| `/etc/fstab` entry causes boot failure | Verify with `blkid`, or add `nofail` to prevent boot from stalling |

## 11. Best Practices

- Schedule periodic `df -hT` checks with alerting before the threshold is breached.
- Use `limits.conf` for interactive users and systemd `Limit*` directives for services, never both for the same process.
- Keep hard limits only as high as genuinely needed.
- Document the value, reason, and evidence for every change.
- Re-run Section 8 validation after any reboot or service restart.

## 12. Conclusion

This SOP standardizes checking disk usage, verifying mount points, and configuring ulimit settings on Linux servers, helping keep systems reliable and changes evidence-backed.

## 13. Contact Information

| **Name** | **Email** |
|---|---|
| Rahul Parihar | [rahul.parihar.snaatak@mygurukulam.co](mailto:rahul.parihar.snaatak@mygurukulam.co) |

## 14. References

| **Topic** | **Description** |
|---|---|
| [df man page](https://man7.org/linux/man-pages/man1/df.1.html) | `df` command reference |
| [du man page](https://man7.org/linux/man-pages/man1/du.1.html) | `du` command reference |
| [limits.conf man page](https://man7.org/linux/man-pages/man5/limits.conf.5.html) | `limits.conf` configuration reference |
| [systemd.exec man page](https://www.freedesktop.org/software/systemd/man/systemd.exec.html) | systemd resource-limit directives |
