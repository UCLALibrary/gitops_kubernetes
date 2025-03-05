# gitops_kubernetes

This gitops repo is used to house the following configuration

## Usage Guide 

### Directory Structure
* ArgoCD configuration is stored under `infrastructure/deployment`
* ArgoCD deployed applications are managed via app-of-apps style. Manifests are stored through umbrella charts in directory `app-of-apps`
* `apps` directory is optional, can be used to store values file or helm charts as a subdirectory to be referenced by ArgoCD

NOTE: In most cases, developers will only need to access `app-of-apps`

The following workflows and operations are currently supported:
* Adding a new application to deploy via ArgoCD

The following workflows/operations are desired, but have not been implemented:
* Removing an existing application via ArgoCD through a git commit


### To add a new ArgoCD application to be deployed
1. Navigate to directory `app-of-apps`
1. Navigate to directory that best associates the subdirectory of what you're trying to deploy to. This is most likely subteam based. I.e. `app-of-apps/services-team`
  * `cd app-of-apps/services-team`
1. There currently isn't a template to copy from, please copy an existing file under `templates` and rename it to your application's name
  * `cp prl-harvester.yaml myapp.yaml`
1. Open the file, remove lines until you have one entry to work with
1. Edit the following lines
  * `.metadata.name` - Application name:  `[environment]-[applicationname]`
  * `.spec.destination.name` - Cluster you want to deploy to. See relative umbrella chart values file
  * `.spec.destination.namespace` - Namespace to deploy this application: `[environment]-[applicationname]`
  * `.spec.destination.sources[0].repoURL` - Helm repo url where your chart is hosted: `https://chartmuseum.library.ucla.edu`
  * `.spec.destination.sources[0].targetRevision` - Version of helm chart to use: `1.1.1`
  * `.spec.destination.sources[0].chart` - Name of chart to reference in repoURL: `uclalib-helm-generic`
  * `.spec.destination.sources[0].helm.valueFiles[0]` - Location of the values file to pair with this helm chart: `$values/apps/services-team-value-files/test-prl-harvester.yaml`
  * `.spec.destination.sources[1].repoURL` - GitHub Repo URL where desired values file is located
  * `.spec.destination.sources[1].targetRevision` - Reference branch in which the desired values file is stored
1. Once you've finalize and pushed your changes to the main branch, login to https://argocd.library.ucla.edu to verify the application has been deployed. It may take a few minutes for it to show up
