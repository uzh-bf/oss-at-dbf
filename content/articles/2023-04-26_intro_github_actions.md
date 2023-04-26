---
title: Introduction to Github Actions
date: 26.04.2023
author: Bulin Shaqiri
---

# Introduction to CI/CD

(Based on [GitLab](https://about.gitlab.com/topics/ci-cd/)'s and [RedHat](https://www.redhat.com/en/topics/devops/what-is-ci-cd)'s explanations)

Before moving on to the topic of GitHub Actions, we first need to understand a more general concept. Continuous Integration and Continuous Delivery (CI/CD) has become an essential practice in modern software development. It is a set of processes and principles that facilitate the development and deployment of software by automating repetitive tasks (e.g., building, testing and deploying), improving collaboration and ensuring the reliability and stability of the software.

![](https://t4648007.p.clickup-attachments.com/t4648007/16e698a7-355f-4b3f-b95a-ff1a59465a15/image.png)
Figure1: CI/CD visualized (Source: [https://www.redhat.com/en/topics/devops/what-is-ci-cd](https://www.redhat.com/en/topics/devops/what-is-ci-cd))

## Continuous Integration

The concept of CI/CD is driven by the motivation to minimize the time and effort required to deliver quality software while also maximizing the efficiency and productivity of development teams.

In this sense, CI involves regularly integrating code changes from multiple software engineers into the main branch of a shared code repository and then automatically building, testing and validating the code to ensure that it is working correctly. Frequent code integration not only reduces the possibility of having [merge conflicts](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/about-merge-conflicts) between developers, but can also help quickly identifying and fixing any issues and bugs with the help of automated tests (typically unit and integration tests). By ensuring that code changes are regularly integrated and tested, CI can help deliver high-quality software more quickly and efficiently.

## Continuous Delivery

CD is most often referred to as Continuous Delivery. This practice involves uploading the tested changes to a repository such as GitHub or a [container registry](https://www.redhat.com/en/topics/cloud-native-apps/what-is-a-container-registry), and continuously delivering software changes to production or production-like environments (e.g., Staging, Production), where they can be tested and validated in real-world conditions. In this sense, Continuous Delivery automates the release of validated code to a repository with the goal of having always a codebase available for the deployment of production environment. The operations team (or the person responsible for deployments in small teams) is then able to quickly deploy new versions of the application to production.

## Continuous Deployment

CD, on the other hand, is sometimes also referred to as Continuous Deployment, which can be viewed as an extension of Continuous Delivery and represents the final stage of a mature CI/CD pipeline. As the name implies, Continuous Deployment, as opposed to Continuous Delivery, is about automatically deploying software changes to production environments once they have passed all the required testing and validation steps defined in the pipeline. It requires a high degree of automation and testing, and cannot be done without having a proper CI in place. This means that software changes could go live within minutes, considering all tests defined in the pipeline passed. Therefore, Continuous Deployment makes it easier to continuously receive and incorporate feedback from users.

# Github Actions

Github Actions is a powerful **CI/CD solution** provided by GitHub that allows developers to automate their workflows directly within their repositories. With GitHub Actions, it is possible to build, test and deploy the code right from GitHub, making it easy and convenient to ensure the quality and reliability of software.

In the following, the most important components and concepts are briefly addressed and explained. If you want to delve into specific topics in detail, the official GitHub Actions [documentation](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions) is the perfect reference.

## Components

Github Actions uses a workflow-based approach, where a **_workflow_** is a configurable, automated process triggered by specific **_events_** and consists of one or more **_jobs_**. Each job is executed within a virtual machine **_runner_** or container and contains a series of **_steps_**, where these steps represent the individual tasks to be executed. A sample workflow is visualized in Figure 2.

![](https://t4648007.p.clickup-attachments.com/t4648007/c91802b7-4745-4328-8ae0-e6feea9b4acd/image.png)
Figure 2: Sample Workflow visualized

### **Workflows**

As stated earlier, a workflow is a configurable automated process that can run one or more jobs. They are defined using YAML files, which are then placed inside the **_.github/workflows_** directory of a repository. You can set up multiple workflows for building the code, testing the code against the current test suite, checking the formatting, deploying the application, and much more.

### **Events**

Workflows are triggered by events. This can be events concerning pull requests (e.g., open, update or merge a pull request), opening an issue, or pushing a commit to a repository. You can specify the events that trigger a workflow in the YAML file using the `on` keyword. Listing 1 shows an example of how such events can be defined. Specifically, this sample workflow is triggered whenever something is pushed to the **_main_** branch or when a pull request targeting the **_main_** branch is (re)opened or updated. An extensive list of possible events can be found [here](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#about-events-that-trigger-workflows).

```yaml
name: The Name of the Workflow is TestWorkflow
on:
  push:
    branches: ["main"]
  pull-request:
    branches: ["main"]
    types: [opened, synchronize, reopened]
```

Listing 1: Sample events

### **Jobs**

Jobs are a collection of steps that run sequentially on a specific runner. Jobs can run in parallel (which is the default setting) or depend on the success of previous jobs. This dependency can be established with the `needs` keyword which ensures that a job is not executed until the depending job has successfully finished. Listing 2 gives an example of how a job can be defined and a dependency to another one created. More information about jobs can be found [here](https://docs.github.com/en/actions/using-jobs).

```yaml
... previous code

jobs:
  test_job_1:
    name: Test Job 1
  test_job_2:
    name: Test Job 2
    needs: test_job_1
  test_job_3:
    name: Test Job 3
    needs: [test_job_1, test_job_2]
```

Listing 2: Sample jobs

### **Steps**

Steps are individual tasks within a job. Each step can either run a shell command or execute an action, which is a reusable piece of code that can be created by [anyone](https://github.com/marketplace?type=actions). Actions usually perform complex but frequently repeated tasks and are used to reduce code duplication and the complexity of workflow files (more information about actions can be found [here](https://docs.github.com/en/actions/creating-actions)). It is important to highlight that steps are executed sequentially, which means that they are dependent on each other. Since they are all executed on the same runner, they can share data from one step to another. Listing 3 illustrates a job that performs linting on a project. The first step checks out the source code from the repository with the help of an already defined action. The second step then runs the linting process by installing the dependencies and executing the linting command.

```yaml
jobs:
  lint:
    ...
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3
      - name: Check linting
        shell: bash
        run: |
          npm install
          npm run lint
```

Listing 3: Complete linting job

### **Runners**

Runners are the environments where jobs execute. Each runner can run a single job at a time. GitHub provides hosted runners for different operating systems (e.g., Ubuntu Linux, Microsoft Windows or macOS) with each workflow running on a newly provisioned [virtual machine](https://www.redhat.com/en/topics/virtualization/what-is-a-virtual-machine) (VM). There is also the possibility to set up self-hosted runners for greater control and customization. Listing 4 shows how to configure the previously defined job to run on the latest version of an Ubuntu Linux runner. In this sense, when the workflow is triggered the job with the job_id **_lint_** will be executed on a new VM which is hosted by GitHub.

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps: ...
```

Listing 4: Configure runner

## **Contexts**

Contexts are a set of predefined objects that provide information about the environment and the current state of the workflow. They contain meta-data and data that can be used in expressions within a workflow. There are many contexts available, such as the `github` , `job` , or `runner` context. The data within a context can be accessed using expression syntax, i.e., `${{ <context> }}`. In the following, the `env` and `secrets` context is introduced. Further information about other contexts can be found [here](https://docs.github.com/en/actions/learn-github-actions/contexts#about-contexts).

### **Environment variables**

Environment variables are used to store configuration information and other data that can change depending on the environment in which the workflow runs. These variables can be accessed within the workflow steps and are helpful for customizing the behavior of scripts and actions. Environment variables can be defined at different levels within a workflow:

- **Workflow-level**: To define environment variables that are available to all jobs in a workflow, the `env` keyword can be used at the top level of the YAML file:

```yaml
name: The Name of the Workflow is TestWorkflow
on:
  ...
env:
  TESTENV: test_env


jobs:
  some_job:
    runs-on: ubuntu-latest
    steps:
      - name: Run a command with an env variable
        run: echo "TESTENV: ${{env.TESTENV}}" # prints out 'TESTENV: test_env'
```

Listing 5: Workflow-level env variables

- **Job-level**: To define environment variables that are available only to specific jobs, the `env` keyword can be used within the respective job:

```yaml
name: The Name of the Workflow is TestWorkflow
on:
  ...
jobs:
  testJob:
    runs-on: ubuntu-latest
    env:
      TESTENV: test-env
    steps:
      - name: Run a command with an env variable
        run: echo "TESTENV: ${{env.TESTENV}}" # prints out 'TESTENV: test_env'
```

Listing 6: Job-level env variables

- **Step-level**: To define environment variables that are available only to a specific step, the `env` keyword can be used within the respective step:

```yaml
name: The Name of the Workflow is TestWorkflow
on:
  ...
jobs:
  testJob:
    runs-on: ubuntu-latest
    steps:
      - name: Run a command with an env variable
        run: echo "env: $NODE_ENV"
        env:
          NODE_ENV: production
```

Listing 7: Step-level env variables

### **Secrets**

Secrets are used to store sensitive data, such as API keys, passwords or tokens, that you don't want to expose in your repository or logs. They are encrypted and can be accessed only with the GitHub Actions workflow of the same repository. Secrets can be created by visiting the GitHub repository, clicking on "Settings" and then on the "Secrets" tab, where you can eventually add new secrets, provided you have the necessary permissions. Secrets can be accessed with the help of the `secrets` context, as shown in Listing 8. This example makes use of the [labeler action](https://github.com/actions/labeler), which requires the **_GITHUB_TOKEN_** as the value for the `repo-token` input parameter.

```yaml
name: The Name of the Workflow is TestWorkflow
on: ...
jobs:
  testJob:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v4
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
```

Listing 8: Access secrets context

If you use a secrets management platform like [Doppler](https://www.doppler.com/), you can easily integrate it with your GitHub repository, which then allows you to synchronize the secrets with GitHub Secrets. The beauty of this is that you can easily access these secrets in the same way as shown earlier in Listing 8.

## Containerized services

Service containers are [Docker containers](https://www.docker.com/resources/what-container/) that are used to provide additional services or dependencies required by the application during the execution of a workflow. They allow accessing the services they provide without having to install and configure them directly within the runner. They are particularly useful when the application relies on services like databases or memory caching, as it is the case for the [Klicker](https://www.klicker.uzh.ch/home) app. For instance, if we want to run End-to-End tests, the workflow will require access to a database and a memory cache. By using these service containers, we can set up the necessary services for a successful execution of the workflow. This can be done with the help of the `services` keyword as shown in Listing 9.

In this example, two service containers, PostgreSQL service container (i.e., database) and Redis service container (i.e., cache), are created using the official images from [Docker Hub](https://hub.docker.com/). In the case of the PostgreSQL service container, additional environment variables for the username, password and database are provided. The `options` keyword is used for both service containers to define health check commands and further settings, to ensure that both services are ready before proceeding with the workflow.

As mentioned earlier, jobs can be executed on Runners that are hosted by GitHub (i.e., provisioned VMs) or on self-hosted runners where the jobs are executed in containers. Depending on the selection of the runner (VM or container), the communication between jobs and its service containers is different.

- **Running jobs and services in containers** makes network access easier. You don't need to configure any ports because containers in the same Docker network automatically expose all ports to each other, but not outside the network. If you want to understand this magic better, feel free to delve into the details [here](https://docs.docker.com/network/bridge/).
- **When running jobs directly on the runner machine**, as it is the case in Listing 9, access to service containers is provided by using `localhost:<port>` or `127.0.0.1:<port>` . GitHub configures the container network to allow the service containers to communicate with the Docker host (i.e., the VM that is running the Docker containers). We also learned earlier that Docker containers do not automatically expose their ports to the job on the runner. This means that we need to map ports on the service container to the Docker host to enable communication between the job and the containerized service. This can be observed in Listing 9 by the `ports` keyword. In this example, the `ports` keyword maps the port 6379 on the Redis container to port 6380 on the Docker host. This means that the job running on the runner machine can access the Redis service on `localhost:6380` or `127.0.0.1:6380` since any request to port **6380** is forwarded to the Redis container running on port **6379**. Click [here](https://docs.github.com/en/actions/using-containerized-services/about-service-containers#mapping-docker-host-and-service-container-ports) for more information.

```yaml
name: The Name of the Workflow is TestWorkflow
on: ...
jobs:
  testJob:
    runs-on: ubuntu-latest #using GitHub's runner
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_USER: klicker
          POSTGRES_PASSWORD: klicker
          POSTGRES_DB: main
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
      redis:
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        image: redis:7
        ports:
          - 6380:6379
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3
      - name: Run tests
        run: |
          # Command to start the application that interacts with Postgres and Redis
```

Listing 9: Creating containerized services

## Caching

GitHub Actions provide a caching feature that allows you to store and reuse data (e.g, dependencies or build artifacts) across multiple workflow runs. By caching data, the performance of workflows can be significantly improved and build times can be reduced, which in turn allows for faster feedback. This leads to reduced costs, which is all the more important considering that public repositories have [2000 minutes of compute time per month](https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions) included.

The built-in caching mechanism of Github Actions uses the `actions/cache` action, which enables you to cache data between workflow runs, based on a key that you specify. The cache is scoped to the repository and branch, ensuring that each cache is unique and isolated. Two common use-cases for caching are:

- **Dependency caching**: Caching dependencies downloaded or installed by package managers, such as [npm](https://www.npmjs.com/) or [pip](https://pypi.org/project/pip/), can significantly reduce the time it takes to set up your environment and run, for example, your tests.
- **Build artifact caching**: If the build process generates intermediate artifacts that take a long time to create, you can cache those artifacts and reuse them in subsequent workflow runs and therefore reducing the overall build times.

Listing 10 shows an example of how caching could be implemented. After checking out the source code from the repository into the runner's workspace, the next step, which uses the `action/cache@v3` action, is triggered. It caches the npm dependencies and the build artifacts generated by the [Next.js](https://nextjs.org/) framework. The paths to the data we want to cache are specified by the `path` key and include the global npm cache as well as the build caches for the three Next.js applications. Furthermore, the cache `key` is unique and, in this case, generated based on the runner's operating system, a fixed string "nextjs" and the hashes of **_package-lock.json_** and all **_.js_**, **_.jsx_**, **_.ts_**, and **_.tsx_** files in the repository. This ensures that a new cache is created whenever dependencies or source files change. An optional `restore-keys` parameter is also used to specify a fallback cache in case an exact match (i.e., **_cache hit_**) for the cache key isn't found (i.e., **_cache miss_**). In the case of a cache miss, the cache action sequentially searches for any caches that exactly match the restore-keys. If there are still no exact matches, it then looks for partial matches of the restore keys. If there is finally a match, the most recent cache is then restored, otherwise a completely new cache is created.

```yaml

---
- name: Check out repository code
  uses: actions/checkout@v3

- uses: actions/cache@v3
  with:
    path: |
      # Global npm cache
      ~/.npm
      # Build caches for three Next.js applications
      ${{ github.workspace }}/apps/frontend-pwa/.next/cache
      ${{ github.workspace }}/apps/frontend-manage/.next/cache
      ${{ github.workspace }}/apps/frontend-control/.next/cache
    # Generate a new cache whenever packages or source files change.
    key: ${{ runner.os }}-nextjs-${{ hashFiles('**/package-lock.json') }}-${{ hashFiles('**.[jt]s', '**.[jt]sx') }}
    # If source files changed but packages didn't, rebuild from a prior cache.
    restore-keys: |
      ${{ runner.os }}-nextjs-${{ hashFiles('**/package-lock.json') }}-

- name: Install dependencies
  uses: bahmutov/npm-install@v1
```

Listing 10: Caching dependencies and artifacts

## Workflow Artifacts

Workflow artifacts are files or collection of files generated during the execution of a workflow. They are then stored, associated with a specific workflow run, and made available for download. These artifacts can include a variety of files, such as binaries, log files, test results, code coverage results, or any other output resulting from the build process. Storing artifacts is useful for various purposes, including sharing or auditing build process, deployment, further analysis, or simply for troubleshooting.

Other than **_build artifact caching_**, which is used to temporarily store intermediate files to improve build times (see section **_Caching_**), workflow artifacts are meant to save the final output of the workflow (e.g., build process, testing results) for later access, analysis, or deployment.

### Upload artifacts

To upload and store artifacts, the `actions/upload-artifact` action can be used within the workflow as illustrated in Listing 11. After installing, building, and testing the project, this action archives the produced artifacts by specifying the name of the artifacts with the `name` keyword and also the directory path with the `path` keyword. A custom retention period with the help of `retention-days` can be defined as well (see _Archive code coverage results_ step). However, this value cannot exceed the retention limit set by the repository or organization.

```yaml
name: Node CI

on: [push]

jobs:
  build_and_test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3
      - name: npm install, build, and test
        run: |
          npm install
          npm run build --if-present
          npm test
      - name: Archive production artifacts
        uses: actions/upload-artifact@v3
        with:
          name: dist-without-markdown
          path: |
            dist
            !dist/**/*.md
      - name: Archive code coverage results
        uses: actions/upload-artifact@v3
        with:
          name: code-coverage-report
          path: output/test/code-coverage.html
          retention-days: 5
```

Listing 11: Upload build and test artifact

### Download artifacts

To download artifacts, the `actions/download-artifact` can be used. However, this action only allows downloading artifacts in subsequent jobs or steps **within the same workflow run**. Listing 12 shows how a previously uploaded artifact called **_my-artifact_** could be downloaded. If a name has not been defined for the download, the default name would be **_artifact_**. Leaving out the `name` parameter results in downoading all artifacts that have been uploaded during the same workflow. Other than that, all uploaded artifacts can be downloaded outside a workflow with the GitHub UI for further analysis, for example.

```yaml
- name: Download a single artifact
  uses: actions/download-artifact@v3
  with:
    name: my-artifact
```

Listing 12: Download single artifact

## Job Matrices

Matrices provide a nice way to run a job with multiple combinations of environment variables, operating systems, or any other configurations. Such matrix configurations help you execute the same job with different parameters, enabling efficient testing across multiple environments, platforms, or configurations with minimal code duplication.

To define a matrix strategy, you need to use the `matrix` keyword under the `strategy` section of a job. This allows to specify the variation of the configurations you want to use in the matrix. Listing 13 shows an example of a matrix that runs a job on three different versions of [Node.js](https://nodejs.org/en) across two different operating systems. The job **_test_** will run on the latest versions of Ubuntu and Windows, and it will use Node.js versions 14, 16, and 18. In this sense, the matrix creates a total of 6 combinations (2 operating systems \* 3 Node.js versions), with the specified job running for each of them. The matrix variables, such as `${{ matrix.os }}` and `${{ matrix.node }}` , are used to reference current combination's configuration when defining steps in the job.

```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [14, 16, 18]
    runs-on: ${{ matrix.os }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test
```

Listing 13: Using a matrix strategy

There are even more configurations that can be applied to the matrices, such as customizing the maximum number of concurrent jobs or adding, expanding, or excluding configurations. To explore these kind of possibilities you can visit this [page](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs).

## Generic Examples

In this section, you can find some initial examples of GitHub Action workflows that demonstrate many of the concepts covered on this site. Additional examples will be added over time to cover a wider range of common workflows.

### Lint and publish a NPM package

Example 1 is triggered by a `push` event to the repository and demonstrate best practices for building, testing and publishing a NPM package. A possible suggestion for improvement could be caching the npm dependencies to avoid re-downloading dependencies every time the workflow is run, therefore improving the performance as described in the **_Caching_** section.

```yaml
name: CI
on: [push]
jobs:
  lint:
    runs-on: ubuntu-latest
    name: Lint
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Use Node 18
        uses: actions/setup-node@v3
        with:
          node-version: 18.x

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - run: npm run lint

  check:
    runs-on: ubuntu-latest
    name: Check Types
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Use Node 18
        uses: actions/setup-node@v3
        with:
          node-version: 18.x

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - run: npm run check

  test:
    runs-on: ubuntu-latest
    name: Test
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Use Node 18
        uses: actions/setup-node@v3
        with:
          node-version: 18.x

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - run: npm run test --ci --coverage --maxWorkers=2

  build:
    name: Build and Publish
    runs-on: ubuntu-latest
    needs: [lint, check, test]
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Use Node 18
        uses: actions/setup-node@v3
        with:
          node-version: 18.x

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - name: Build
        run: npm run build

      - uses: JS-DevTools/npm-publish@v1
        with:
          token: ${{ secrets.NPM_TOKEN }}
          access: public
          check-version: true

      - uses: JS-DevTools/npm-publish@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          registry: https://npm.pkg.github.com
          access: public
          check-version: true
```

Example 1: [Lint, test, build, and publish](https://github.com/uzh-bf/design-system/blob/main/.github/workflows/main.yml) a NPM package

### Publish a docker image

Example 2 shows a workflow that is designed to build and push (only if it's not a pull request) a Docker image for Klicker's **_frontend-manage_** application. The purpose of this workflow is to ensure that the Docker image is built and pushed to the specified registry whenever there are changes to the application code or the workflow itself, using the appropriate environment configuration for the **_Staging_** (i.e., **_QA_**) environment.

```yaml
name: Build Docker image for frontend-manage (QA)

on:
  push:
    branches:
      - "v3"
    paths:
      - "apps/frontend-manage/**"
      - ".github/workflows/v3_frontend-manage**"
  pull_request:
    # branches: [ "v3" ]
    paths:
      - "apps/frontend-manage/**"
      - ".github/workflows/v3_frontend-manage**"

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}/frontend-manage

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Replace .env.production with .env.qa
        shell: bash
        run: |
          rm apps/frontend-manage/.env.production
          mv apps/frontend-manage/.env.qa apps/frontend-manage/.env.production
          cp -R packages/shared-components apps/frontend-manage/shared-components

      - name: Set up QEMU
        uses: docker/setup-qemu-action@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Log into registry ${{ env.REGISTRY }}
        if: github.event_name != 'pull_request'
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract Docker metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v3
        with:
          context: apps/frontend-manage
          file: apps/frontend-manage/Dockerfile
          push: ${{ github.event_name != 'pull_request' }}
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```

Example 2: [Build and publish](https://github.com/uzh-bf/klicker-uzh/blob/v3/.github/workflows/v3_frontend-manage-docker-qa.yml) a docker image

### Share data between jobs

The workflow in Example 3 is triggered on a `push` event. The workflow consists of three jobs that perform caluclations and share results using artifacts. It's noteworthy to highlight that jobs that are dependent on a previous job's artifact must wait for the dependent job to complete sucessfully. Hence, the `needs` parameter has to be appropriately configured.

```yaml
name: Share data between jobs

on: [push]

jobs:
  job_1:
    name: Add 3 and 7
    runs-on: ubuntu-latest
    steps:
      - shell: bash
        run: |
          expr 3 + 7 > math-homework.txt
      - name: Upload math result for job 1
        uses: actions/upload-artifact@v3
        with:
          name: homework
          path: math-homework.txt

  job_2:
    name: Multiply by 9
    needs: job_1
    runs-on: windows-latest
    steps:
      - name: Download math result for job 1
        uses: actions/download-artifact@v3
        with:
          name: homework
      - shell: bash
        run: |
          value=`cat math-homework.txt`
          expr $value \* 9 > math-homework.txt
      - name: Upload math result for job 2
        uses: actions/upload-artifact@v3
        with:
          name: homework
          path: math-homework.txt

  job_3:
    name: Display results
    needs: job_2
    runs-on: macOS-latest
    steps:
      - name: Download math result for job 2
        uses: actions/download-artifact@v3
        with:
          name: homework
      - name: Print the final result
        shell: bash
        run: |
          value=`cat math-homework.txt`
          echo The result is $va
```

Example 3: Share data between jobs

The workflow run archives all artifacts that are generated. In this case, however, it is only one artifact, since we always use the same name and thus overwrite the uploaded artifacts twice. This is illustrated in Figure 3.

![](https://t4648007.p.clickup-attachments.com/t4648007/1472d386-7e70-49dd-98f5-063dde6bd5b6/image.png)
Figure 3: Workflow run captured in [GitHub](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts#downloading-or-deleting-artifacts)

### SonarCloud analysis

Example 4 performs a SonarCloud analysis on the v3 branch and pull requests targeting the v3. It demonstrates how to integrate SonarCloud analysis into a workflow and can help ensure the code quality and maintainability of a project.

```yaml
name: Perform sonarcloud analysis
on:
  push:
    branches:
      - v3
  pull_request:
    branches: ["v3"]
    types: [opened, synchronize, reopened]
jobs:
  sonarcloud:
    name: SonarCloud
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0 # Shallow clones should be disabled for a better relevancy of analysis
      - name: SonarCloud Scan
        uses: SonarSource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} # Needed to get PR information, if any
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

Example 4: Perform [SonarCloud analysis](https://github.com/uzh-bf/klicker-uzh/blob/v3/.github/workflows/v3_sonarcloud.yml)
