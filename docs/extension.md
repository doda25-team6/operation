# Extension Proposal

## Identified Shortcoming
The current model training and release pipeline (F9 workflow) produces a deployable model artifact but lacks automated integration testing between the trained model and the app-service. This leads to potential runtime errors when new models are deployed, as the app-service may fail to load or use the model correctly, discovered only after deployment.

## Effect
- Higher risk of production incidents or degraded user experience after model updates.
- Manual validation required for each model release, slowing iteration.
- Reduced confidence in continuous experimentation because "new version" may fail for operational reasons.

## Proposed Extension
Implementing automated integration tests in the model release workflow that deploy the model to a staging environment and run end-to-end tests with the app-service.


### Implementation Steps
1. Add a staging Kubernetes namespace in the operation repo.
2. Update F9 action workflow: after training, package the model and deploy to staging.
3. Run automated tests (e.g., via Selenium or API tests) against staging.
4. Only promote to production if the tests pass.

### Expected Outcome
- Faster feedback on model compatibility.
- Reduced production incidents.
- Improved CI/CD reliability.

### Testing the Change
Compare the before vs after over multiple model releases:
- Change failure rate
- Mean Time to Detect - MTTD for model compatability
- Lead time from training completion to production-ready release

(Runnning A/B tests comparing deployment success rates and time-to-detect issues before and after the change.)

### Sources
- https://sre.google/sre-book/release-engineering/
- https://argo-rollouts.readthedocs.io/en/stable/concepts/