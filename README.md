# Run SpecFlow Selenium Tests on HyperExecute with TestMu AI (Formerly LambdaTest)

<p align="center">
  <a href="https://www.testmuai.com/"><img src="https://img.shields.io/badge/MADE%20BY%20TestMu%20AI-000000.svg?style=for-the-badge&labelColor=000" alt="Made by TestMu AI"></a>
  <a href="https://www.nuget.org/packages/SpecFlow/"><img src="https://img.shields.io/nuget/v/SpecFlow.svg?style=for-the-badge&labelColor=000000" alt="SpecFlow version"></a>
  <a href="https://community.testmuai.com/"><img src="https://img.shields.io/badge/Join%20the%20community-blueviolet.svg?style=for-the-badge&labelColor=000000" alt="Community"></a>
</p>

## Getting Started

[TestMu AI](https://www.testmuai.com/) (Formerly LambdaTest) is the world's first full-stack AI Agentic Quality Engineering platform that empowers teams to test intelligently, smarter, and ship faster. Built for scale, it offers a full-stack testing cloud with 10K+ real devices and 3,000+ browsers. With AI-native test management, MCP servers, and agent-based automation, TestMu AI supports Selenium, Appium, Playwright, and all major frameworks.

With TestMu AI (Formerly LambdaTest), you can run SpecFlow Selenium tests at scale on the HyperExecute smart test orchestration platform. This sample shows how to configure and execute C# SpecFlow tests using both Matrix and Auto-Split strategies on the TestMu AI cloud.

- [Sign up on TestMu AI](https://www.testmuai.com/register/) (Formerly LambdaTest).
- Follow the [TestMu AI documentation](https://www.testmuai.com/support/docs/) (Formerly LambdaTest) for the full setup walkthrough.

### Prerequisites

- .NET SDK (LTS release)
- A TestMu AI (Formerly LambdaTest) account with HyperExecute access

Download the HyperExecute CLI binary corresponding to the host operating system. It is recommended to download the binary in the project's Parent Directory.

* Mac: https://downloads.lambdatest.com/hyperexecute/darwin/hyperexecute
* Linux: https://downloads.lambdatest.com/hyperexecute/linux/hyperexecute
* Windows: https://downloads.lambdatest.com/hyperexecute/windows/hyperexecute.exe

### Setup

Clone the repository:

```bash
git clone https://github.com/LambdaTest/specflow-selenium-hypertest-sample.git
cd specflow-selenium-hypertest-sample
```

The project structure is as shown below:

```yaml
specflow-demo-sample
      |
      |--- Features (Contains the feature files)
              |
              | --- GoogleSearch.feature
              | --- LambdaTestSearch.feature
              | --- SeleniumPlayground.feature
              | --- ToDoApp.feature
      |--- Hooks (Contains the event bindings to perform additional automation logic)
              | --- Hooks.cs
      |--- Steps (Contains the step definitions that correspond to the feature files)
              | --- GoogleSearchSteps.cs
              | --- DuckDuckGoSearchSteps.cs
              | --- SeleniumPlaygroundSteps.cs
              | --- ToDoAppSteps.cs
      |--- dotnet-install.sh  (Windows - Shell script to install .NET SDK, including .NET CLI & shared runtime)
      |--- dotnet-install.ps1 (macOS - Shell script to install .NET SDK, including .NET CLI & shared runtime)
      |--- App.config (Application Configuration file containing settings specific to the app)
      |
      yaml
       |
       |--- specflow_hypertest_matrix_sample.yaml
       |--- specflow_hypertest_autosplit_sample.yaml
```

Set your credentials as environment variables.

**macOS / Linux:**

```bash
export LT_USERNAME="YOUR_USERNAME"
export LT_ACCESS_KEY="YOUR_ACCESS_KEY"
```

**Windows:**

```bash
set LT_USERNAME="YOUR_USERNAME"
set LT_ACCESS_KEY="YOUR_ACCESS_KEY"
```

## Running Tests in SpecFlow Using the Matrix Strategy

Matrix YAML file (`specflow_hypertest_matrix_sample.yaml`) in the repo contains the following configuration:

```yaml
globalTimeout: 90
testSuiteTimeout: 90
testSuiteStep: 90
```

Global timeout, testSuite timeout, and testSuiteStep timeout are set to 90 minutes.

The target platform is set to macOS:

```yaml
os: [mac]
```

A user-defined key `project` is set to the C# solution (i.e. `.sln`). It can even be set to a C# project (`.csproj`) instead of C# solution. Hence, the matrix comprises of `os` and `project` keys:

```yaml
matrix:
  os: [mac]
  project: ["OnlySpecTest.sln"]
```

Content under the `pre` directive is the pre-condition that will run before the tests are executed on the HyperExecute grid. The "dotnet install" scripts for macOS & Windows are downloaded from [Microsoft Official Website](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-install-script).

* [Bash - Linux/macOS](https://dot.net/v1/dotnet-install.sh)
* [PowerShell for Windows](https://dot.net/v1/dotnet-install.ps1)

Environment variables `LT_USERNAME` and `LT_ACCESS_KEY` are added under `env` in the YAML file:

```yaml
env:
 LT_USERNAME: LT_USER_NAME
 LT_ACCESS_KEY: LT_ACCESS_KEY
```

The pre-condition installs the LTS .NET release and sets permissions on the C# project:

```yaml
pre:
   - ./dotnet-install.sh --channel LTS
   - chmod +rwx OnlySpecTest.sln
```

The `testSuites` object contains commands for executing the tests. In the current YAML file, `dotnet test` command is used for executing the tests:

```yaml
testSuites:
  - dotnet test $project
```

Run the following command on the terminal to trigger the tests in the C# project on the HyperExecute grid:

```bash
./hyperexecute --user "${ YOUR_LAMBDATEST_USERNAME()}" --key "${ YOUR_LAMBDATEST_ACCESS_KEY()}" --config specflow_hypertest_matrix_sample.yaml --verbose
```

Visit the [HyperExecute Automation Dashboard](https://automation.lambdatest.com/hypertest) to check the status of execution.

## Running Tests in SpecFlow Using Auto-Split Execution

Auto-split YAML file (`specflow_hypertest_autosplit_sample.yaml`) in the repo contains the following configuration:

```yaml
globalTimeout: 90
testSuiteTimeout: 90
testSuiteStep: 90
```

The `runson` key determines the platform (or operating system) on which the tests would be executed. Here we have set the target OS as macOS:

```yaml
runson: mac
```

Auto-split is set to true in the YAML file:

```yaml
autosplit: true
```

Retry on failure is set to False and the concurrency is set to 1:

```yaml
retryOnFailure: false
maxRetries: 5
concurrency: 1
```

Content under the `pre` directive is the pre-condition that will run before the tests are executed on the HyperExecute grid:

```yaml
env:
 LT_USERNAME: ${ YOUR_LAMBDATEST_USERNAME()}
 ACCESS_KEY: ${ YOUR_LAMBDATEST_ACCESS_KEY()}
```

```yaml
pre:
   - ./dotnet-install.sh --channel LTS
   - chmod +rwx OnlySpecTest.sln
```

The `testDiscoverer` contains the command that locates the C# solution (i.e. `.sln`):

```bash
find . -type f -name "*.sln"
```

Running the above command on the terminal gives the following output:

```
./OnlySpecTest.sln
```

Run the following command on the terminal to trigger the tests:

```bash
./hyperexecute --user "${ YOUR_LAMBDATEST_USERNAME()}" --key "${ YOUR_LAMBDATEST_ACCESS_KEY()}" --config specflow_hypertest_autosplit_sample.yaml --verbose
```

Visit the [HyperExecute Automation Dashboard](https://automation.lambdatest.com/hypertest) to check the status of execution.

### Run tests

**Matrix mode:**

```bash
./hyperexecute --user "${ YOUR_LAMBDATEST_USERNAME()}" --key "${ YOUR_LAMBDATEST_ACCESS_KEY()}" --config specflow_hypertest_matrix_sample.yaml --verbose
```

**Auto-split mode:**

```bash
./hyperexecute --user "${ YOUR_LAMBDATEST_USERNAME()}" --key "${ YOUR_LAMBDATEST_ACCESS_KEY()}" --config specflow_hypertest_autosplit_sample.yaml --verbose
```

View results on your TestMu AI dashboard.

## Contributions

Contributions are welcome. Open an issue to discuss your idea before submitting a pull request. When reporting bugs, include your .NET version, OS, and SpecFlow version.

## TestMu AI (Formerly LambdaTest) Community

Connect with testers and developers in the [TestMu AI Community](https://community.testmuai.com/). Ask questions, share what you are building, and discuss best practices in test automation and DevOps.

## TestMu AI (Formerly LambdaTest) Certifications

Earn free [TestMu AI Certifications](https://www.testmuai.com/certifications/) for testers, developers, and QA engineers. Validate your skills in Selenium, Cypress, Playwright, Appium, Espresso and more. Industry-recognized, shareable on LinkedIn, and built by practitioners, not marketers.

## Learning Resources by TestMu AI (Formerly LambdaTest)

Learn modern testing through tutorials, guides, videos, and weekly updates:

* [TestMu AI Blog](https://www.testmuai.com/blog/)
* [TestMu AI Learning Hub](https://www.testmuai.com/learning-hub/)
* [TestMu AI on YouTube](https://www.youtube.com/@TestMuAI)
* [TestMu AI Newsletter](https://www.testmuai.com/newsletter/)

## LambdaTest is Now TestMu AI

On **January 12, 2026**, [LambdaTest evolved to TestMu AI](https://www.testmuai.com/lambdatest-is-now-testmuai/), the world's first fully autonomous **Agentic AI Quality Engineering Platform**.

Same team. Same infrastructure. Same customer accounts. All existing LambdaTest logins, scripts, capabilities, and integrations continue to work without change.

Find the new home for [LambdaTest](https://www.testmuai.com).

### How LambdaTest Evolved into TestMu AI

In 2017, we launched LambdaTest with a simple mission: make testing fast, reliable, and accessible. As LambdaTest grew, we expanded into Test Intelligence, Visual Regression Testing, Accessibility Testing, API Testing, and Performance Testing, covering the full depth of the testing lifecycle.

As software development entered the AI era, testing had to evolve, too. We rebuilt the architecture to be AI-native from the ground up, with autonomous agents that **plan, author, execute, analyze, and optimize tests** while keeping humans in the loop. The platform integrates with your repos, CI, IDEs, and terminals, continuously learning from every code change and development signal.

That evolution earned a new name: **TestMu AI**, built for an AI-first future of quality engineering. TestMu is not a new name for us. It is the name of our annual community conference, which has brought together 100,000+ quality engineers to discuss how AI would reshape testing, long before that became an industry norm.

What started as a high-performance cloud testing platform has transformed into an AI-native, multi-agent system powering a connected, end-to-end quality layer. That evolution defined a new identity: LambdaTest evolved into TestMu AI, built for an AI-first future of quality engineering.

## Support

Got a question? Email [support@testmuai.com](mailto:support@testmuai.com) or chat with us 24x7 from our chat portal.
