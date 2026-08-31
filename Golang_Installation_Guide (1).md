# Common Stack | Applications | Golang | Installation Guide Documentation

## Author Table

| **Author** | **Created On** | **Version** | **Last Updated By** | **Last Edited On** | **L0 Reviewer** | **L1 Reviewer** | **L2 Reviewer** |
|---|---|---|---|---|---|---|---|
| Rahul Parihar | 30-08-2026 | 1.0 | Rahul Parihar | 30-08-2026 | Annitha | Prashant/Prince | Sandeep Rawat / Ravindra |

## Table of Contents

1. [Introduction](#introduction)
2. [What Is Go?](#what-is-go)
3. [Why Is Go Used?](#why-is-go-used)
4. [Prerequisites](#prerequisites)
5. [Installing Go on Windows](#installing-go-on-windows)
6. [Installing Go on macOS](#installing-go-on-macos)
7. [Installing Go on Linux](#installing-go-on-linux)
8. [Verifying the Installation](#verifying-the-installation)
9. [Setting Up the Go Workspace](#setting-up-the-go-workspace)
10. [Writing and Running a First Go Program](#writing-and-running-a-first-go-program)
11. [Common Installation Issues](#common-installation-issues)
12. [Best Practices](#best-practices)
13. [Conclusion](#conclusion)
14. [Contact Information](#contact-information)
15. [References](#references)

## Introduction

Go, also known as Golang, is an open-source language developed by Google, used for backend services, CLI tools, and networked applications.

This guide covers installing Go on Windows, macOS, and Linux, verifying the setup, and running a first program.

## What Is Go?

Go is a statically typed, compiled language. A Go installation includes the compiler, standard library, and CLI tools, all accessed through the `go` command.

| Component | Purpose |
|---|---|
| `go` | CLI tool to build, run, and manage Go code |
| Go compiler | Compiles source code into a native binary |
| `GOROOT` | Directory where Go itself is installed |
| Go modules | Dependency management for Go projects |

## Why Is Go Used?

| Reason | Description |
|---|---|
| Fast compilation | Compiles directly to native machine code |
| Built-in concurrency | Goroutines make concurrent code simple |
| Static binaries | Single executable, no external runtime |
| Cross-platform | Builds binaries for other OS from one machine |

## Prerequisites

- Windows 10+, macOS 11+, or a common Linux distribution.
- Administrator or `sudo` access.
- Internet connection and ~500 MB free disk space.

## Installing Go on Windows

### Step 1: Download and Run the Installer

Download the `.msi` installer from the official downloads page and run it, accepting the default settings.

<details>
<summary>Screenshot: Go downloads page</summary>

<img width="1522" height="1310" alt="image" src="https://github.com/user-attachments/assets/63464441-3824-482f-81e7-f5d6595210c2" />

<img width="1502" height="1192" alt="image" src="https://github.com/user-attachments/assets/5b64b84b-284c-423d-802c-1fd0ddd786aa" />

</details>

<details>
<summary>Screenshot: Go setup wizard</summary>

<img width="622" height="497" alt="image" src="https://github.com/user-attachments/assets/d65e5cff-faa7-4b46-b8ae-a9223554d466" />

</details>

### Step 2: Verify the PATH Variable

The installer adds Go to `PATH` automatically. This can be confirmed manually if needed.

<details>
<summary>Screenshot: Environment Variables dialog</summary>

<img width="722" height="157" alt="image" src="https://github.com/user-attachments/assets/5e62e21f-da49-48ea-90ce-1ca8c4cf46a4" />

</details>

## Installing Go on macOS

### Option 1: Official Installer

Download and run the `.pkg` installer. It installs Go to `/usr/local/go` and updates the shell path.

### Option 2: Homebrew

```bash
brew install go
```

## Installing Go on Linux

```bash
wget https://go.dev/dl/go1.23.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.23.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
source ~/.bashrc
```

Replace the version number with the latest release.

## Verifying the Installation

```bash
go version
```

<img width="402" height="92" alt="image" src="https://github.com/user-attachments/assets/cc4dd0a8-7612-4380-b45e-e115be6d0992" />


## Setting Up the Go Workspace

```bash
mkdir my-go-app && cd my-go-app
go mod init example.com/my-go-app
```

This creates a `go.mod` file that tracks the module name and dependencies.

| File | Purpose |
|---|---|
| `go.mod` | Module name and dependency versions |
| `go.sum` | Dependency checksums |
| `main.go` | Application entry point |

## Writing and Running a First Go Program

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

```bash
go run main.go
```

```text
Hello, Go!
```

Build a standalone binary with:

```bash
go build -o my-go-app
```

## Common Installation Issues

| Issue | Resolution |
|---|---|
| `go: command not found` | Add the Go `bin` directory to `PATH` and reload the shell |
| Old version after upgrade | Remove the old Go directory before extracting the new one |
| Permission denied on install | Run with `sudo` or as administrator |
| `go.mod` errors | Re-run `go mod init` in the project directory |

## Best Practices

- Install the latest stable release from the official source.
- Remove older versions before installing a new one.
- Use Go modules (`go.mod`) instead of `GOPATH`.
- Keep the toolchain updated for security fixes.
- Run `go version` after every install or upgrade.

## Conclusion

Go installs quickly on Windows, macOS, and Linux through official installers, package managers, or manual extraction. Running `go version` and `go mod init` afterward gives a working foundation to start building applications.

## Contact Information

| Name | Email |
|---|---|
| Rahul Parihar | [rahul.parihar.snaatak@mygurukulam.co](mailto:rahul.parihar.snaatak@mygurukulam.co) |

## References

| Reference | Link |
|---|---|
| Go Downloads | https://go.dev/dl/ |
| Go Installation Docs | https://go.dev/doc/install |
| Go Modules Reference | https://go.dev/ref/mod |
| Effective Go | https://go.dev/doc/effective_go |
