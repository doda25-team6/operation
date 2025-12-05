### WEEK 1 (10/11 - 16/11)

<br> 
 <details><summary> Valantis </summary>
<p>

**A1**:

https://github.com/doda25-team6/app/commit/e1dc7ab288ea037aac5453a578fbde07e1c23512

first commit to set up project sructure in frontend

https://github.com/doda25-team6/model-service/commit/fac570f3ac93b900b8144a99be8637ee37f2f17b

initial commit to set up project structure in model-service

https://github.com/doda25-team6/lib-version/pull/1

feat(F-1): Implement version-aware library with VersionUtil

https://github.com/doda25-team6/lib-version/pull/2

feat(F-2): Add GitHub Actions workflow for library release

https://github.com/doda25-team6/app/pull/2

feat(F-1): Integrate lib-version for version monitoring 

</p>
</details>
<br>

 <details><summary> Sahana </summary>
<p>

**A1**:

https://github.com/doda25-team6/operation/pull/1

Initial folder structure for operations

https://github.com/doda25-team6/app/pull/1

Added the Dockerfile in app for containerizing as well as created a release-image.yml for the F4 requirement.

https://github.com/doda25-team6/model-service/pull/1

Added the Dockerfile in model-service and created the release-image.yml for F4

</p>
</details>
<br>

 <details><summary> Levi </summary>
<p>

**A1**:
Created a SCRUM board for the project and linked all issues/PRs to it: 
https://github.com/orgs/doda25-team6/projects/1

</p>
</details>
<br>

 <details><summary> Anhar </summary>
<p>

**A1**:

Apart from researching GitHub Actions workflows, Docker, etc., no work was performed yet on F9 and F10.

</p>
</details>
<br>

<br> 
 <details><summary> Cem </summary>
<p>

**A1**:

Mostly research and in-class assignments to understand Docker, Actions and workflows. Not yet started on F7 and F8.

</p>
</details>
<br>

-----

### WEEK 2 (17/11 - 23/11)

<br> 
 <details><summary> Valantis </summary>
<p>

**A1**:

https://github.com/doda25-team6/app/pull/15

feat: upgrade to Java 25 with multi-stage Docker build fix

https://github.com/doda25-team6/model-service/pull/7

This fixes paths in Dockerfile and the ML model serving API that was failing with pickle deserialization errors when trying to load the trained preprocessor.

https://github.com/doda25-team6/operation/pull/5

This resolves the docker compose up --build failures by ensuring proper build context access for multi-stage containerization.

https://github.com/doda25-team6/operation/pull/8

Added F1 and F2 features in readme and project overview with tag a1 links for peer review.

https://github.com/doda25-team6/operation/pull/9

Add GitHub Package Registry support for F2 implementation

https://github.com/doda25-team6/app/pull/16

feat(F2): Implement GitHub Package Registry integration

</p>
</details>
<br>

 <details><summary> Sahana </summary>
<p>

**A1**:

https://github.com/doda25-team6/operation/pull/2

Docker compose yml file updated to run both containers from operation

https://github.com/doda25-team6/operation/pull/4

Updated the operation README file

https://github.com/doda25-team6/model-service/pull/6

updated the Docker file to copy the output folder

</p>
</details>
<br>

 <details><summary> Levi </summary>
<p>

**A1**:
Added a feature to automatically update versions after a new commit. Changed the xml and yml files and added a small shell script that can be run to test and update the project version.
https://github.com/doda25-team6/lib-version/pull/5

</p>
</details>
<br>

 <details><summary> Anhar </summary>
<p>

**A1**:

I spent most of this week working on F9. A (manually triggerable) workflow was created to train, version, and release model files. See https://github.com/doda25-team6/model-service/pull/9.

Some work has gone into an attempt to make F10 work. The Docker Compose file (operation repo), the Dockerfile (model-service repo), and serve_model.py file (model-service) were modified. See https://github.com/doda25-team6/model-service/pull/10 and https://github.com/doda25-team6/operation/pull/7.

</p>
</details>
<br>

<br> 
 <details><summary> Cem </summary>
<p>

**A1**:

- F8, Created the workflow for automated container releases for app repository. Workflow gets the version from pom.xml, compares that with the given tag and if they are equal, creates a container and releases it. Tested it out by giving differing versions and tags: https://github.com/doda25-team6/app/pull/18
- Implemented the use of lib version from github inside the app repository. Updated dockerfile of app to mount secrets and included repository in pom.xml: https://github.com/doda25-team6/app/pull/17
- Updated docker-compose so that env is used: https://github.com/doda25-team6/operation/pull/10
Next week, planning to fix the exposure of backend ports.
- Did some tiny changes on model-service dockerfile: https://github.com/doda25-team6/model-service/pull/9/commits

</p>
</details>
<br>

-----

### WEEK 3 (24/11 - 30/11)

<br> 
 <details><summary> Valantis </summary>
<p>

**A2**:
https://github.com/doda25-team6/operation/pull/25

fix for step 8 that makes Vagrant pass IPs to Ansible 

https://github.com/doda25-team6/operation/pull/27

Implemented 1.2 "Setting up the Kubernetes Controller" (steps 13 to 17)

</details>

<br> 
 <details><summary> Sahana </summary>
<p>

**A2**:

https://github.com/doda25-team6/operation/pull/12

Updated docker compose paths, and got docker compose up working

https://github.com/doda25-team6/operation/pull/26

Added steps 9 to 12 into general.yml for adding repository, installing k8 tools, configuring containerd and starting kubelet

</details>

<br>

 <details><summary> Anhar </summary>
<p>

**A2**:

- I implemented steps 5-8 of Assignment 2. See https://github.com/doda25-team6/operation/pull/24.
- I performed peer review for my team and another group.
<br>
</details>

<br> 
 <details><summary> Cem </summary>
<p>

**A2**:

- F2: Fixed the unexpected network configuration and configured as expected. Tested whether it is possible to access without port-forwards. [step-2-networking](https://github.com/doda25-team6/operation/pull/21)
- F3: Configured ansible, enabled provisioning and parameterized VMs cpu and memory. Included placeholders for ctrl and node yml files. [step-3-provision-ansible](https://github.com/doda25-team6/operation/pull/22/)
- F4: Registered ssh keys inside each VM to allow access via ssh. Looped over files to register them inside VMs. [step-4-ssh-keys](https://github.com/doda25-team6/operation/pull/23)
- Peer reviewed another group and my team.

</p>
</details>
<br>

### WEEK 4 (1/12 - 7/12)

<br> 
 <details><summary> Sahana </summary>
<p>

**A3**:

https://github.com/doda25-team6/operation/pull/35

Added the helm chart in operation

</details>

<br>

<br> 
 <details><summary> Valantis </summary>
<p>

**A3**:
https://github.com/doda25-team6/operation/pull/36

Initial implementation for setting up Prometheus.

</details>
