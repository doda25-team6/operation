# Extension Proposal

## Identified Shortcoming
The current model training and release pipeline (F9 workflow) lacks automated integration testing between the trained model and the app-service. This leads to potential runtime errors when new models are deployed, as the app-service may fail to load or use the model correctly, discovered only after deployment.

## Effect
- Increased risk of production failures.
- Manual testing required before each release.
- Delays in experimentation cycles.

## Proposed Extension
Implement automated integration tests in the model release workflow that deploy the model to a staging environment and run end-to-end tests with the app-service.

### Implementation Steps
1. Add a staging Kubernetes namespace in the operation repo.
2. Modify the GitHub Actions workflow to deploy to staging after training.
3. Run automated tests (e.g., via Selenium or API tests) against staging.
4. Only promote to production if tests pass.

### Expected Outcome
- Faster feedback on model compatibility.
- Reduced production incidents.
- Improved CI/CD reliability.

### Testing the Change
Run A/B tests comparing deployment success rates and time-to-detect issues before and after the change.

### Sources
- [Kubernetes Testing Best Practices](https://kubernetes.io/docs/concepts/cluster-administration/testing/)
- [GitHub Actions for Kubernetes](https://docs.github.com/en/actions/deployment/deploying-to-your-cloud-provider/deploying-to-kubernetes)