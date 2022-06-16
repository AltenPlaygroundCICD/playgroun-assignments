# ALTEN Playground CI/CD

Welcome to the ALTEN playground on Continuous Integration and Continuous Deployment. In this playground you will make your own pipelines and run them via Github actions.

## 1. Continuous integration

### 1.1. Getting ready

To start the playground assignments you must have a Github account. Create one on [Github](https://github.com/signup). After login fork our example repository in [AltenPlaygroundCICD](https://github.com/AltenPlaygroundCICD). Feel free to pick Python or C++ as a starter. Note that our C++ example might require some extra effort to get going.

### 1.2. Run your first pipeline

Use Github actions to kick-start your first pipeline with predefined jobs. Try and get your unit test running. Github will have a Python app starter and CMake starter available to pick and quickly get up and running. As soon as you add the workflow files to the repository Github will pickup on the instructions and schedule your pipeline to run.

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
apt-get install clang-format
```

```bash
clang-format -n -- $(git ls-files '*.cpp' '*.h')
```

### 1.6. Ensure a Pull Request cannot be merged if the build has failed

Go and make a faulty commit on a new branch. This is to check your pipeline will pick-up on parts that are not intended. Are Pull Requests blocked if the code is not passing your checks?

### 1.7. Publish your artifacts

As a final step you can publish artifacts on your buildables. Add an action to Upload a Build Artifact to store your precious buildables. These can be downloaded in the workflow summary if you open one of your flows. For Python you might want to create a package to install? Or just a precompiled version of a file? In the C++ example you can upload the binary that is created in the pipeline.

## 2. Continuous deployment

For Continuous deployment we will use generated documentation as example. For the Python example you can use `Sphinx` to run these. For C++ you may use `Doxygen`.

The documentation will be hosted publicly on Github Pages, so make sure you do not publish confidential information.

### 2.1. Generate your code documentation

For Python:

To generate code documentation you have to add the tool to your work environment. For `Sphinx` you can add this tool with `pip`.

```bash
pip install sphinx
```

```bash
sphinx-apidoc -o . index
make html
```

Your website will be available in `./_build/html`

For C++:

Generating code documentation for c++ requires different steps, which requires the use of `Doxygen` instead.

```bash
apt-get install doxygen
```

```bash
doxygen Doxyfile
```

Your website will be available in `./Doxygen/html`

### 2.2. Activate pages for the playground repository

Github Pages are activated on account level or repository level. For this assignment you should active pages on repository level. This will create a shareable link for you to access the website.

[Guide on Github pages](https://github.com/marketplace/actions/deploy-to-github-pages)

### 2.3. Deploy the pages

After creation add the deploy step to your workflow. This step will upload all html files to a storage location provided by Github. After upload it might take up to 5 minutes to publish all files on your new page. Is everything in there?

## 3. Deep dive

### 3.1. Status badges

With Github actions, it is possible to provide workflow status badges which show whether a workflow is currently failing or passing. A common place to place a status badge is in the `README.md`.

A template can be generated, which can be found under Actions > Workflows. This template can be placed in `README.md`.

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

Some builds might take quite some time to complete. Parts of a build could be cached to speed up next builds in your repository. For Python this could be your virtual environment with all packages to install. For C++ it might be your dependencies, but better is ccache. Can you include a cache to make subsequent builds faster?

### 3.4. Code coverage

Having tests for your code is important. Having automated tests is even more important as you can´t forget to run them. All pull requests should run these to guarantee the quality of your team. With code coverage you can see what lines are part of existing unit tests and where gaps are to cover. Having code coverage in your pipeline allows you to track coverage over time and also drops in coverage. If a pull request lowers the total coverage you could automatically decline the contribution as someone did not create tests for the new code? Try to add a figure on your code coverage per cent. For this example just a print in the build output is fine.

### 3.5. Move all tools into your own container

During the assignments we added more tools to use during the pipeline. This demonstrates you can modify your environment as you go. A drawback of adding more tools is that on every run you have to spent some time to download and install these. Depending on the size of tools this might impact your runtime quite significantly.

Alternatively, you can encapsulate all required tools in a prebuild container. You can take the container used in this pipeline as a base template and add a few more tools. Push this container to a registry like the GitHub Container Registry and reference it from the pipeline. Any run starting with this container can skip all steps to install the tools. Can you make a pipeline to build this container automatically and use it in the playground pipeline to build your work?

### 3.6. Templating the job

Now that you have a complete pipeline running you might want to reuse these steps in another repository. You could copy the file and commit that. However, if you ever have to change the container you use, build paths or even just steps you must remember to update all copies.

Another strategy is to use templates. These can be included in your pipelines and describe generic steps to take. For example, you could make a template to run a linter, build unit tests and perhaps even deploy documentation. All your repositories will run these steps.



