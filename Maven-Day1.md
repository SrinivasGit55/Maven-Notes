# Maven Day 1: Maven for DevOps - Build Fundamentals, POM, Lifecycles & Repositories

## Learning Objectives

By the end of this session, you should be able to:

- Understand why build tools are necessary before deployment
- Differentiate between compiled and interpreted applications from a DevOps perspective
- Explain what Maven is and where it fits in the Java ecosystem
- Understand Maven architecture and how it compares with Ant and Gradle
- Read and explain a `pom.xml`
- Understand Maven coordinates, dependencies, scopes, properties, and BOMs
- Understand Maven lifecycles, phases, plugins, and goals
- Work with local and remote Maven repositories
- Run practical Maven commands to build, package, and install artifacts
- Understand how Maven fits into Jenkins, Docker, and CI/CD pipelines

---

## 1. Introduction to Build Tools

In software delivery, code is not deployed directly in most cases. Before deployment, we need a repeatable process to:

- Resolve dependencies
- Compile source code
- Run tests
- Package the code into deployable artifacts
- Publish the artifact to a repository
- Promote the same artifact across environments

This is why build tools are mandatory in DevOps and enterprise software delivery.

### Compiled vs Interpreted Applications

#### Compiled languages
Examples: Java, Go, C, C++, Rust, C#

- Source code is converted to machine code or bytecode
- Build is mandatory before running or deploying
- Example pipeline:
  - Source code
  - Compiler / linker / build tool
  - Executable or JAR/WAR
  - Deploy artifact

Example:
- Java source: `.java`
- Compiled output: `.class`
- Packaged output: `.jar`, `.war`

#### Interpreted languages
Examples: Python, JavaScript, Ruby, PHP

- Code is executed by an interpreter at runtime
- Build tools still help for:
  - dependency locking
  - bundling
  - packaging
  - containerization
  - scanning for vulnerabilities
  - delivering reproducible builds

### Why Build Tools Matter in DevOps

A modern build pipeline usually includes:

- dependency resolution
- compilation
- unit test execution
- packaging
- artifact publishing
- security scanning
- container build
- deployment automation

This ensures consistency across developer machines, CI/CD pipelines, and production environments.

---

## 2. What is Maven?

Maven is a build automation and project management tool primarily used for Java-based projects. It is maintained by the Apache Software Foundation and is widely used in enterprise Java and JVM ecosystems.

### Core features of Maven

- Uses a declarative `pom.xml` file
- Standardizes build lifecycle
- Resolves dependencies automatically
- Downloads required libraries from repositories
- Runs tests and packaging steps
- Publishes artifacts to local or remote repositories
- Encourages project conventions

### Maven and the JVM ecosystem

Maven is commonly used for:

- Java
- Kotlin
- Scala
- Groovy
- JVM-based microservices

Even if the language is different, if it targets the JVM, Maven can manage the build.

---

## 3. Maven vs Ant vs Gradle

### Ant

- Task-oriented and imperative
- You write what to do step-by-step
- Flexible but less structured
- More manual configuration
- Often leads to custom scripts and maintenance challenges

### Maven

- Declarative build model
- Strong conventions
- Standard lifecycle
- Best for standardized enterprise builds
- Easier to understand for teams and CI/CD systems

### Gradle

- More flexible than Maven
- Uses Groovy or Kotlin DSL
- Faster incremental builds
- Good for highly customized workflows
- More complex if you need deep control

### Rule of Thumb

- Choose Maven when you want convention-first, standard builds
- Choose Gradle when you want maximum flexibility and performance tuning
- Ant is more manual and less common in modern enterprise pipelines

---

## 4. Maven Project Structure

A simple Maven project typically looks like this:

```bash
my-app/
├── pom.xml
├── src/
│   ├── main/
│   │   └── java/
│   │       └── com/
│   │           └── mycompany/
│   │               └── app/
│   │                   └── App.java
│   └── test/
│       └── java/
│           └── com/
│               └── mycompany/
│                   └── app/
│                       └── AppTest.java
└── target/
```

The default Maven layout is important because Maven assumes this structure by convention.

---

## 5. pom.xml: The Heart of Maven

The `pom.xml` file is the Project Object Model. It is the central configuration for:

- project coordinates
- dependencies
- plugins
- build settings
- packaging
- distribution targets
- profiles
- repository configuration

### Example

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.10.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

---

## 6. GAV Coordinates

Maven identifies artifacts using GAV:

- Group ID
- Artifact ID
- Version

### Format

```text
groupId:artifactId:version
```

Example:

```text
com.mycompany.app:my-app:1.0.0
```

### What they mean

#### groupId
The organization or owning project. Usually reverse-DNS style:

```text
com.mycompany.app
```

#### artifactId
The name of the project or module:

```text
my-app
```

#### version
The version of the artifact:

```text
1.0.0
```

### Repository path mapping

A Maven artifact is typically stored in the local repo like this:

```text
~/.m2/repository/com/mycompany/app/my-app/1.0.0/my-app-1.0.0.jar
```

This is why Maven can resolve dependencies by naming conventions and repository metadata.

---

## 7. Packaging

`packaging` tells Maven what type of artifact the project produces.

Examples:

- `jar` — Java library or application
- `war` — Java web application
- `pom` — parent project or aggregator
- `ear` — enterprise application archive

Default packaging is `jar`.

---

## 8. SNAPSHOT vs RELEASE

This is extremely important in Maven.

### RELEASE version
Example:

```text
1.0.0
```

- immutable
- treated as a stable version
- once published, it should not be changed

### SNAPSHOT version
Example:

```text
1.0.0-SNAPSHOT
```

- mutable
- can change over time
- Maven may fetch updated metadata
- used during development

### Why this matters

A `SNAPSHOT` can be updated repeatedly. A `RELEASE` version should not change once released.

### Force update of SNAPSHOTs

```bash
mvn -U clean package
```

`-U` means “update snapshots”.

---

## 9. Properties in pom.xml

Properties are placeholders used to centralize configuration.

Example:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.release>17</maven.compiler.release>
    <spring.boot.version>3.3.5</spring.boot.version>
</properties>
```

You can use them like this:

```xml
<version>${spring.boot.version}</version>
```

### Why properties are useful

- one place to define versions
- easier to manage upgrades
- avoid duplication across modules

### Override from command line

```bash
mvn -Dspring.boot.version=3.3.6 package
```

This lets you override configuration at build time.

---

## 10. Dependencies and Dependency Management

### Dependencies

Dependencies declare the external libraries your project needs.

Example:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>3.3.5</version>
    </dependency>
</dependencies>
```

Maven then resolves:

- direct dependencies
- transitive dependencies
- version conflicts
- dependency graph

### DependencyManagement

`dependencyManagement` is a version rulebook.

It does not automatically add dependencies to the classpath by itself.

Example:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>3.3.5</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

This lets child modules reuse common versions without repeating them.

### BOM (Bill of Materials)

A BOM is a POM that defines compatible versions for related libraries.

This is used heavily in Spring Boot and enterprise Java stacks.

Example:

- Spring Boot BOM
- AWS BOM
- Test BOMs

### Important concept

`dependencyManagement` controls versions, but `dependencies` actually brings libraries into the project.

---

## 11. Dependency Scopes

Dependency scope controls where a dependency is visible.

| Scope | Compile Classpath | Test Classpath | Runtime Classpath | Typical Use |
|------|-------------------|----------------|------------------|-------------|
| compile | Yes | Yes | Yes | Default, most libraries |
| test | No | Yes | No | JUnit, Mockito |
| provided | Yes | Yes | No | Servlet API, application server provides it |
| runtime | No | Yes | Yes | JDBC drivers |
| system | Yes | Yes | No | Local file, avoid if possible |
| import | N/A | N/A | N/A | Used in BOM imports |

### Common examples

#### `compile`
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>6.1.6</version>
</dependency>
```

#### `test`
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
</dependency>
```

#### `provided`
```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

### Best practice
Use the narrowest possible scope. This reduces unnecessary dependencies and improves security posture.

---

## 12. Maven Lifecycles, Phases, Plugins, Goals

This is one of the most important conceptual areas in Maven.

### Lifecycle
A lifecycle is a predefined sequence of build stages.

Common Maven lifecycles:

- `default` — compile, test, package, install, deploy
- `clean` — remove generated files
- `site` — generate project documentation

### Phase
A phase is a step in a lifecycle.

Example default lifecycle phases:

```text
validate
compile
test
package
verify
install
deploy
```

### Plugin
A plugin is the actual tool that performs work.

Examples:

- compiler plugin
- surefire plugin
- jar plugin
- install plugin
- deploy plugin

### Goal
A goal is a specific task inside a plugin.

Example:

- `compiler:compile`
- `surefire:test`
- `jar:jar`

### Relationship

```text
Lifecycle → Phase → Plugin → Goal
```

The phase is the orchestration layer. The plugin/goal does the actual work.

---

## 13. Common Maven Phases

### `mvn validate`
Checks whether the project is structurally valid.

### `mvn compile`
Compiles source code and places classes in `target/classes`

### `mvn test`
Compiles test code and runs unit tests

### `mvn package`
Creates a JAR/WAR/EAR artifact in `target/`

### `mvn install`
Installs the artifact into the local Maven repository

### `mvn deploy`
Uploads the artifact to a remote repository

### `mvn clean`
Deletes the `target/` directory

---

## 14. Phase vs Goal

### Run a phase

```bash
mvn compile
```

This runs the default lifecycle up to `compile`, including necessary earlier steps.

### Run a goal directly

```bash
mvn compiler:compile
```

This runs only the compiler plugin goal. It does not necessarily run the whole lifecycle.

### Best practice

Use phases for normal builds:

```bash
mvn clean package
```

Use direct goals when debugging or targeted operations:

```bash
mvn dependency:tree
mvn help:effective-pom
```

---

## 15. Maven Repositories

A repository is a place that stores artifacts, metadata, and plugin binaries.

### Types of repositories

#### 1. Local repository
Located at:

```bash
~/.m2/repository
```

This is Maven’s cache.

- stores downloaded dependencies
- stores installed artifacts
- allows offline builds with `-o`

Example:

```bash
mvn -o package
```

#### 2. Remote repository
Usually hosted by:

- Maven Central
- Nexus
- Artifactory
- enterprise internal repository

#### 3. Mirror
A mirror is configured in `~/.m2/settings.xml` and redirects all requests to a central internal repository.

This is common in enterprise environments.

---

## 16. Repository Resolution Order

Maven resolves dependencies in a sequence like this:

1. Local repository
2. Mirror (if configured)
3. Declared remote repositories in the project
4. Maven Central (default if not overridden)

This means local cache is always checked first.

---

## 17. Maven Central and Internal Repositories

### Maven Central
The default public repository for Maven dependencies.

### Internal repository
Companies often use:

- Nexus
- JFrog Artifactory

These store:

- internal release artifacts
- snapshot artifacts
- mirrored dependencies
- approved artifacts only

This improves:

- speed
- security
- governance
- artifact traceability

---

## 18. Settings.xml and Mirror Configuration

The `settings.xml` file is usually stored in:

```bash
~/.m2/settings.xml
```

This is where you configure:

- mirrors
- credentials
- proxy settings
- local repository location

Example:

```xml
<settings>
  <mirrors>
    <mirror>
      <id>corp-group</id>
      <mirrorOf>*</mirrorOf>
      <url>https://nexus.example.com/repository/maven-group</url>
    </mirror>
  </mirrors>

  <servers>
    <server>
      <id>releases</id>
      <username>${env.NEXUS_USER}</username>
      <password>${env.NEXUS_PASS}</password>
    </server>
    <server>
      <id>snapshots</id>
      <username>${env.NEXUS_USER}</username>
      <password>${env.NEXUS_PASS}</password>
    </server>
  </servers>
</settings>
```

### Why mirrors matter
A mirror becomes a single gateway for all dependency resolution. This helps organizations centralize control and reduce direct internet access.

---

## 19. Distribution Management

`distributionManagement` defines where Maven deploys artifacts.

Example:

```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <url>https://nexus.example.com/repository/maven-releases</url>
    </repository>

    <snapshotRepository>
        <id>snapshots</id>
        <url>https://nexus.example.com/repository/maven-snapshots</url>
    </snapshotRepository>
</distributionManagement>
```

### Important difference

- `mvn install` → installs to local repository
- `mvn deploy` → pushes to remote repository

---

## 20. Super POM and Parent POM

### Super POM
Maven has a built-in default POM that defines common behavior.

Examples:
- default source directories
- standard lifecycle bindings
- default central repository

You can inspect it using:

```bash
mvn help:effective-pom
```

### Parent POM
A parent POM is your own project-level configuration used by child modules.

Example:

```xml
<parent>
    <groupId>com.mycompany</groupId>
    <artifactId>parent-pom</artifactId>
    <version>1.0.0</version>
</parent>
```

A parent POM can centralize:

- dependencyManagement
- pluginManagement
- common properties
- versions
- profiles
- enforcer rules

This helps multi-module enterprise projects stay consistent.

---

## 21. Maven Build Workflow in Practice

Let us walk through a simple Maven workflow.

### Step 1: Install Java and Maven

```bash
sudo apt update
sudo apt install -y openjdk-17-jdk
java -version
javac -version
sudo apt install -y maven
mvn -v
```

### Step 2: Create a project

```bash
mvn archetype:generate \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.5 \
  -DinteractiveMode=false
```

### Step 3: Explore the structure

```bash
cd my-app
tree -L 2 .
```

### Step 4: Validate

```bash
mvn validate
```

### Step 5: Compile

```bash
mvn compile
```

### Step 6: Test

```bash
mvn test
```

### Step 7: Package

```bash
mvn package
```

### Step 8: Install locally

```bash
mvn install
```

### Step 9: Clean build

```bash
mvn clean install
```

### Step 10: View dependency tree

```bash
mvn dependency:tree
```

### Step 11: View effective POM

```bash
mvn help:effective-pom
```

---

## 22. Practical Example: Running a Java App

After compilation:

```bash
java -cp target/classes com.mycompany.app.App
```

If the project contains a `main` method, this will run the application.

---

## 23. Typical Maven Commands Cheat Sheet

```bash
mvn validate
mvn compile
mvn test
mvn package
mvn install
mvn deploy
mvn clean
mvn -B clean package
mvn -U clean package
mvn -o package
mvn dependency:tree
mvn help:effective-pom
mvn help:effective-settings
```

### What do these mean?

- `-B` → Batch mode, ideal for CI
- `-U` → Update snapshots
- `-o` → Offline mode
- `clean` → remove stale outputs
- `install` → local repository
- `deploy` → remote repository

---

## 24. Expected Outputs and Verification

A successful Maven build should show:

```bash
BUILD SUCCESS
```

### Example verification steps

#### Check Java version
```bash
java -version
```

#### Check Maven version
```bash
mvn -v
```

#### Check compiled classes
```bash
ls target/classes
```

#### Check packaged JAR
```bash
ls target/
```

#### Check installed artifact
```bash
ls ~/.m2/repository/com/mycompany/app/my-app/
```

These outputs prove the build artifact was created and stored successfully.

---

## 25. Common Errors and Troubleshooting

### Error: `JAVA_HOME is not defined`
Cause:
- JRE installed instead of JDK

Fix:
```bash
sudo apt install -y openjdk-17-jdk
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
```

---

### Error: `mvn: command not found`
Cause:
- Maven not installed or PATH not updated

Fix:
```bash
export PATH=$PATH:/opt/maven/bin
source ~/.bashrc
which mvn
```

---

### Error: dependency download fails
Cause:
- network issue
- mirror misconfiguration
- repository blocked

Fix:
- check `settings.xml`
- confirm mirror URL
- use valid credentials
- test with `mvn -U`

---

### Error: `401 Unauthorized` during deploy
Cause:
- credentials mismatch or missing server config

Fix:
- ensure `server.id` matches `distributionManagement` repository ID
- use environment variables in CI

---

### Error: build fails because dependency not found
Cause:
- wrong version
- wrong scope
- missing repository

Fix:
- run `mvn dependency:tree`
- confirm `repository` and `mirror` configuration

---

### Error: artifact not updated
Cause:
- using snapshot update policy and stale cache

Fix:
```bash
mvn -U clean package
```

---

## 26. DevOps Use Cases

### 1. CI/CD pipelines
Jenkins job flow:

```text
git clone
mvn clean package
archive jar
build Docker image
push to registry
deploy app
```

### 2. Artifact governance
Corporate Nexus or Artifactory acts as:

- internal repository
- dependency cache
- approved artifact source
- policy enforcement point

### 3. Release management
- development artifacts: SNAPSHOT
- production artifacts: RELEASE
- build once, promote everywhere

### 4. Microservices
Parent POM can manage:

- versions
- plugins
- common configuration
- module structure

This standardizes every service in an enterprise platform.

---

## 27. Best Practices

- Pin plugin versions in `pluginManagement`
- Use dependency versions centrally
- Prefer BOMs for version alignment
- Use the narrowest dependency scope possible
- Avoid hardcoding secrets in `pom.xml`
- Keep `settings.xml` for credentials and mirrors
- Use `mvn -B` in CI pipelines
- Run `mvn clean` for fresh builds in CI
- Use `dependency:tree` when debugging conflicts
- Prefer `mvn verify` for full validation before publishing

---

## 28. Common Mistakes

- Using JRE instead of JDK
- Forgetting to configure `distributionManagement`
- Thinking `mvn install` deploys to remote repository
- Hardcoding credentials in `pom.xml`
- Using `compile` scope when `test` or `provided` is more appropriate
- Ignoring `SNAPSHOT` vs `RELEASE`
- Running goals directly without understanding lifecycle impact

---

## 29. Interview Questions and Answers

### Q1. What is Maven?
A: Maven is a build automation tool for Java and JVM projects. It standardizes dependency resolution, compilation, testing, packaging, and artifact publishing through a declarative `pom.xml`.

### Q2. What is `pom.xml`?
A: It is the project configuration file. It defines project coordinates, dependencies, plugins, repositories, and lifecycle behavior.

### Q3. What is GAV?
A: GAV stands for Group ID, Artifact ID, and Version. It uniquely identifies an artifact.

### Q4. What is the difference between `dependencyManagement` and `dependencies`?
A: `dependencyManagement` manages versions and rules. `dependencies` actually adds libraries to the project classpath.

### Q5. What is the difference between a phase and a goal?
A: A phase is part of a lifecycle. A goal is an action performed by a plugin. The lifecycle orchestrates phases; plugin goals do the actual work.

### Q6. What is a mirror in Maven?
A: A mirror redirects all dependency requests to a central internal repository such as Nexus or Artifactory.

### Q7. Why is Maven important in DevOps?
A: It provides repeatable, consistent builds and allows the same artifact to be promoted across environments with control over dependencies and versions.

### Q8. Why is `install` not the same as `deploy`?
A: `install` stores the artifact in the local Maven repository. `deploy` publishes it to a remote repository such as Nexus or Artifactory.

---

## 30. Key Takeaways

- Maven is the standard build tool for Java/JVM projects
- `pom.xml` is the single source of truth for project build configuration
- GAV is the identity of every Maven artifact
- `SNAPSHOT` is mutable; `RELEASE` is immutable
- `dependencyManagement` controls versions; `dependencies` decides what is used
- Maven phases organize the build lifecycle
- Plugins and goals perform the actual tasks
- Repositories store artifacts and metadata
- Mirrors are common in enterprise DevOps infrastructure
- `install` is local; `deploy` is remote
- Maven helps standardize the path from source code to deployable artifact

---

## 31. Quick Revision Checklist

Before moving to the next Maven session, ensure you can answer:

- What is a build tool and why is it required?
- Difference between compiled and interpreted apps in DevOps
- What is Maven and why use it?
- What is `pom.xml`?
- What is GAV?
- What is the difference between `SNAPSHOT` and `RELEASE`?
- What is `dependencyManagement`?
- What are Maven scopes?
- What is the difference between lifecycle, phase, plugin, and goal?
- Which repository is used first: local, mirror, or remote?
- What is the purpose of `settings.xml`?
- What is a mirror used for?
- What is the difference between `mvn install` and `mvn deploy`?
- What is the Super POM?
- What is the parent POM?

---

## 32. Official Documentation References

- Maven Download and Installation: https://maven.apache.org/download.cgi
- Maven Getting Started Guide: https://maven.apache.org/guides/getting-started/index.html
- Maven Lifecycle Guide: https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html
- Maven POM Reference: https://maven.apache.org/pom.html
- Maven Settings Reference: https://maven.apache.org/settings.html
- Maven Repositories Guide: https://maven.apache.org/guides/introduction/introduction-to-repositories.html
- Reference repository: https://github.com/CloudWithVarJosh/Maven-For-DevOps
- Video: https://youtu.be/3OKc5y_3wMM?si=c88vQMgq24_zeWSA

---

## 33. Final Notes

This first Maven day gives you the foundation you need for the rest of the DevOps journey. You now understand:

- why build tools matter
- how Maven works
- what `pom.xml` does
- how dependency management and lifecycles fit together
- how repositories and mirrors work in enterprise environments

This is the practical foundation behind CI pipelines, Java builds, Jenkins jobs, Docker image creation, and artifact promotion.

If you want, the next step can be:
- Maven Day 2: Plugins, profiles, build customization, and CI/CD
- Maven Day 3: Multi-module projects, release management, and Maven with Jenkins/Docker

