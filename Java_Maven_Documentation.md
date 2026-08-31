# Common Stack | Applications | Java | Maven Documentation

## Author Table

| **Author** | **Created On** | **Version** | **Last Updated By** | **Last Edited On** | **L0 Reviewer** | **L1 Reviewer** | **L2 Reviewer** |
|---|---|---|---|---|---|---|---|
| Rahul Parihar | 30-08-2026 | 1.0 | Rahul Parihar | 30-08-2026 | Annitha | Prashant/Prince | Sandeep Rawat / Ravindra |

## Table of Contents

1. [Introduction](#introduction)
2. [What Is Maven?](#what-is-maven)
   - [Why Is Maven Used?](#why-is-maven-used)
   - [Maven Features](#maven-features)
   - [Maven Project Structure](#maven-project-structure)
3. [Maven Build Lifecycle](#maven-build-lifecycle)
4. [Maven Repository Workflow](#maven-repository-workflow)
   - [Local Repository](#local-repository)
   - [Remote Repository](#remote-repository)
   - [Local vs Remote Repository](#local-vs-remote-repository)
5. [Maven Alternatives](#maven-alternatives)
6. [Advantages and Disadvantages](#advantages-and-disadvantages)
7. [Best Practices](#best-practices)
8. [Conclusion](#conclusion)
9. [Contact Information](#contact-information)
10. [References](#references)

## Introduction

Apache Maven is a build automation and dependency management tool commonly used in Java projects.

As a project grows, manually managing dependencies, compilation, testing, and packaging becomes hard to maintain. Maven automates this through a standard project structure and a single configuration file, `pom.xml`.

## What Is Maven?

Maven uses a configuration file called `pom.xml` (Project Object Model). It holds project information, dependencies, plugins, build settings, and packaging details.

### Example Dependency

```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-library</artifactId>
    <version>1.0.0</version>
</dependency>
```

Maven reads this entry and downloads the artifact from the configured repositories.

### Why Is Maven Used?

Without Maven, these tasks would need to be handled manually:

- Downloading and tracking dependency versions.
- Compiling code and managing the classpath.
- Running tests and packaging the build output.

Maven automates all of this into one consistent process.

### Maven Features

| Feature | Description |
|---|---|
| Dependency management | Downloads and manages project dependencies |
| Build automation | Automates compilation, testing, and packaging |
| Standard structure | Consistent project layout across teams |
| POM configuration | Central build configuration in `pom.xml` |
| Plugins | Extend the build process |
| Build lifecycle | Predefined phases for building projects |
| Transitive dependencies | Auto-resolves nested dependencies |

### Maven Project Structure

```text
my-java-project/
├── pom.xml
├── src/
│   ├── main/java
│   ├── main/resources
│   ├── test/java
│   └── test/resources
└── target/
```

| Path | Purpose |
|---|---|
| `src/main/java` | Application source code |
| `src/test/java` | Test source code |
| `pom.xml` | Project configuration |
| `target/` | Build output |

## Maven Build Lifecycle

Maven has three built-in lifecycles:

| Lifecycle | Purpose |
|---|---|
| clean | Removes files from previous builds |
| default | Builds and deploys the project |
| site | Generates project documentation |

The **default** lifecycle runs through the following phases, in order:

```text
mvn validate
     |
     v
mvn compile
     |
     v
mvn test
     |
     v
mvn package
     |
     v
mvn verify
     |
     v
mvn install
     |
     v
mvn deploy
```

## Maven Repository Workflow

A Maven repository stores build artifacts such as JARs, POM files, and plugins. Maven uses two types: local and remote.

### Combined Workflow

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/db0c4678-0ad4-45a5-bdfb-1116d6e17bd1" />

### Local Repository

Directory on the developer or build machine, located at `~/.m2/repository` by default.

- Caches downloaded dependencies to avoid re-downloading.
- Stores artifacts installed via `mvn install`.

### Remote Repository

Hosted on a server and shared across teams. Populated via `mvn deploy`. A few commonly used remote repositories:

| Repository | Purpose |
|---|---|
| Maven Central | Public repository for Java artifacts |
| Nexus | Repository manager for private/public artifacts |
| JFrog Artifactory | Stores and distributes artifacts |

### Local vs Remote Repository

The two repository types serve different purposes, summarized below:

| Feature | Local | Remote |
|---|---|---|
| Location | Build machine | Repository server |
| Scope | Single machine | Shared across teams |
| Default location | `~/.m2/repository` | Server-specific |
| Related command | `mvn install` | `mvn deploy` |

## Maven Alternatives

Maven is one of several build tools available for Java. A quick comparison against the other common options:

| Tool | Description | Best Use Case |
|---|---|---|
| Maven | XML-based build and dependency management | Standard Java projects |
| Gradle | Groovy/Kotlin DSL, flexible builds | Complex or custom builds |
| Ant | Basic XML-based build automation | Legacy Java projects |

## Advantages and Disadvantages

Weighing Maven's strengths against its trade-offs:

| Advantages | Disadvantages |
|---|---|
| Standard project structure | `pom.xml` can grow lengthy |
| Automated dependency management | Requires learning the lifecycle |
| Resolves transitive dependencies | Version conflicts can occur |
| Local and remote repository support | Initial learning curve |

## Best Practices

- Pin explicit dependency versions.
- Remove unused dependencies and plugins.
- Do not commit the `.m2` directory to source control.
- Use Nexus or Artifactory for shared artifacts.
- Run tests as part of every build.

## Conclusion

Maven provides a structured, repeatable way to build and manage Java projects, handling dependency resolution, compilation, testing, packaging, and artifact distribution through a single configuration file.

It is a solid default choice when a project needs a standard structure and centralized dependency management.

## Contact Information

| Name | Email |
|---|---|
| Rahul Parihar | [rahul.parihar.snaatak@mygurukulam.co](mailto:rahul.parihar.snaatak@mygurukulam.co) |

## References

| Reference | Link |
|---|---|
| Maven Guides | https://maven.apache.org/guides/ |
| Maven POM Reference | https://maven.apache.org/pom.html |
| Introduction to Repositories | https://maven.apache.org/guides/introduction/introduction-to-repositories.html |
| Maven Central | https://central.sonatype.com/ |
