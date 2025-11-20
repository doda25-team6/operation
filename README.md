## F3

Navigate to `operation` and start up the containers for model-service and app by running the following command:
`docker compose up --build`
(Ensure Docker is up and running)

The services can be accessed on:
http://localhost:8080
POST → http://localhost:8081/predict (API endpoint)

To test whether the backend is running, run:
```
curl -X POST http://localhost:8081/predict \ 

     -H "Content-Type: application/json" \ 

     -d '{"sms":"hello world"}' 
```

To stop the containers: `docker compose down`

To run the containers individually:

Navigate to model-service, run the following commands: 
`docker build -t model-service-test .`
`docker run -p 8081:8081 model-service-test` 

Navigate to app, run the following commands: 
`docker build -t app-service-test .`
`docker run -p 8080:8080 app-service-test`

or run the commands provided in the README file within the app repository.

## F4

To satisfy F4, GitHub Actions workflows were added to both model-service and app repositories `(.github/workflows/release-image.yml)`
These workflows automatically build Docker images and publish them to GitHub Container Registry (GHCR) whenever a version tag is pushed.

## Triggering the workflow
A new version tag was created and pushed:
```
git tag v0.1.1
git push origin v0.1.1
```
This triggers the `release-image.yml` workflow, which can be observed in each repository’s Actions tab.

### Pulling the Published Images
After the workflow completes successfully, the generated images become available on GHCR and can be pulled locally:
Model Service: `docker pull ghcr.io/doda25-team6/model-service:v0.1.1`
App: `docker pull ghcr.io/doda25-team6/app:v0.1.1`

### Testing the Images
To verify that the published images run correctly, they were executed locally:
Model Service: `docker run -p 8081:8081 ghcr.io/doda25-team6/model-service:v0.1.1`
App: `docker run -p 8080:8080 ghcr.io/doda25-team6/app:v0.1.1`

## F6

To satisfy F6, environment variables were added into the Dockerfiles for `model-service` and `app`. Below are given the environment variables that can be modified in `operation/docker-compose.yml` or when running the components individually:

### Environment variables in `app`

- `SERVER_PORT`: The port on which the app runs within the container (default: 8080)
- `MODEL_HOST`: The URL on which model service runs for the app to use, relative to the app within the container (default: http://model-service:8081)

### Environment variable in `model-service`

- `SERVER_PORT`: The port on which the service runs within the container (default: 8081)

## F9

In the `model-service` repository, a dedicated workflow was created, which can be run on demand. 

The workflow is triggered by navigating to the Actions tab in the GitHub web UI, clicking "On-demand training, versioning, and release of the model" on the left, and pressing "Run workflow." You will be asked for a release tag, which represents a semantic version, e.g., v0.0.1. Note that existing tags cause the workflow to fail its run.

Afterward, the workflow will run and create a release at https://github.com/doda25-team6/model-service/releases. The release contains corresponding versioned model files, which can be downloaded without authentication.

The workflow consists of two jobs, namely `train-and-version` and `release`. This design decision was made so that training and versioning could be performed inside the same Docker Python container, which avoids user permission issues. Releasing the model files is not done inside this container, but in the GitHub runner. That is why, two jobs are used, where the output files from the first job are downloaded to the second (dependent) job.

## F10

A few changes have been made to remove the hard-coded use of the model by the model-service container. Firstly, the Docker Compose file in `operation` was extended with an environement variable and a volume mount. Secondly, the Dockerfile and `serve_model.py` were changed in `model-service`.

The Dockerfile does not have an environment variable for the model file location. This is defined in the Docker Compose file only, to simplify the configuration.

In `serve_model.py`, logic has been added to check whether the model can be loaded in the container, or if it has to be downloaded from the GitHub releases page in the `model-service` repository. It has been chosen to use a global variable `clf` in `serve_model.py`, instead of using Flask's app config, as it's expected to work well given the relatively small scale of the project.
