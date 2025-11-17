# F3

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

# F4

To satisfy F4, GitHub Actions workflows were added to both model-service and app repositories `(.github/workflows/release-image.yml)`
These workflows automatically build Docker images and publish them to GitHub Container Registry (GHCR) whenever a version tag is pushed.

## Triggering the workflow
A new version tag was created and pushed:
```
git tag v0.1.1
git push origin v0.1.1
```
This triggers the `release-image.yml` workflow, which can be observed in each repository’s Actions tab.

## Pulling the Published Images
After the workflow completes successfully, the generated images become available on GHCR and can be pulled locally:
Model Service: `docker pull ghcr.io/doda25-team6/model-service:v0.1.1`
App: `docker pull ghcr.io/doda25-team6/app:v0.1.1`

## Testing the Images
To verify that the published images run correctly, they were executed locally:
Model Service: `docker run -p 8081:8081 ghcr.io/doda25-team6/model-service:v0.1.1`
App: `docker run -p 8080:8080 ghcr.io/doda25-team6/app:v0.1.1`