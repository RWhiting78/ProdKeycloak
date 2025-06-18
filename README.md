# Production Deployment of Keycloak

A collection of Helm values, YAML manifests and more relating the production deployment of Keycloak. 

# Installation

### Requirements
The deployment of Keycloak requires a few things:
- A ready Kubernetes cluster deployed into the Keycloak project. (More info can be found [here.](https://stfc.atlassian.net/wiki/spaces/CLOUDKB/pages/211878034/Cluster+API+Setup))
- NGINX Ingress Controller deployed on the cluster. (More info can be found [here.](https://stfc.atlassian.net/wiki/spaces/CLOUDKB/pages/309854262/CAPI+Ingress+and+TLS))
- Cluster controller must be running Ubuntu ver 24.04 (noble)

### Configuration
First, clone this repository onto the controller machine, there are some credentials that need to be set manually before proceeding.
 - Database username and password in `/Values/kc-values.yaml` (Starting from line 39)
 - Authentication username and password in `/Values/prom-values.yaml` (Starting from line 29)
 - Certificate email in `/Manifests/clusterissuer.yaml` (Line 9)

### Bootstrap
Once the required configuration values are set, navigate to the Bootstrap playbook in the `Playbooks` directory and run it using the following command:
`ansible-playbook bootstrap.yaml` 
It should take a small while to run, once complete allow for extra time as Keycloak tends to take a small while to start up. Keycloak can be found in namespace `keycloak` and Prometheus in namespace `prometheus`. You can check the progress of their installation using `kubectl get all -n [namespace]`.

Once the Keycloak pod reports `READY` you should be able to access the admin console on `https://cloud.keycloak.stfc.ac.uk/`.

The Prometheus/Alertmanager servers will be accessible off the back of a loadbalancer assigned a random floating IP address, to check this you can see the `EXTERNAL-IP` fields next to either service in the service section when running `kubectl get all -n prometheus`. 
