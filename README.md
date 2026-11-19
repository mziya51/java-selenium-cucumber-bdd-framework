# TryCloud | Selenium & Cucumber BDD Test Automation

A Java-based test automation project for testing the user interface of the TryCloud web application.

The project uses Selenium WebDriver for browser automation, Cucumber for behavior-driven development (BDD), and Maven for dependency management and test execution.

## Table of Contents

- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Running Tests](#running-tests)
- [Example Runner Configuration](#example-runner-configuration)
- [Adding New Tests](#adding-new-tests)
- [Reporting](#reporting)
- [Security and Test Data](#security-and-test-data)
- [Contributing](#contributing)

## Technology Stack

The following versions are defined in the project's current `pom.xml`:

| Technology / Dependency | Version / Setting | Purpose |
|---|---|---|
| Java | 11 — source / target | Test automation development |
| Maven | Build tool | Dependency management and build execution |
| Selenium Java | 4.14.1 | Browser-based UI automation |
| WebDriverManager | 5.3.3 | Browser driver management when integrated into the code |
| Cucumber Java | 7.3.0 | Mapping Gherkin steps to Java implementations |
| Cucumber JUnit | 7.3.0 | Running Cucumber scenarios through JUnit 4 |
| JavaFaker | 1.0.2 | Generating sample test data |
| Cucumber Reporting Plugin | 7.3.0 | Additional reporting integration |
| Maven Surefire Plugin | 2.22.1 | Executing tests during the Maven test phase |

> A dependency being declared in `pom.xml` does not necessarily mean its functionality has already been configured in the test code.

### Maven Project Coordinates

| Property | Value |
|---|---|
| Group ID | `org.example` |
| Artifact ID | `cloud-solutions` |
| Version | `1.0-SNAPSHOT` |
| Source encoding | `UTF-8` |

## Project Structure

```text
java-selenium-cucumber-bdd-framework/
├── src/
│   └── test/
│       ├── java/
│       │   └── net/
│       │       └── trycloud/
│       │           ├── pages/
│       │           ├── runners/
│       │           ├── step_definitions/
│       │           └── utilities/
│       └── resources/
│           ├── features/
│           └── uploadedFiles/
├── .gitignore
├── config.properties
├── pom.xml
└── README.md
```

### Directory Responsibilities

The following responsibilities describe the intended organization of the existing folder structure:

| Directory / File | Responsibility |
|---|---|
| `pages` | Page Object Model classes containing page locators and page-specific actions |
| `runners` | Runner classes that configure Cucumber test execution |
| `step_definitions` | Java implementations of steps defined in feature files |
| `utilities` | Shared helpers for driver management, configuration loading, and common operations |
| `features` | Test scenarios written in Gherkin using `.feature` files |
| `uploadedFiles` | Test files used in file-upload scenarios |
| `config.properties` | Project-specific execution settings |
| `pom.xml` | Maven dependencies and build configuration |

Recommended responsibility flow:

```text
Feature → Step Definition → Page Object → Selenium WebDriver
                    ↓
                 Utilities
```

Keeping page interactions in `pages`, scenario steps in `step_definitions`, and reusable helpers in `utilities` helps reduce duplication and simplify maintenance.

## Prerequisites

- JDK 11
- Maven installed and available from the command line
- A web browser supported by the project's driver implementation
- Access to the TryCloud test environment
- An authorized test account for scenarios requiring authentication
- Internet access for initial dependency downloads and driver downloads when required
- Optional: IntelliJ IDEA with Cucumber and Gherkin support

Verify your installation:

```bash
java -version
mvn -version
```

Check the Java version and Java home shown in the Maven output.

## Getting Started

1. Clone the repository:

   ```bash
   git clone <repository-url>
   ```

   Replace `<repository-url>` with the actual Git repository URL.

2. Navigate to the project root directory containing `pom.xml`.

3. Open the project in your IDE as a Maven project.

4. Resolve dependencies and compile the test code without running tests:

   ```bash
   mvn test-compile
   ```

5. Review the test environment settings and runner configuration before executing tests.

## Configuration

The project contains a `config.properties` file in its root directory.

The following content is an example only. Property names and values must match the configuration-reading code implemented in the project:

```properties
browser=chrome
url=YOUR_TRYCLOUD_TEST_URL
```

Configuration guidelines:

- Replace the URL placeholder with the actual TryCloud test environment address.
- The driver implementation must read the `browser` property for that setting to take effect.
- The configuration reader determines how the root-level `config.properties` file is loaded. Do not assume it is automatically available on the classpath.
- Do not commit sensitive information such as usernames, passwords, or tokens.
- If environment variables or a secret-management solution are used, implement the corresponding loading logic in the test code.

## Running Tests

Run the following commands from the project root directory.

### Run Tests Discovered by Maven

```bash
mvn clean test
```

This command runs the test classes discovered by Maven Surefire. The Cucumber scenarios that execute depend on the runner configuration.

### Run a Specific Runner Class

For example, if the runner class is named `CukesRunner`:

```bash
mvn test -Dtest=CukesRunner
```

Replace `CukesRunner` with the actual runner class name in your project.

> By default, Surefire discovers classes matching the naming patterns `Test*`, `*Test`, `*Tests`, and `*TestCase`. If your runner name does not match these patterns, select it explicitly with `-Dtest` or configure Surefire includes.

### Run Tests by Tag

If the corresponding tags exist in your feature files:

```bash
mvn test -Dtest=CukesRunner -Dcucumber.filter.tags="@smoke"
```

To combine tag conditions:

```bash
mvn test -Dtest=CukesRunner -Dcucumber.filter.tags="@regression and not @wip"
```

The runner name and tags above are examples. Replace them with the names used in your project.

### Run Tests from the IDE

Open a runner class configured for JUnit and use the IDE's `Run` action.

Running feature files directly requires appropriate Cucumber/Gherkin support and run configuration in the IDE.

## Example Runner Configuration

The following class is a configuration example. It is not assumed to exist in the repository.

File location:

```text
src/test/java/net/trycloud/runners/CukesRunner.java
```

```java
package net.trycloud.runners;

import io.cucumber.junit.Cucumber;
import io.cucumber.junit.CucumberOptions;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
@CucumberOptions(
    features = "src/test/resources/features",
    glue = "net.trycloud.step_definitions",
    plugin = {
        "pretty",
        "html:target/cucumber-reports/cucumber.html",
        "json:target/cucumber-reports/cucumber.json"
    },
    dryRun = false
)
public class CukesRunner {
}
```

Configuration options:

- `features`: Location of the feature files.
- `glue`: Packages containing step definitions and hooks.
- `plugin`: Console output and report formats.
- `dryRun`: When set to `true`, checks step-definition matching without executing scenario steps.

If hook classes are located in another package, include that package in the `glue` configuration.

## Adding New Tests

1. Create a `.feature` file under `src/test/resources/features`.
2. Describe the scenario using Gherkin syntax.
3. Implement the corresponding Java steps in `step_definitions`.
4. Keep page-specific locators and actions in the appropriate `pages` classes.
5. Place reusable helper methods in `utilities`.
6. Run the scenario and review the results.

### Example Gherkin Scenario

The following scenario is illustrative only and does not represent verified existing test coverage:

```gherkin
@smoke
Feature: TryCloud authentication

  Scenario: Sign in with valid credentials
    Given the user is on the TryCloud login page
    When the user signs in with valid credentials
    Then the user should see the application home page
```

Each step requires a corresponding Java implementation and the necessary test environment configuration.

## Reporting

When the example runner configuration above is used, Cucumber writes reports to:

```text
target/
└── cucumber-reports/
    ├── cucumber.html
    └── cucumber.json
```

Open the HTML report in a web browser to review the results.

The default output directory for Maven Surefire test results is:

```text
target/surefire-reports/
```

### Reporting Notes

- Report locations depend on the runner's `plugin` configuration.
- The `me.jvt.cucumber:reporting-plugin` dependency is declared in the POM. Any additional integration must be configured according to that plugin's requirements.
- Capturing screenshots and attaching them to reports requires additional implementation; declaring dependencies alone does not enable this functionality.
- The `mvn clean` command removes the `target` directory. Archive any reports you need to retain before running it.

## Security and Test Data

- Run tests only against environments you are authorized to test.
- Use dedicated test accounts and synthetic data instead of real user data.
- Do not store passwords, tokens, or other sensitive information in source code or feature files.
- Use non-sensitive sample files in `uploadedFiles`.
- Plan cleanup steps for scenarios that create or modify application data.
- Review `.gitignore` before committing local configuration files or generated output.

## Contributing

1. Create a separate branch for your changes.
2. Follow the existing package structure and coding style.
3. Add or update scenarios and step definitions for new behavior.
4. Run the affected tests.
5. Open a pull request describing the changes and the test results.
