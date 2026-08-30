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

Go, also known as Golang, is an open-source programming language developed by Google. It is commonly used to build backend services, command-line tools, and networked applications because of its simple syntax, fast compilation, and built-in support for concurrency.

This guide walks through installing Go on Windows, macOS, and Linux, verifying the installation, and setting up a basic workspace to run a first program.

## What Is Go?

Go is a statically typed, compiled programming language designed for simplicity, reliability, and performance.

A Go installation includes the compiler, standard library, and a set of command-line tools, all accessed through the `go` command.

### Key Go Components

| Component | Purpose |
|---|---|
| `go` | Main command-line tool used to build, run, and manage Go code |
| Go compiler | Compiles Go source code into a native binary |
| Standard library | Built-in packages for common tasks such as I/O, networking, and text processing |
| `GOROOT` | Directory where the Go installation itself resides |
| `GOPATH` | Directory used for Go workspaces and downloaded packages |
| Go modules | Dependency management system used by modern Go projects |

## Why Is Go Used?

Go is widely used for backend and infrastructure development because of its performance, simplicity, and strong tooling.

| Reason | Description |
|---|---|
| Fast compilation | Compiles directly to native machine code |
| Built-in concurrency | Goroutines and channels make concurrent programming straightforward |
| Simple syntax | Small language surface that is easy to read and learn |
| Static binaries | Produces a single, self-contained executable with no external runtime |
| Strong standard library | Covers networking, HTTP, JSON, and more without extra dependencies |
| Cross-platform | Can compile binaries for other operating systems from a single machine |
| Growing ecosystem | Widely used for cloud-native tools, APIs, and microservices |

## Prerequisites

Before installing Go, confirm the following:

- A supported operating system: Windows 10 or later, macOS 11 or later, or a common Linux distribution.
- Administrator or `sudo` access on the machine.
- An active internet connection to download the Go installer.
- Enough free disk space, typically 500 MB or more.

## Installing Go on Windows

### Step 1: Download the Installer

Go to the official downloads page and download the Windows installer package (`.msi`).

<details>
<summary>Screenshot: Go downloads page</summary>

<img src="<SCREENSHOT_URL_GO_DOWNLOADS_PAGE>" alt="Go downloads page showing the Windows installer link" width="70%" />

</details>

### Step 2: Run the Installer

Double-click the downloaded `.msi` file and follow the setup wizard. Accept the license agreement and keep the default installation path unless a custom location is required.

<details>
<summary>Screenshot: Go setup wizard</summary>

<img src="<SCREENSHOT_URL_GO_WINDOWS_INSTALLER>" alt="Go installation wizard on Windows" width="70%" />

</details>

### Step 3: Complete the Installation

Finish the wizard. The installer automatically adds the Go binary path to the system `PATH` environment variable.

### Step 4: Confirm the PATH Variable (Optional)

If needed, the `PATH` entry can be reviewed manually.

<details>
<summary>Screenshot: Environment Variables dialog</summary>

<img src="<SCREENSHOT_URL_WINDOWS_ENV_VARIABLES>" alt="Windows Environment Variables dialog showing the Go path" width="70%" />

</details>

## Installing Go on macOS

### Option 1: Using the Official Installer

Download the `.pkg` installer from the official downloads page and run it.

<details>
<summary>Screenshot: macOS installer package</summary>

<img src="<SCREENSHOT_URL_MACOS_INSTALLER>" alt="Go installer package on macOS" width="70%" />

</details>

The installer places Go under `/usr/local/go` and adds `/usr/local/go/bin` to the shell path.

### Option 2: Using Homebrew

```bash
brew install go
```

Homebrew installs Go and keeps it updated through standard `brew upgrade` commands.

## Installing Go on Linux

### Step 1: Download the Archive

```bash
wget https://go.dev/dl/go1.23.0.linux-amd64.tar.gz
```

Replace the version number with the latest available release.

### Step 2: Remove Any Previous Installation

```bash
sudo rm -rf /usr/local/go
```

### Step 3: Extract the Archive

```bash
sudo tar -C /usr/local -xzf go1.23.0.linux-amd64.tar.gz
```

This extracts Go into `/usr/local/go`.

### Step 4: Update the PATH Variable

Add the following line to `~/.bashrc` or `~/.profile`:

```bash
export PATH=$PATH:/usr/local/go/bin
```

Apply the change:

```bash
source ~/.bashrc
```

## Verifying the Installation

Run the following command on any operating system to confirm Go is installed correctly:

```bash
go version
```

Expected output:

```text
go version go1.23.0 linux/amd64
```

The version number and platform suffix will vary depending on the operating system and installed Go release.

## Setting Up the Go Workspace

Modern Go development uses modules rather than a fixed `GOPATH` layout, but a working directory is still needed for project files.

### Create a Project Directory

```bash
mkdir my-go-app
cd my-go-app
```

### Initialize a Go Module

```bash
go mod init example.com/my-go-app
```

This creates a `go.mod` file that tracks the module name and its dependencies.

### Typical Project Layout

```text
my-go-app/
├── go.mod
├── go.sum
└── main.go
```

| File | Purpose |
|---|---|
| `go.mod` | Declares the module name and dependency versions |
| `go.sum` | Records checksums for dependency verification |
| `main.go` | Entry point of the application |

## Writing and Running a First Go Program

### Step 1: Create `main.go`

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

### Step 2: Run the Program

```bash
go run main.go
```

Expected output:

```text
Hello, Go!
```

### Step 3: Build a Binary (Optional)

```bash
go build -o my-go-app
```

This produces a standalone executable named `my-go-app` in the current directory.

## Common Installation Issues

| Issue | Likely Cause | Resolution |
|---|---|---|
| `go: command not found` | Go binary path not added to `PATH` | Add the Go `bin` directory to the shell profile and reload it |
| Old version shown after upgrade | Previous installation not removed | Remove the old Go directory before extracting the new version |
| Permission denied during install | Insufficient privileges | Run the install command with `sudo` or as an administrator |
| Module errors when running `go run` | Missing or corrupted `go.mod` | Run `go mod init` again inside the project directory |
| `GOPATH` conflicts on older setups | Legacy workspace configuration | Migrate the project to Go modules using `go mod init` |

## Best Practices

- Always install the latest stable release from the official downloads page.
- Remove older Go versions before installing a new one to avoid path conflicts.
- Use Go modules (`go.mod`) instead of a fixed `GOPATH` layout for new projects.
- Keep the Go installation and toolchain updated to receive security fixes.
- Avoid installing Go through untrusted third-party sources.
- Use `gofmt` to keep code formatting consistent across a project.
- Verify the installation with `go version` after every install or upgrade.

## Conclusion

Go can be installed on Windows, macOS, and Linux using official installers, package managers, or manual archive extraction.

Once installed, verifying the setup with `go version` and initializing a module with `go mod init` provides a working foundation for building Go applications.

Go's simple installation process, combined with its built-in tooling, makes it straightforward to get a development environment running quickly on any major platform.

## Contact Information

| Name | Email |
|---|---|
| Rahul Parihar | [rahul.parihar.snaatak@mygurukulam.co](mailto:rahul.parihar.snaatak@mygurukulam.co) |

## References

- [Go Downloads](https://go.dev/dl/)
- [Go Installation Documentation](https://go.dev/doc/install)
- [Go Modules Reference](https://go.dev/ref/mod)
- [Effective Go](https://go.dev/doc/effective_go)
- [Go Command Documentation](https://pkg.go.dev/cmd/go)
