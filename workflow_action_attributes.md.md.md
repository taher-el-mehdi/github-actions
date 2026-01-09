# First Workflow Action

Workflows are defined in the `.github/workflows` directory in a repository. A repository can have multiple workflows, each of which can perform a different set of tasks such as:

 - Building and testing pull requests
 - Deploying your application every time a release is created
 - Adding a label whenever a new issue is opened

This is a basic workflow to help you get started with Actions:
```yml

name: Hello

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v4

      # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world!

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.
```
## Attribute Reference

### `name` (Workflow-level)

- **Purpose**: Gives a human-readable name to the workflow.
- **Best Practice**: Use descriptive names, especially when managing multiple workflows in one repo.
- **Optional**: If omitted, GitHub will default to using the file path and filename as the workflow name.

### `on`

- **Purpose**: Specifies the event(s) that trigger the workflow.
- **Required**: Yes. Without it, the workflow will not run.
- **Examples of Events**:
  - `push`, `pull_request`, `release`
  - Webhook events like `issues`, `create`, `delete`, `member`
  - Scheduled events using `schedule` with `cron` syntax

### `jobs`

- **Purpose**: Defines jobs in the workflow.
- **Required**: Yes. At least one job must be defined.
- **Format**: Each job is an identifier you choose, and must:
  - Start with a letter or underscore
  - Contain only alphanumeric characters, dashes, or underscores

### `runs-on`

- **Purpose**: Specifies the type of virtual machine (runner) the job will run on.
- **Common GitHub-hosted runners**:
  - `ubuntu-latest`
  - `windows-latest`
  - `macos-latest`

Use the following links for more information on GitHub hosted runners:

- **[Available Images](https://github.com/actions/runner-images?tab=readme-ov-file#available-images)**
- **Installed Software**
  - [Windows](https://github.com/actions/runner-images/blob/main/images/windows/Windows2022-Readme.md)
  - [Ubuntu (Linux)](https://github.com/actions/runner-images/blob/main/images/ubuntu/Ubuntu2404-Readme.md#installed-software)
  - [macOS](https://github.com/actions/runner-images/blob/main/images/macos/macos-14-Readme.md)

### `steps`

- **Purpose**: A sequence of tasks within a job.
- **Details**:
  - Can be either actions (`uses`) or shell commands (`run`)
  - Each step runs in its own process
  - Steps share the same virtual environment and filesystem

### `uses`

- **Purpose**: Indicates an Action to use in a step.
- **Format**:
  - From same repo: `./path-to-action`
  - From another public repo: `owner/repo@ref`
  - From a container registry: `docker://image`

### `run`

- **Purpose**: Executes shell commands directly in the runner's environment.
- **Use Case**: When you want to perform tasks using commands like `npm install`, `echo`, `python script.py`, etc.

### `name` (Step-level)

- **Purpose**: Gives a distinct label to a step.
- **Optional**: If omitted, the step will default to the command or Action string as the display name.

---
[← Introduction](./README.md) | [Workflow and Action Attributes →](./workflow_action_attributes.md.md.md)
<!-- FooterEnd -->
