
# Subscription samples

As with channels, subscriptions (`subscription.apps.open-cluster-management.io`) provide you with improved continuous integration and continuous delivery capabilities for application management.

Subscriptions are sets of definitions that identify Helm charts, deployables, and other Kubernetes resources within channels by using annotations, labels, and versions. Subscriptions can point to a channel or storage location for identifying new or updated deployables. The subscription operator can then download the subscribed Helm chart, deployable, or secret directly from the storage location to target managed clusters without checking the Hub cluster first. With a subscription, the subscription operator can monitor the channel for new or updated resources instead of the Hub cluster.

The definition structure for a subscription can resemble the following YAML content:

```yaml
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: nginx
  namespace: ns-sub-1
  labels:
    app: nginx-app-details
spec:
  channel: ns-ch/predev-ch
  name: nginx-ingress
  packageFilter:
    version: "1.36.x"
  placement: # See the following section for information
    placementRef:
      kind: PlacementRule
      name: towhichcluster
  overrides: # See Deployable documentation
  - clusterName: "/"
    clusterOverrides:
    - path: "metadata.namespace"
      value: default
```

For more information about creating and managing subscriptions, see [Managing subscriptions](managing_subscriptions.md).

## Example subscription YAML 

The following YAML content defines example subscriptions: <!--note Ian/Brandi to create a full topic with just examples and pull from the main doc -->

  * [Channel subscription example](#channel-subscription-example)
  * [Scheduled channel subscription example](#scheduled-channel-subscription-example)
  * [Channel subscription example with overrides](#channel-subscription-example-with-overrides)
  * [Helm repository subscription example](#helm-repository-subscription-example)
  * [GitHub repository subscription example](#github-repository-subscription-example)

### Subscription example

```YAML
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: nginx
  namespace: ns-sub-1
  labels:
    app: nginx-app-details
spec:
  channel: ns-ch/predev-ch
  name: nginx-ingress
```

### Subscription time window example

The following example subscription includes multiple configured time windows. A time window occurs between 10:20 AM and 10:30 AM occurs every Monday, Wednesday, and Friday. A time window also occurs between 12:40 PM and 1:40 PM every Monday, Wednesday, and Friday. The subscription is active only during these six weekly time windows for deployments to begin.  

```YAML
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: nginx
  namespace: ns-sub-1
  labels:
    app: nginx-app-details
spec:
  channel: ns-ch/predev-ch
  name: nginx-ingress
  packageFilter:
    version: "1.36.x"
  placement:
    placementRef:
      kind: PlacementRule
      name: towhichcluster
  timewindow:
    windowtype: "active" #Enter active or block depending on the purpose of the type.
    location: "America/Los_Angeles"
    daysofweek: ["Monday", "Wednesday", "Friday"]
    hours:
      - start: "10:20AM"
        end: "10:30AM"
      - start: "12:40PM"
        end: "1:40PM"
```

### Subscription with overrides example

The following example includes package overrides to define a different release name of the Helm release for Helm chart. A package override setting is used to set the name `my-nginx-ingress-releaseName` as the different release name for the  `nginx-ingress` Helm release.

```yaml
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: simple
  namespace: default
spec:
  channel: ns-ch/predev-ch
  name: nginx-ingress
  packageOverrides:
  - packageName: nginx-ingress
    packageAlias: my-nginx-ingress-releaseName
    packageOverrides:
    - path: spec
      value: 
        defaultBackend:
          replicaCount: 3
  placement:
    local: false
```

### Helm repository subscription example

The following subscription automatically pulls the latest `nginx` Helm release for the version `1.36.x`. The Helm release deployable is placed on the `my-development-cluster-1` cluster when a new version is available in the source Helm repository.

The `spec.packageOverrides` section shows optional parameters for overriding values for the Helm release. The override values are merged into the Helm release `values.yaml` file, which is used to retrieve the configurable variables for the Helm release.

```YAML
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: nginx
  namespace: ns-sub-1
  labels:
    app: nginx-app-details
spec:
  channel: ns-ch/predev-ch
  name: nginx-ingress
  packageFilter:
    version: "1.36.x"
  placement:
    clusters:
    - name: my-development-cluster-1
  packageOverrides:
  - packageName: my-server-integration-prod
    packageOverrides:
    - path: spec
      value:
        persistence:
          enabled: false
          useDynamicProvisioning: false
        license: accept
        tls:
          hostname: my-mcm-cluster.icp
        sso:
          registrationImage:
            pullSecret: hub-repo-docker-secret
```

### GitHub repository subscription example

#### Subscribing specific branch and directory of GitHub repository

   ```yaml
    apiVersion: apps.open-cluster-management.io/v1
    kind: Subscription
    metadata:
      name: sample-subscription
      namespace: default
      annotations:
        apps.open-cluster-management.io/github-path: sample_app_1/dir1
        apps.open-cluster-management.io/github-branch: branch1
    spec:
      channel: default/sample-channel
      placement:
        placementRef:
          kind: PlacementRule
          name: dev-clusters
   ```

  In this example subscription, the annotation `apps.open-cluster-management.io/github-path` indicates that the subscription subscribes to all Helm charts and Kubernetes resources within the `sample_app_1/dir1` directory of the GitHub repository that is specified in the channel. The subscription subscribes to `master` branch by default. In this example subscription, the annotation `apps.open-cluster-management.io/github-branch: branch1` is specified to subscribe to `branch1` branch of the repository.

#### Adding a `.kubernetesignore` file

You can include a `.kubernetesignore` file within your GitHub repository root directory, or within the `apps.open-cluster-management.io/github-path` directory that is specified in subscription's annotations.

You can use this `.kubernetesignore` file to specify patterns of files or subdirectories, or both, to ignore when the subscription deploys Kubernetes resources or Helm charts from the repository.

You can also use the `.kubernetesignore` file for fine-grain filtering to selectively apply Kubernetes resources. The pattern format of the `.kubernetesignore` file is the same as a `.gitignore` file.

If the `apps.open-cluster-management.io/github-path` annotation is not defined, the subscription looks for a `.kubernetesignore` file in the repository root directory. If the `apps.open-cluster-management.io/github-path` field is defined, the subscription looks for the `.kubernetesignore` file in the `apps.open-cluster-management.io/github-path` directory. Subscriptions do not search in any other directory for a `.kubernetesignore` file.

#### Applying Kustomize

If there is `kustomization.yaml` or `kustomization.yml` file in a subscribed GitHub folder, kustomize is applied.

You can use `spec.packageOverrides` to override `kustomization` at the subscription deployment time. 

```yaml
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: example-subscription
  namespace: default
spec:
  channel: some/channel
  packageOverrides:
  - packageName: kustomization
    packageOverrides:
    - value: | 
patchesStrategicMerge:
- patch.yaml
```

In order to override `kustomization.yaml` file, `packageName: kustomization` is required in `packageOverrides`. The override either adds new entries or updates existing entries. It does not remove existing entries.

#### Enabling GitHub WebHook

By default, a GitHub channel subscription clones the GitHub repository specified in the channel every minute and applies changes when the commit ID has changed. Alternatively, you can configure your subscription to apply changes only when the GitHub repository sends repo PUSH and PULL webhook event notifications.

In order to configure webhook in a GitHub repository, you need a target webhook payload URL and optionally a secret.

##### Payload URL

Create a route (ingress) in the hub cluster to expose the subscription operator's webhook event listener service.

  ```shell
  oc create route passthrough --service=multicluster-operators-subscription -n open-cluster-management
  ```

Then, use `oc get route multicluster-operators-subscription -n open-cluster-management` command to find the externally-reachable hostname. The webhook payload URL is `https://<externally-reachable hostname>/webhook`.

##### WebHook secret

WebHook secret is optional. Create a Kubernetes secret in the channel namespace. The secret must contain `data.secret`. See the following example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-github-webhook-secret
data:
  secret: BASE64_ENCODED_SECRET
```

The value of `data.secret` is the base-64 encoded WebHook secret you are going to use.

**Best practice:** Use a unique secret for each GitHub repository.

##### Configuring WebHook in GitHub repository

Use the payload URL and webhook secret to configure WebHook in your GitHub repository.

##### Enable WebHook event notification in channel

Annotate the subscription channel. See the following example:

```shell
oc annotate channel.apps.open-cluster-management.io <channel name> apps.open-cluster-management.io/webhook-enabled="true"
```

If you used a secret to configure WebHook, annotate the channel with this as well where `<the_secret_name>` is the kubernetes secret name containing webhook secret.

```shell
oc annotate channel.apps.open-cluster-management.io <channel name> apps.open-cluster-management.io/webhook-secret="<the_secret_name>"
```

##### Subscriptions of webhook-enabled channel

No webhook specific configuration is needed in subscriptions.


