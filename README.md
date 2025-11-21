# SMS Spam Detection System - Implementation Overview

## Repository Links (Tag: a1)
- **[app](https://github.com/doda25-team6/app/tree/a1)** - Spring Boot frontend and API gateway
- **[model-service](https://github.com/doda25-team6/model-service/tree/a1)** - Flask ML backend with scikit-learn
- **[lib-version](https://github.com/doda25-team6/lib-version/tree/a1)** - Version-aware Maven library
- **[operation](https://github.com/doda25-team6/operation/tree/a1)** - Docker Compose orchestration (this repository)

## Quick Start
```bash
git clone https://github.com/doda25-team6/app.git
git clone https://github.com/doda25-team6/model-service.git
git clone https://github.com/doda25-team6/lib-version.git
git clone https://github.com/doda25-team6/operation.git
cd operation
git checkout a1
docker compose up --build
```

Access the application at http://localhost:8080/sms/
and the model-service API at http://localhost:8081/

---

## Feature Implementation Details

## F1: Version-aware Library

A version-aware Maven library `lib-version` has been created in the [lib-version repository](https://github.com/doda25-team6/lib-version/tree/a1). This library contains a `VersionUtil` class that provides version information parsed from a `version.properties` resource file included in the package.

### Implementation Details
- **Version Storage**: Version information stored in `src/main/resources/version.properties`
- **Integration**: The `app` service depends on this library and uses `VersionUtil` during startup
- **Usage**: Version information is logged when the application starts, providing runtime version awareness

## F2: Library Release

The `lib-version` library has automated release workflows implemented via GitHub Actions. The library is automatically packaged, versioned, and released to the GitHub Package Registry for Maven.

### Workflow Details
- **Automation**: GitHub Actions workflow in `.github/workflows/release.yml`
- **Trigger**: Git version tags (e.g., `v1.0.0`) trigger the release workflow
- **Registry**: Published to GitHub Package Registry for Maven
- **Versioning**: Automatic version determination from git tags
- **Packaging**: Maven packaging with proper dependency management

### Usage
The released library can be consumed by adding the GitHub Package Registry as a Maven repository and including the dependency:

```xml
<dependency>
    <groupId>team6</groupId>
    <artifactId>lib-version</artifactId>
    <version>1.0.0</version>
</dependency>
```

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
