## Apply a Greenfield app to ploigos
This repository is intended to serve as a proof of concept for running a greenfield Maven application through a DevSecOps pipeline created by the [ploigos-software-factory-operator](https://github.com/ploigos/ploigos-software-factory-operator).
1. Clone this repository and cd into it:
    ```bash
    git clone https://github.com/andykrohg/ploigos-onboarding-demo.git
    cd ploigos-onboarding-demo
    ```
2. Generate a brand new application using the `quarkus-maven-plugin`:
    ```bash
    mvn io.quarkus:quarkus-maven-plugin:1.12.2.Final:create \
        -DprojectGroupId=org.acme \
        -DprojectArtifactId=getting-started \
        -DclassName="org.acme.getting.started.GreetingResource" \
        -Dpath="/hello"
    ```
3. Add configuration to your project to wire it up for Ploigos. Create the directory `getting-started/cicd/ploigos-step-runner-config`:
    ```bash
    mkdir -p getting-started/cicd/ploigos-step-runner-config
    ```
   Add a Jenkinsfile to `getting-started/cicd`, which simply invokes the `ploigos-jenkins-library`:
    ```bash
    cp Jenkinsfile getting-started/cicd/
    ```
   Add the provided `config.yml` to `getting-started/cicd/ploigos-step-runner-config`. This file is comprised of application-level properties that complement platform-level configuration, which is managed by your `TsscPlatform` in a `ConfigMap` called `ploigos-platform-config`. Both config files will be passed to `ploigos-step-runner` for each pipeline step.
    ```bash
    cp config.yml getting-started/cicd/ploigos-step-runner-config/
    ```
   Add the provided `sonar-project.properties` file to the `getting-started` directory. This serves as a generic configuration which tells the Sonarqube CLI, `sonar-scanner`, where to look for scanning targets during the `static-code-analysis` pipeline step.
    ```bash
    cp sonar-project.properties getting-started/
    ```
4. Initialize `getting-started` as a git repository, then push to github (or wherever):
    ```bash
    cd getting-started
    git init -b main
    git add .
    git commit -m "Anchors away! ⚓"
    git remote add origin https://github/<your account>/getting-started.git
    git push origin main
    cd ..
    ```
5. (Optional) Create a fork of the [ploigos-cloud-resources-template](https://github.com/andykrohg/ploigos-cloud-resources-template), particularly if you'd like to update the name of the chart [here](https://github.com/andykrohg/ploigos-cloud-resources-template/blob/main/Chart.yaml#L2). Otherwise, your Kubernetes assets will have the default name `myapp`.
6. Replace line 10 of `tssc-pipeline.yml` with the URL for your `getting-started` repository (pushed in Step 4). If you created a fork of the helm repo in Step 5, replace line 15 with your fork's URL as well.
7. Ensure that you're in the project where your `TsscPlatform` lives, and create a `TsscPipeline` from file:
    ```bash
    oc project devsecops
    oc apply -f tssc-pipeline.yml
    ```