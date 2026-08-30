# Common Stack | Applications | Java | Maven Documentation

## Author Table

| **Author** | **Created On** | **Version** | **Last Updated By** | **Last Edited On** | **L0 Reviewer** | **L1 Reviewer** | **L2 Reviewer** |
|---|---|---|---|---|---|---|---|
| Rahul Parihar | 30-08-2026 | 1.0 | Rahul Parihar | 30-08-2026 | Annitha | Prashant/Prince | Sandeep Rawat / Ravindra |

## Table of Contents

1. [Introduction](#introduction)
2. [What Is Java?](#what-is-java)
3. [Why Is Java Used?](#why-is-java-used)
4. [What Is Maven?](#what-is-maven)
   - [Why Is Maven Used?](#why-is-maven-used)
   - [Maven Features](#maven-features)
   - [Maven Project Structure](#maven-project-structure)
   - [Maven Build Lifecycle](#maven-build-lifecycle)
5. [Maven Repository Workflow](#maven-repository-workflow)
   - [Local Repository](#local-repository)
   - [Remote Repository](#remote-repository)
   - [Local Repository vs Remote Repository](#local-repository-vs-remote-repository)
6. [Java Build and Dependency Management Tools](#java-build-and-dependency-management-tools)
7. [Tool Comparison](#tool-comparison)
8. [Advantages and Disadvantages](#advantages-and-disadvantages)
9. [Best Practices](#best-practices)
10. [Conclusion](#conclusion)
11. [Contact Information](#contact-information)
12. [References](#references)

## Introduction

Java is a general-purpose, object-oriented programming language used to develop applications, backend services, APIs, and enterprise software.

As a Java project grows, manually managing dependencies, compilation, testing, and packaging becomes difficult. Apache Maven helps automate these activities through a standard project structure and a configuration file named `pom.xml`.

Java provides the programming language, while Maven manages the build process and project dependencies.

## What Is Java?

Java is a high-level, object-oriented programming language designed to run on different operating systems.

Java source code is compiled into bytecode. The Java Virtual Machine (JVM) then executes this bytecode.

### Java Execution Flow

```text
Java Source Code
       |
       v
    javac
       |
       v
 Java Bytecode
       |
       v
      JVM
       |
       v
    Output
```

### Important Java Components

| Component | Purpose |
|---|---|
| JDK | Provides the tools required to develop Java applications |
| JVM | Executes Java bytecode |
| JRE | Provides the runtime environment for Java applications |
| `javac` | Compiles Java source code into bytecode |
| `java` | Runs Java applications |

## Why Is Java Used?

Java is widely used for application and backend development because it is portable, has a large ecosystem, and provides mature development tools.

| Reason | Description |
|---|---|
| Platform independence | Java applications can run on different platforms through a compatible JVM |
| Object-oriented design | Supports reusable and structured application development |
| Portability | Java bytecode can run on systems that support the JVM |
| Large ecosystem | Provides access to many libraries and frameworks |
| Enterprise support | Commonly used for large-scale business applications |
| Strong tooling | Provides mature development, testing, and debugging tools |
| Community support | Has a large developer and open-source community |

## What Is Maven?

Apache Maven is a build automation and dependency management tool commonly used for Java projects.

Maven uses a configuration file called `pom.xml`. POM stands for Project Object Model.

The `pom.xml` file contains project information, dependencies, plugins, build settings, packaging details, and other configuration options.

### Example Dependency

```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-library</artifactId>
    <version>1.0.0</version>
</dependency>
```

Maven reads the dependency details and downloads the required artifact from the configured repositories.

### Why Is Maven Used?

Without Maven, developers may need to manually manage the following activities:

- Downloading JAR files.
- Maintaining dependency versions.
- Configuring the classpath.
- Compiling source code.
- Running tests.
- Packaging applications.
- Managing dependency relationships.

Maven automates these tasks and provides a consistent build process.

### Maven Features

| Feature | Description |
|---|---|
| Dependency management | Downloads and manages project dependencies |
| Build automation | Automates compilation, testing, and packaging |
| Standard project structure | Provides a consistent layout for Maven projects |
| POM configuration | Stores project and build configuration in `pom.xml` |
| Plugins | Adds functionality to the Maven build process |
| Build lifecycle | Provides predefined phases for building projects |
| Repository support | Supports local and remote repositories |
| Transitive dependencies | Automatically resolves dependencies required by other dependencies |

### Maven Project Structure

Maven follows a standard project structure.

```text
my-java-project/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   └── resources/
│   └── test/
│       ├── java/
│       └── resources/
└── target/
```

### Directory Description

| Path | Purpose |
|---|---|
| `src/main/java` | Contains application source code |
| `src/main/resources` | Contains application resources |
| `src/test/java` | Contains test source code |
| `src/test/resources` | Contains test resources |
| `pom.xml` | Contains Maven project configuration |
| `target/` | Contains generated build output |

### Maven Build Lifecycle

Maven provides predefined build phases. The following phases are commonly used:

```text
validate
   ↓
compile
   ↓
test
   ↓
package
   ↓
verify
   ↓
install
   ↓
deploy
```

| Command | Purpose |
|---|---|
| `mvn validate` | Validates the project |
| `mvn compile` | Compiles the source code |
| `mvn test` | Runs the tests |
| `mvn package` | Packages the application |
| `mvn verify` | Performs verification checks |
| `mvn install` | Installs the artifact in the local repository |
| `mvn deploy` | Publishes the artifact to a remote repository |

## Maven Repository Workflow

A Maven repository stores artifacts used during the build process. These artifacts may include:

- JAR files.
- POM files.
- Maven plugins.
- Application libraries.
- Organization-specific artifacts.

Maven primarily uses two types of repositories:

1. Local repository.
2. Remote repository.

### Repository Workflow

```text
Maven Project
      |
      v
   pom.xml
      |
      v
Maven Build Command
      |
      v
Check Local Repository
      |
      +----------------------+
      |                      |
    Found                Not found
      |                      |
      |                      v
      |              Check Remote Repository
      |                      |
      |                      v
      |              Download Dependency
      |                      |
      |                      v
      |              Store in Local Repository
      |                      |
      +----------------------+
      |
      v
Build Project
      |
      v
Generate Artifact
      |
      +------------------+
      |                  |
      v                  v
mvn install         mvn deploy
      |                  |
      v                  v
Local Repository   Remote Repository
```

### Local Repository

The local repository is a directory stored on the developer's or build machine.

Maven uses it to store:

- Downloaded dependencies.
- Maven plugins.
- Dependency metadata.
- Locally installed project artifacts.

#### Default Locations

Linux:

```text
~/.m2/repository
```

Windows:

```text
C:\Users\<username>\.m2\repository
```

For example, a dependency with the following coordinates:

```text
groupId    = org.example
artifactId = example-library
version    = 1.0.0
```

may be stored as follows:

```text
~/.m2/repository/
└── org/
    └── example/
        └── example-library/
            └── 1.0.0/
                ├── example-library-1.0.0.jar
                ├── example-library-1.0.0.pom
                └── ...
```

#### Purpose of the Local Repository

- Prevents repeated downloads of the same dependency.
- Speeds up subsequent builds.
- Stores locally installed artifacts.
- Allows Maven to reuse previously downloaded dependencies.

#### `mvn install`

The `mvn install` command builds the project and copies the generated artifact to the local repository.

```bash
mvn install
```

```text
Project
   |
   v
mvn install
   |
   v
target/my-app-1.0.0.jar
   |
   v
~/.m2/repository/
```

### Remote Repository

A remote repository is hosted on a server and can be accessed by multiple developers or build machines.

It can contain:

- Third-party dependencies.
- Maven plugins.
- Application libraries.
- Organization-specific artifacts.

| Repository | Purpose |
|---|---|
| Maven Central | Public repository for Java and JVM artifacts |
| Nexus Repository | Repository manager for private and public artifacts |
| JFrog Artifactory | Repository manager for storing and distributing artifacts |

#### Remote Repository Flow

```text
Maven Build
     |
     v
Local Repository
     |
     | Dependency not available
     v
Remote Repository
     |
     v
Download Artifact
     |
     v
Store in Local Repository
```

#### `mvn deploy`

The `mvn deploy` command publishes the generated artifact to a configured remote repository.

```bash
mvn deploy
```

```text
Java Project
     |
     v
mvn deploy
     |
     v
Build Artifact
     |
     v
Remote Repository
```

### Local Repository vs Remote Repository

| Feature | Local Repository | Remote Repository |
|---|---|---|
| Location | Local developer or build machine | Remote repository server |
| Scope | Usually used by one machine | Shared by multiple users or systems |
| Network requirement | Not required for cached artifacts | Usually required |
| Main purpose | Cache dependencies and store local artifacts | Store and share artifacts |
| Default location | `~/.m2/repository` | Server-specific location |
| Examples | `.m2/repository` | Maven Central, Nexus, Artifactory |
| Related command | `mvn install` | `mvn deploy` |

## Java Build and Dependency Management Tools

| Tool | Description |
|---|---|
| Maven | Build automation and dependency management using XML |
| Gradle | Build automation and dependency management using Groovy or Kotlin DSL |
| Apache Ant | Java build automation tool |
| Bazel | Scalable build system designed for large and reproducible builds |

## Tool Comparison

| Tool | Configuration | Ease of use | Best use case |
|---|---|---|---|
| Maven | XML | Easy | Standard Java projects |
| Gradle | Groovy or Kotlin DSL | Moderate | Flexible and complex builds |
| Ant | XML | Moderate | Legacy Java projects |
| Bazel | Starlark | Advanced | Large and complex repositories |

## Advantages and Disadvantages

| Advantages | Disadvantages |
|---|---|
| Provides a standard project structure | The `pom.xml` file can become lengthy |
| Automates dependency management | Requires knowledge of the Maven lifecycle |
| Resolves transitive dependencies | Dependency conflicts can occur |
| Automates compilation, testing, and packaging | Complex projects may need advanced configuration |
| Supports local and remote repositories | Has an initial learning curve |

## Best Practices

- Define explicit versions for dependencies.
- Add only the libraries required by the application.
- Remove unused dependencies and plugins.
- Use dependency management to centralize versions.
- Use Nexus or Artifactory for organization-specific artifacts.
- Do not commit the `.m2` directory to source control.
- Follow the standard Maven project structure.
- Keep dependencies updated to supported versions.
- Use a dependency analysis tool to identify unused or conflicting dependencies.
- Run tests as part of the build process.

## Conclusion

Java provides a portable platform for application development, while Maven provides a structured way to build and manage Java projects.

Maven simplifies dependency management, compilation, testing, packaging, plugin management, and artifact distribution.

The local repository stores cached dependencies and locally installed artifacts. The remote repository provides shared access to dependencies and project artifacts.

A typical dependency flow is:

```text
pom.xml
   |
   v
Maven
   |
   v
Local Repository
   |
   | Dependency not available
   v
Remote Repository
   |
   v
Download Dependency
   |
   v
Store in Local Repository
   |
   v
Build Application
```

Maven is a suitable choice when a project requires a standard structure, repeatable builds, and centralized dependency management.

## Contact Information

| Name | Email |
|---|---|
| Rahul Parihar | [rahul.parihar.snaatak@mygurukulam.co](mailto:rahul.parihar.snaatak@mygurukulam.co) |

## References

- [Apache Maven Documentation](https://maven.apache.org/guides/)
- [Maven POM Reference](https://maven.apache.org/pom.html)
- [Maven Introduction to Repositories](https://maven.apache.org/guides/introduction/introduction-to-repositories.html)
- [Maven Central Repository](https://central.sonatype.com/)
- [Oracle Java Documentation](https://docs.oracle.com/en/java/)
