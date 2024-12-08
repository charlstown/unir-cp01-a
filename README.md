# Case Study 1.1-A

In this exercise, I chose to run the solution using Docker containers, deploying Jenkins and Wiremock as services. The setup includes running the Flask API locally for initial testing, followed by a complete CI/CD pipeline managed within Dockerized Jenkins.

- [Test Flask API locally](#test-flask-api-locally)
- [Test Flask API on jenkins](#test-flask-api-on-jenkins)

---

## Test Flask API locally

1. [Lanunch the API](#lanunch-the-api)
2. [Deploy the Wiremock container](#deploy-the-wiremock-container)
3. [Run the tests locally](#run-the-tests-locally)

### Lanunch the API

1. Navigate to the repository path.

    ```bash
    cd unir-cp01-a
    ```

2. Set the aplication file in the environment variables and run Flask,

    ```bash
    export FLASK_APP=app/api.py
    flask run
    ```

    or point at the app in the same running command

    ```bash
    flask --app app/api.py run
    ```

3. Check the running API locally at for example

    ```bash
    http://127.0.0.1:5000/calc/add/5/3
    ```

### Deploy the Wiremock container

In order to pass the REST Test, you need to run the Wiremock service with the mapping file.

1. Deploy the Wiremock service container with the follorwing command.

    ```bash
    sudo docker compose -f .docker/dockercompose-wiremock.yml up
    ```

2. Call the Wiremock endpoint to check is up and running.
    
    ```bash
    http://0.0.0.0:9090/calc/sqrt/64
    ```

    *Make sure the `BASE_URL_MOCK` variable on the file `api_test.py` is set to `http://localhost`.*

### Run the tests locally

1. To run the Unit Test simply run the following command:

    ```bash
    python -m pytest test/unit/
    ```

2. To run the REST test run this command:

    ```bash
    python -m pytest test/rest/
    ```

    
## Test Flask API on jenkins

1. [Deploy the container services](#deploy-the-container-services)
2. [Run the tests from JenkinsFile](#run-the-tests-from-jenkinsfile)

### Deploy the container services

To Deploy the Jenkins and the Wiremock service in a container, simply run the follorwing command.

    ```bash
    sudo docker compose -f .docker/dockercompose.yml up
    ```

Visit the Jenkins url at http://0.0.0.0:8080/ the container should have this port exposed.

### Run the tests from JenkinsFile

 1. Create a new item in Jenkins, select `Pipeline`, and click `OK`.
 2. In the configuration, set Pipeline Definition to `Pipeline script from SCM`, select Git, and add this repository URL.
 3. Point the Script Path to the Jenkinsfile in the root folder, save the pipeline, and click Build Now.