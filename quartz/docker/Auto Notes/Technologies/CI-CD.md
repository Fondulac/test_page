# CI-CD with Gitlab 

[Documentation](https://docs.gitlab.com/ee/ci/)

CI/CD stands for *continuous integration / continuous development*. As stated in the name, CI/CD allows an application to be continuously built and changed while it's live. 

## Step 1 - ```.gitlab-ci.yml```

The .gitlab-ci-yml file specifies stages, jobs, and scripts to be executed during the CI/CI pipeline. It consists of a yaml file with its own custom syntax. 

[Basics](https://about.gitlab.com/blog/2020/12/10/basics-of-gitlab-ci-updated/)
[Syntax](https://docs.gitlab.com/ee/ci/yaml/index.html)

## Step 2 - Find or create runners 

Runners are the agents that execute the jobs specified by the gitlab-ci.yml file. 

Runners perform the following: 
- Loads the image
- Clones the project
- Runs the job locally or within the container 

[Runners Documentation](https://docs.gitlab.com/runner/)

## Step 3 - Define pipelines

The pipeline is defined in the .gitlab-ci.yml file
Pipelines consist of ==jobs== and ==stages==

Stages define the order of execution - can be called whatever, such as build, test, deploy 

Jobs specify the tasks to be performed in each stage. For example, a job can compile or test code. 

A pipeline can be triggered by almost anything - commits, merges, issues - or can be scheduled. 

[Pipeline Editor](https://docs.gitlab.com/ee/ci/pipeline_editor/index.html)
[Pipeline Visualizer](https://docs.gitlab.com/ee/ci/pipeline_editor/index.html#visualize-ci-configuration)
[Pipeline Documentation](https://docs.gitlab.com/ee/ci/pipelines/index.html)

## Step 4 - Use CI/CD variables as part of jobs

Gitlab CI/CD variables are key-value pairs used to store and pass configuration settings and sensitive information, such as passwords and API keys to jobs in a pipeline. 

These variables can be hard coded into the .gitlab-ci.yml file or set in the project settings. They can also be generated dynamically. 

Two types of variables - 
- Custom variables defined by the user in the GitLab UI, API, or configuration files
- Predefined variables set automatically by GitLab to provide information about the current job, pipeline, and environment. 

protect variables by marking them as protected or masked. 
- Protected variables are only available to jobs running on protected branches or logs
- Masked variables have their values hidden to job logs in prevent sensitive information from being exposed. 

[CI/CD Variables](https://docs.gitlab.com/ee/ci/variables/index.html)
[Dynamically generated predefined variables](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html)

## Step 5 - Using CI/CD components 

Components are reusuable pipeline configuration units. Use a CI/CD component to compose an entire pipeline configuration or small part of a larger pipeline. 

Add components to configuration using ```include: component```

