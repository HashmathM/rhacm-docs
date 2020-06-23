# Creating and managing subscriptions

You can create and manage subscriptions to identify, retrieve, and deploy new and updated resources to managed clusters. By using subscriptions, you can improve the continuous delivery capabilities of your application management. Samples for all resources, including subscriptions, are located in the [Application resource samples](app_resource_samples.md) documentation.

Learn more about subscriptions, then see the following tasks: 

  - [Creating a subscription](creating_subscription.md)
  - [Matching a subscription to an application](matching_subscription.md)
  - [Updating a subscription](updating_subscription.md)
  - [Configuring package overrides](package_overrides.md)
  - [Deleting a subscription](deleting_subscriptions.md)

Subscriptions (`subscription.apps.open-cluster-management.io`) are Kubernetes resources that serve as sets of definitions for identifying Kubernetes resources (in GitHub, Objectstores, or hub cluster deployables), and Helm charts within channels by using annotations, labels, and versions.

Subscription resources can point to a channel for identifying new and updated Helm charts or Kubernetes resources for deployment. The subscription operator then watches the channel for new and updated charts and deployables. 

When a new or updated Helm chart or Kubernetes resource is detected, the subscription operator downloads the Helm release version for the specified Helm chart version or the specified Kubernetes resource. The subscription operator can download these objects directly, or as deployables, from the storage location to target managed clusters without checking the hub cluster first.

Subscriptions can filter the deployables that are promoted to a channel to select specific deployables. For instance, the subscription can filter the deployables to select a specific deployable version. For this case, the subscription operator checks the `version` parameter to identify the deployable version to select.  
  
<!-- ## PUT IN NEW FILE FOR 2.0 Scheduling resource deployments for a subscription (tech preview decision, return for GA see issue 2313 -->

<!-- If you need to deploy new or changed Helm charts, deployables, or other Kubernetes resources during only specific times, you can define subscriptions for those resources to begin deployments during only those specific times. For instance, you can define time windows between 10:00 PM and 11:00 PM each Friday to serve as scheduled maintenance windows for applying patches or other application updates to your clusters. -->

<!-- Alternatively, you can restrict or block deployments from beginning during specific time windows, such as to avoid unexpected deployments during peak business hours. For instance, to avoid peak hours you can define a time window for a subscription to avoid beginning deployments between 8:00 AM and 8:00 PM. -->

<!-- By defining time windows for your subscriptions, you can coordinate updates for all of your applications and clusters. For instance, you can define subscriptions to deploy only new application resources between 6:01 PM and 11:59 PM and define other subscriptions to deploy only updated versions of existing resources between 12:00 AM to 7:59 AM. -->

<!-- When a time window is defined for a subscription, the time ranges when a subscription is active changes. As part of defining a time window, you can define the subscription to be active or blocked during that window. The deployment of new or changed resources begins only when the subscription is active. Regardless of whether a subscription is active or blocked, the subscription continues to monitor for any new or changed resource. The active and blocked setting affects only deployments.
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
* You can define multiple time windows for a subscription, such as to define a time window every Monday and Wednesday. -->