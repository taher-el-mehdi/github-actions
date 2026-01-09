# Create a Workflow

1. Create or open a new repository on GitHub.
1. Launch the web editor by pressing `.` on your keyboard (this opens the repository in `github.dev`).
1. In the file explorer pane, right-click and select **New Folder**.
1. Enter the following folder path:

    ```bash
    .github/workflows
    ```

1. Make sure the `workflows` directory is selected.
1. Right-click and choose **New File**.
1. Name the file:

    ```bash
    first.yml
    ````

1. Inside `first.yml`, type the following to name your workflow:

    ```yaml
    name: first
    ````

1. On the next line, type the following to define the trigger:

   ```yaml
   on: [push]
   ```

> [!TIP]
> You can list one or more trigger events using bracket notation. Even if you're using only one event, this format allows for easily adding additional events in the future.

At this point, your workflow is set up to run whenever code is pushed to the repository.

# 01_02 Add Jobs and Steps to a Workflow

A GitHub Actions workflow becomes functional by adding jobs and steps.

- **Jobs** are a collection of tasks that run in the same environment.
  - Each workflow must have at least one job,
  - Job identifiers must begin with a letter or underscore and contain only alphanumeric characters, dashes, or underscores.

- **Steps** are individual tasks within a job.
  - Each job must have at least one step

> [!TIP]
> Pay attention to the indentation in your workflow file to avoid errors caused by bad YAML formatting.


1. Below the `on: [push]` line, add the `jobs` section:

    ```yaml
    jobs:
    ````

1. Indent beneath `jobs:` and add two job identifiers:

   ```yaml
     job1:
     job2:
   ```

1. Under `job1`, add:

    ```yaml
        name: First Job
        runs-on: ubuntu-latest
    ```

1. Under `job2`, add:

    ```yaml
        name: Second Job
        runs-on: windows-latest
    ```

1. Under `job1`, add a `steps` block with two named steps:

   ```yaml
       steps:
         - name: Step One
         - name: Step Two
   ```

1. Under `job2`, add the same `steps` block:

   ```yaml
       steps:
         - name: Step One
         - name: Step Two
   ```

Your complete workflow file should be similar to the following:

```yaml
name: first
on: [push]
jobs:
  job1:
    name: First Job
    runs-on: ubuntu-latest
    steps:
      - name: Step One
      - name: Step Two
  job2:
    name: Second Job
    runs-on: windows-latest
    steps:
      - name: Step One
      - name: Step Two
```

- **Actions** are reusable units of code packaged for GitHub workflows.
- **Commands** are shell commands run directly on the runner's operating system.

> [!TIP]
> `**[actions/checkout](https://github.com/actions/checkout)**` might be one of the most commonly used actions. Its used to check out the code in your repository into the runner’s filesystem so other steps in the same job can work with it.
> Because each job runs in an isolated environment, each job that needs the work with the repository code will have to run the `checkout` action.

Other useful actions include actions that setup specific versions for compilers and interpreters for programming languages, including:

- **[actions/setup-go](https://github.com/actions/setup-go)**
- **[actions/setup-python](https://github.com/actions/setup-python)**
- **[actions/setup-node](https://github.com/actions/setup-node)**
- **[actions/setup-java](https://github.com/actions/setup-java)**

## Overview

In this lesson, you will:

- Use the `uses` attribute to add a commonly used action.
- Use the `run` attribute to add OS-specific shell commands.

## Instructions

1. Open your `first.yml` file in the GitHub web editor.
1. Under **Job 1** and **Job 2**, in the `- name: Step One` block, add:

    ```yaml
        uses: actions/checkout@v4
    ````

1. Under **Job 1**, in the `- name: Step Two` block, add:

    ```yaml
        run: env | sort
    ```

    This command prints and alphabetizes all environment variables. On Ubuntu, this runs in the  Bash shell.

1. Under **Job 2**, in the `- name: Step Two` block, add:

    ```yaml
        run: "Get-ChildItem Env: | Sort-Object Name"
    ```

    This PowerShell command prints environment variables and sorts them by name. Quotation marks are required to avoid YAML parsing issues with the colon (`:`) character in the argument `Env:`.

Your complete workflow file should be similar to the following:

```yaml
name: first
on: [push]
jobs:
  job1:
    name: First Job
    runs-on: ubuntu-latest
    steps:
      - name: Step One
        uses: actions/checkout@v4
      - name: Step Two
        run: env | sort
  job2:
    name: Second Job
    runs-on: windows-latest
    steps:
      - name: Step One
        uses: actions/checkout@v4
      - name: Step Two
        run: "Get-ChildItem Env: | Sort-Object Name"
```
# 01_05 Add Dependencies Between Jobs

In the previous lesson, you created a workflow where two jobs ran in parallel.

But what if one job depends on the output of another?

In this lecture, you’ll learn how to use the `needs` attribute in a GitHub Actions workflow to define dependencies between jobs. This allows you to control the order in which jobs run, making it possible to build more complex workflows.

## 1. Default Job Execution

- By default, all jobs in a workflow run in parallel.
- This can cause issues if one job relies on the output of another.

## 2. Add the `needs` Attribute to Define a Dependency

- Use the `needs` attribute to specify that a job depends on the successful completion of another.
- Consider the following workflow snippet:

    ```yaml
    jobs:
      job1:
      job2:
        needs: job1
    ```

- In this example, `job2` will not run until `job1` has completed successfully.

### 3. Chain Multiple Jobs with Dependencies

- You can build longer sequences or more complex execution trees by chaining jobs.
- When multiple jobs are specified for the `needs` attribute, use a bracketed list.
- Consider the following workflow snippet:

    ```yaml
    jobs:
      job1:
      job2:
        needs: job1
      job3:
        needs: [job1, job2]
    ```

- In this example, `job1` and `job2` will run in parallel.
- `job3` will only run after both `job1` and `job2` have completed successfully.
- This design pattern is useful for workflows with testing, building, and deployment phases.
# 01_06 Specify Branches for Workflow Events

By default, GitHub Actions workflows triggered by events like `push` or `pull_request` will run in response to those events on any branch.

However, GitHub gives us the ability to narrow that scope by specifying which branches (or tags) should trigger the workflow.

In this lesson, you’ll learn how to configure your workflow to target or exclude specific branches, use multiple event types, and understand the syntax rules around these filters.


- A single `push` or `pull_request` trigger will respond to events on any branch unless you restrict it.

## 2. Use YAML Block Format for Events

- To customize trigger behavior, write the event as a block:

  ```yaml
  on:
    push:
  ```

## 3. Add the `branches` Attribute

- Add a `branches` attribute under the event to list specific branches:

  ```yaml
  on:
    push:
      branches:
        - main
        - develop
  ```

  ```yaml
  on:
    pull_request:
      branches:
        - main
  ```

## 4. Support for Multiple Events with Branch Filters

- You can only define `on:` once per workflow, so multiple triggers must be written as separate blocks:

  ```yaml
  on:
    push:
      branches:
        - main
    pull_request:
      branches:
        - main
  ```

## 5. Exclude Branches Using `branches-ignore`

- If you want to ignore certain branches instead:

  ```yaml
  on:
    push:
      branches-ignore:
        - experimental
  ```

> *Note: You cannot use `branches` and `branches-ignore` together under the same event.*

## 6. Use `tags` and `tags-ignore` the same way as branches

- Example for tag filtering:

  ```yaml
  on:
    push:
      tags:
        - 'v*'
  ```

## 7. Handling Special Characters

- Wrap branches or tags with special characters in quotes:

  ```yaml
  on:
    push:
      branches:
        - "feature/authentication"
        - 'release/*'
  ```

> [!TIP]
> For detailed pattern rules, refer to GitHub’s super-detailed [filter pattern cheat sheet](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#filter-pattern-cheat-sheet).
 →
<!-- FooterStart -->
---
[← workflow_action_attributes](.workflow_action_attributes.md) | [Workflow and Action Limits →](./Workflow and Action Limits.md)
<!-- FooterEnd -->
