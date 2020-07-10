# Blue/Green HTTPD Deployment
A Blue-Green HTTPD deployment via a Jenkins pipeline & OpenShift

This example will deploy 2 HTTPD applications labeled blue-app and green-app (branches).

Each application gets its own URL (route/blue-app will serve content from the blue container and route/green-app will serve content from the green container), as well as a vanity URL that can be used to serve content from either the blue-app or the green-app.

## Dependencies
OpenShift >= 4.1
Jenkins >= 2

## Installation
#### Method 1 CLI
1. Clone this repo
2. Login to OCP API
```
oc login -u <user> api.openshift-is-awesome.com:6443
```
3. Create new project
```
oc new-project blue-green
```
4. Deploy Jenkins (this might take some time)
```
oc new-app --template="jenkins-ephemeral" -p DISABLE_ADMINISTRATIVE_MONITORS=true
```
5. Install pipeline template
```
oc create -f blue-green-example-pipeline.yml
```
6. Deploy pipeline into jenkins (make sure Jenkins is running and accessible)
```
oc new-app --template="httpd-blue-green-pipeline"
```
7. Start Pipeline Build
```
oc start-build httpd-blue-green-pipeline
```
The pipeline will deploy:
* Blue App Buildconfig
  * Builds httpd container image with static index.html (displays a blue square)
* Green App Buildconfig
  * Builds httpd container image with static index.html (displays a green square)
* Blue App DeploymentConfig
  * Runs 1 replica of the application
  * Creates a route named `blue-app.<subdomain>.tld.com`
  * Creates a service (blue-app)
* Green App DeploymentConfig
  * Runs 1 replica of the application
  * Creates a route named `green-app.<subdomain>.tld.com`
  * Creates a service (green-app)
* Vanity url
  * Creates a route named `app.<subdomain>.tld.com`
The pipeline also has additional steps for:
* Rebuilding the Blue App (has option to skip step)
* Rebuilding the Green App (has option to skip step)
* Switching the Vanity URL serving content from the blue-app to the green-app and vice-versa (has option to skip step)
#### Method 2 OpenShift Webconsole
* Todo
