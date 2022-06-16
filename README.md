# ALTEN Playground CI/CD

Welcome to the ALTEN playground on Continuous Integration and Continuous Deployment. In this playground you will make your own pipelines and run them via Github actions.

## 1. Continuous integration

### 1.1. Getting ready

To start the playground assignments you must have a Github account. Create one on [Github](https://github.com/signup). After login fork our example repository in [AltenPlaygroundCICD](https://github.com/AltenPlaygroundCICD). Feel free to pick Python or C++ as a starter. Note that our C++ example might require some extra effort to get going.

### 1.2. Run your first pipeline

### 1.3. Make the unit tests work

In the provided example you will notice the first run fails. Oops! Some unit tests are not correct.

Open your repository and navigate to the file containing all unit tests. Try to repair the failing tests and push again. Once you fix the test commit on a feature branch and create a pull request. _Note:_ You could do this via the web editor of Github.

### 1.4. Run the unit tests on push to repository

Are the tests running again? Good job! But starting the pipeline by hand is not ideal, what if someone forgets to run the pipeline after a bad merge? Lets get ahead of that and ensure the pipeline always activates whenever a new push comes in. That will keep bad commits out of your work.

In your workflow add the following to define a trigger to start on pushes to `develop` or pull requests to `develop`.

```yaml
on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]
```

Does your pipeline run after adding commits to develop?

### 1.5. Run a linter on the code

Tests are running now, but did we manage to use best practises in formatting our code? Using a linter or static checker we can have a quick glance at our code quality before we create releases.

First we need to add a new tool that will run these checks. Once the tool is in we can activate it in the pipeline.

For Python:

```bash
python -m pip install --upgrade pip
pip install pylint
```

```bash
pylint $(git ls-files '*.py')
```

For C++:

```bash
TODO: apt-get install clang?#?#
```

```bash
TODO: clang-format ...
```

### 1.6. Ensure a Pull Request cannot be merged if the build has failed

### 1.7. Publish your artifacts

## 2. Continuous deployment

For Continuous deployment we will use generated documentation as example. For the Python example you can use `Sphinx` to run these. For C++ you may use `Doxygen`.

The documentation will be hosted publicly on Github Pages, so make sure you do not publish confidential information.

### 2.1. Generate your code documentation

To generate code documentation you have to add the tool to your work environment. For `Sphinx` you can add this tool with `pip`.

```bash
pip install sphinx
```

```bash
sphinx-apidoc -o . index
make html
```

Your website will be available in `./_build/html`

### 2.2. Activate pages for the playground repository

Github Pages are activated on account level or repository level. For this assignment you should active pages on repository level. This will create a shareable link for you to access the website.

[Guide on Github pages](https://github.com/marketplace/actions/deploy-to-github-pages)

### 2.3. Deploy the pages

After creation add the deploy step

## 3. Deep dive

### 3.1. Status badges

TODO: Furkan

https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge

### 3.2. Feedback in pull requests

With Github actions, it is possible to interact with e.g. issues an pull requests. Some examples that one might want to do:

- Automatically assign a random reviewer to a pull request
- Greet a first time contributer on their first issue / pull request
- Leave a comment with the code coverage results in a pull request

All of these can be achieved by using the official [github-script action](https://github.com/marketplace/actions/github-script). This action allows you to run javascript within the action, and interact with various components in Github using the [octokit](https://octokit.github.io/rest.js) Github javascript client.

Some challenges:

- See if you can leave comments on pull requests or issues.
- Gather some build output (e.g. the binary's filesize), and include it in a comment.

### 3.3. Caches

TODO:

### 3.4. Code coverage

TODO:

### 3.5. Move all tools into your own container

During the assignments we added more tools to use during the pipeline. This demonstrates you can modify your environment as you go. A drawback of adding more tools is that on every run you have to spent some time to download and install these. Depending on the size of tools this might impact your runtime quite significantly.

Alternatively, you can encapsulate all required tools in a prebuild container. You can take the container used in this pipeline as a base template and add a few more tools. Push this container to a registry like the DockerHub and reference it from the pipeline. Any run starting with this container can skip all steps to install the tools.

### 3.6. Templating the job

Now that you have a complete pipeline running you might want to reuse these steps in another repository. You could copy the file and commit that. However, if you ever have to change the container you use, build paths or even just steps you must remember to update all copies.

Another strategy is to use templates. These can be included in your pipelines and describe generic steps to take. For example, you could make a template to run a linter, build unit tests and perhaps even deploy documentation. All your repositories will run these steps.



