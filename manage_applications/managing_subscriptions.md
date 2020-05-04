# Creating and managing subscriptions

You can create and manage subscriptions to identify, retrieve, and deploy new and updated resources to managed clusters. By using subscriptions, you can improve the continuous delivery capabilities of your application management. Samples for all resources, including subscriptions, are located in the [Application resource samples](app_resource_samples.md) documentation.

See the following tasks: 

  - Creating a subscription
  - Matching a subscription to an application
  - Updating a subscription
  - Deleting a subscription
  - Scheduling a subscription

Subscriptions (`subscription.apps.open-cluster-management.io`) are Kubernetes resources that serve as sets of definitions for identifying Kubernetes resources (in GitHub, Objectstores, or hub cluster deployables), and Helm charts within channels by using annotations, labels, and versions.

Subscription resources can point to a channel for identifying new and updated Helm charts or Kubernetes resources for deployment. The subscription operator then watches the channel for new and updated charts and deployables. 

When a new or updated Helm chart or Kubernetes resource is detected, the subscription operator downloads the Helm release version for the specified Helm chart version or the specified Kubernetes resource. The subscription operator can download these objects directly, or as deployables, from the storage location to target managed clusters without checking the hub cluster first.

Subscriptions can filter the deployables that are promoted to a channel to select specific deployables. For instance, the subscription can filter the deployables to select a specific deployable version. For this case, the subscription operator checks the `version` parameter to identify the deployable version to select.  
  
## Creating a subscription

1. Compose the definition YAML content for your subscription. For more information about YAML structure and samples, see [Application resource samples](app_resource_samples.md) documentation.

2. Create the subscription within Red Hat Advanced Cluster Management for Kubernetes. You can use the console, the Kubernetes CLI (`kubectl`) tool, or REST API:  

   * To use the console:

     1. Open the console.
     2. From the Navigation menu, click **Manage applications**. The **Overview** tab for all applications opens.
     3. Click the **Resources** tab.
     4. Scroll to the _Resource pipeline_ section. From the list of buttons to the right of the resource summary cards, click **Susbcription**. The _Create a Susbcription_ editor is displayed.
     5. Enter the YAML content to define your subscription or directly update the default YAML template to meet your requirements.
     6. When you are finished, click **Save** to create the subscription. Your new subscription displays within the __Resource pipeline__ for the corresponding applications and channels.

     Alternatively, you can select to create a subscription when you are working with a specific application.

     1. From the __Overview__ tab for all applications, click the application from the __All applications__ list. The __Overview__ tab for that application opens.
     2. Click the **Resources** tab for that application.
     3. Scroll to the _Resource pipeline_ section. From the list of buttons to the right of the resource summary cards, click **Susbcription**. The _Create a Susbcription_ editor is displayed.
     4. Enter the YAML content to define your subscription or directly update the default YAML template to meet your requirements.
     5. When you are finished, click **Save** to create the subscription. Your new subscription displays within the __Resource pipeline__ for the corresponding applications and channels.

     **Note:** The subscription might not initially display in your resource pipeline for any application. For the subscription to be associated with an application, you need to include the appropriate values in your subscription definition to match the required values that are defined by the application definition. For more information, see [Matching a subscription to an application](#matching-a-subscription-to-an-application).

   * To use the Kubernetes CLI tool:

     1. Run the following command to apply your file to an apiserver. Replace `filename` with the name of your file:

        ```
        kubectl apply -f filename.yaml
        ```

     2. Verify that your subscription resource is created, by running the following command:

        ```
        kubectl get Subscription
        ```

        Ensure that your new subscription is listed in the resulting output.

   * To use REST API, you need to use the [subscription POST API](../apis/mcm/subscriptions.json).

After your subscription is created, your subscription can have one of the following statuses:

* `Subscribed` (managed clusters only)
  The subscription has successfully subscribed to the specified resources and the resources are deployed.
* `Propagated` (hub cluster only)
  The subsequent subscriptions based on your specified `spec.placement` setting are generated for deployment to the appropriate managed clusters, if any. However, the subscriptions might not actually be deployed.
* `Failed`
  The subscription failed to subscribe to the specified resources.
* No status
  The subscription operator is either not online, or the `spec.placement` setting for the subscription is missing.

If you created the subscription for a multi-cluster environment, the subscription is created on the Hub cluster. Depending on your `spec.placement` settings, the subsequent placement of your subscription on other clusters is different. If you include more than one placement setting within the `spec.placement` section, the subscription operator uses the following priority to select the placement setting to use:
  
* `spec.placement.placementRef`

  The subscription is placed on the clusters that are identified by the specified `PlacementRule` resource.

* `spec.placement.clusters`

  The subscription is placed on the clusters that are specified as the value for this field.

* `spec.placement.clusterSelector`

  The subscription is placed on the clusters that match the specified label selector that is defined as the value for this field.

If you created your subscription on a stand-alone cluster or a cluster that you want to manage directly, the subscription is created on only that cluster. Depending on the value that you set for the `spec.placement.local` field within the subscription definition, the subsequent behavior of your subscription is different.

* `true`

  The subscription synchronizes with the specified channel that is local to that cluster.

* `false`

  The subscription does not subscribe to any resources from the specified channel.

## Matching a subscription to an application

To associate a subscription with an application, both the subscription and application must be in the same namespace so that the subscription can retrieve Helm charts, deployables, or other resources from a channel.

Within the application resource definition, the definition must include `spec.componentKinds` settings to indicate that the application uses a subscription. The definition must also include `spec.selector` settings to define the labels (`matchLabels`) or expressions (`matchExpressions`) to use to match the application with the subscription.

Within the subscription resource definition, the definition must include the required values to match the labels or expressions that are defined by the application.

When the subscription is associated with an application, the subscription uses the `spec.placement` settings for the subscription or deployable to deploy any subscribed charts, deployables or other Kubernetes resources for the application.

For more information about the resource definition for an application, see [Creating and managing applications](managing_apps.md).

## Updating a subscription

1. Compose the definition YAML content for your subscription.

2. Create the subscription within Red Hat Advanced Cluster Management for Kubernetes. You can use the console, the Kubernetes CLI (`kubectl`) tool, or REST API:  

   * To use the console:
       1. Open the console.
       2. From the Navigation menu, click **Manage applications**. The **Overview** tab for all applications opens.
       3. Click the **Resources** tab.
       4. Scroll down the page to **Resource pipeline** section. Expand the row for the application that uses the subscription that you want to edit.
       5. For the subscription that you want to update, click the **Edit** icon for the YAML. The __Edit subscription__ window opens.
       6. Edit the YAML.
       7. When you are finished, click **Save** to update the subscription.

     Alternatively, you can select to update the subscription when you are working with a specific application.

       1. From the __Overview__ tab for all applications, click the application from the __All applications__ list. The __Overview__ tab for that application opens.
       2. Click the **Resources** tab for that application.
       3. Scroll down the page to **Resource pipeline** section.
       4. For the subscription that you want to update, click the **Edit** icon for the YAML. The __Edit subscription__ window opens.
       5. Edit the YAML.
       6. When you are finished, click **Save** to update the subscription.

     You can also use the console search to find and edit a subscription:

       1. Click the **Search** icon in the Header.  
       2. Within the search box, filter by `kind:subscription` to view all subscriptions.
       3. Within the list of all subscriptions, click the subscription that you want to update. The YAML for the subscription is displayed.
       4. Click **Edit** to enable editing the YAML content.
       5. When you are finished your edits, click **Save**. Your changes are saved and applied automatically.

   * To use the Kubernetes CLI tool, the steps are the same as for creating a subscription.

   * To use REST API, use the [subscription PATCH API](../apis/mcm/subscriptions.json).

## Scheduling resource deployments for a subscription

If you need to deploy new or changed Helm charts, deployables, or other Kubernetes resources during only specific times, you can define subscriptions for those resources to begin deployments during only those specific times. For instance, you can define time windows between 10:00 PM and 11:00 PM each Friday to serve as scheduled maintenance windows for applying patches or other application updates to your clusters.

Alternatively, you can restrict or block deployments from beginning during specific time windows, such as to avoid unexpected deployments during peak business hours. For instance, to avoid peak hours you can define a time window for a subscription to avoid beginning deployments between 8:00 AM and 8:00 PM.

By defining time windows for your subscriptions, you can coordinate updates for all of your applications and clusters. For instance, you can define subscriptions to deploy only new application resources between 6:01 PM and 11:59 PM and define other subscriptions to deploy only updated versions of existing resources between 12:00 AM to 7:59 AM.

When a time window is defined for a subscription, the time ranges when a subscription is active changes. As part of defining a time window, you can define the subscription to be active or blocked during that window. The deployment of new or changed resources begins only when the subscription is active. Regardless of whether a subscription is active or blocked, the subscription continues to monitor for any new or changed resource. The active and blocked setting affects only deployments.

When a new or changed resource is detected, the time window definition determines the next action for the subscription.

* For subscriptions to `HelmRepo`, `ObjectBucket`, and `GitHub` type channels:
  * If the resource is detected during the time range when the subscription is active, the resource deployment begins.
  * If the resource is detected outside the time range when the subscription is blocked from running deployments, the request to deploy the resource is cached. When the next time range that the subscription is active occurs, the cached requests are applied and any related deployments begin.
* For subscriptions to `Namespace` type channels:
  * When a subscription becomes active, the subscription synchronizes with the channel and begins the deployment for the latest version of any resources that need to be deployed.  
  * When the subscription is blocked, the subscription is not synchronized with the channel for deploying resources.

If a deployment begins during a defined time window and is running when the defined end of the time window elapses, the deployment continues to run to completion.

To define a time window for a subscription, you need to add the required fields and values to the subscription resource definition YAML.

* As part of defining a time window, you can define the days and hours for the time window.
* You can also define the time window type, which determines whether the time window when deployments can begin occurs during, or outside, the defined timeframe.
  * If the time window type is `active`, deployments can begin only during the defined timeframe. You can use this setting when you want deployments to occur within only specific maintenance windows.
  * If the time window type is `block`, deployments cannot begin during the defined timeframe, but can begin at any other time. You can use this setting when you have critical updates that are required, but still need to avoid deployments during specific time ranges. For instance, you can use this type to define a time window to allow security-related updates to be applied at any time except between 10:00 AM and 2:00 PM.
* You can define multiple time windows for a subscription, such as to define a time window every Monday and Wednesday.  

## Deleting subscriptions

To delete a subscription, you can use the console, the Kubernetes command line interface (`kubectl`) tool, or REST API.  

* To use the console, use the console search to find and delete a subscription:

   1. Open the console.
   2. Click the **Search** icon in the Header.  
   3. Within the search box, filter by `kind:subscription` to view all subscriptions.
   4. Within the list of all subscriptions, expand the _Options_ menu for the subscription that you want to delete. Click **Delete subscription**.
   5. When the list of all subscriptions is refreshed, the subscription is no longer displayed.

* To use the Kubernetes CLI tool:

  1. Run the following command to delete the subscription from a target namespace. Replace `name` and `namespace` with the name of your subscription and your target namespace:

     ```
     kubectl delete Subscription <name> -n <namespace>
     ```

  2. Verify that your subscription is deleted by running the following command:

     ```
     kubectl get Subscription <name>
     ```

* To use REST API, use the [subscription DELETE API](../apis/mcm/subscriptions.json).

### Package overrides

Package overrides for a subscription override values for the Helm chart or Kubernetes resource that is subscribed to by the subscription.

To configure a package override, specify the field within the Kubernetes resource spec to override as the value for the `path` field. Specify the replacement value as the value for the `value` field.

For example, if you need to override the values field within the spec for a Helm release (`HelmRelease.app.ibm.com`) for a subscribed Helm chart, you need to set the value for the `path` field in your subscription definition to `spec`. 

```
packageOverrides:
- packageName: nginx-ingress
  packageOverrides:
  - path: spec
    value: my-override-values
```

The contents for the `value` field are used to override the values within the `spec` field of the `HelmRelease` spec.

* For a Helm release, override values for the `spec` field are merged into the Helm release `values.yaml` file to override the existing values. This file is used to retrieve the configurable variables for the Helm release.

* If you need to override the release name for a Helm release, include the `packageOverride` section within your definition. Define the `packageAlias` for the Helm release by including the following fields:
  * `packageName` to identify the Helm chart.
  * `packageAlias` to indicate that you are overriding the release name.

   By default, if no Helm release name is specified, the Helm chart name is used to identify the release. In some cases, such as when there are multiple releases subscribed to the same chart, conflicts can occur. The release name must be unique among the subscriptions within a namespace. If the release name for a subscription that you are creating is not unique, an error occurs. You must set a different release name for your subscription by defining a `packageOverride`. If you want to change the name within an existing subscription, you must first delete that subscription and then recreate the subscription with the preferred release name.

  ```
  packageOverrides:
  - packageName: nginx-ingress
    packageAlias: my-helm-release-name
  ```