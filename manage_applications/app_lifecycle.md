# Application management lifecycle

Red Hat Advanced Cluster Management for Kubernetes provides enhanced application management capabilities through a Kubernetes resource based application model. The application model centers around managed clusters subscribing to repositories. The application model groups all subscriptions related to an application and displays a topology that is easy to understand and use. The topology view can scale with the number of managed clusters, providing insight and management, without context switching between clusters.

The Application model consists of the following Kubernetes custom resources:

   - Channel: Resource representing a source repository such as a Github repository, Helm chart repository, Objectstore with YAML, or namespace containing Kubernetes resource templates(deployables)
   - Subscription: Subscribes a repository and delivers kubernetes resources.
   - Placement Rule: Referenced by subscriptions, and supplies the target managed clusters that must subscribe to a channel.
   - Application: Used to select all of the subscriptions that are part of your application(a visual aid)

Learn more about the Application lifecycle from the following topics.

  * [Application model and definitions](app_model.md)
  * [Application resource interactions and lifecycle](#application-resource-interactions-and-lifecycle)
  * [Deploying application resources](#deploying-application-resources)
  * [Managing applications with the console](#managing-applications-with-the-console)

## Application resource interactions and lifecycle

With the new application model and deployment options, the high-level flow for creating and managing applications can resemble the following steps:

1. A developer or DevOps personnel for your organization creates the YAML definitions for any required channels. With the channels created, application developers can include the required values to promote a deployable to a channel when they are creating the deployable.

2. With the channels created, the application developers for your organization can create the YAML definitions for any required applications or related deployables on the hub cluster. As part of creating a deployable, the developer can reference any required placement rule for deploying the deployable. The developer can also include the required values to promote the deployable to a channel.
   * For `objectBucket` type channels, developers can promote resources directly to the channel without wrapping the resource as a deployable. The channel controllers can synchronize the resource as a deployable automatically.
   * For `HelmRepo` type channels, developers can upload Helm charts to the Helm repository without creating deployables to represent the chart. The channel controllers can synchronize the chart as a deployable within the channel.
   * For `GitHub` type channels, developers can upload Kubernetes resources YAML files and unpackaged Helm charts to a GitHub repository without wrapping the resources as deployables. The channel controllers synchronize resources as deployables automatically.
   * For `Namespace` type channels, developers can upload Kubernetes resource templates. Subscription for this type of channel retrieves and deploys the template. The namespaces that the channel monitors for new or updated deployables must be on the hub cluster.

3. DevOps personnel or other users for your organization can create any required subscriptions that need to be used for deploying the application and the related deployables. The labels for the subscription definition are used to associate the subscription to an application by matching the `selector` settings in the application spec.

4. DevOps personnel or other users for your organization can create the set of placement rules for your clusters within Red Hat Advanced Cluster Management for Kubernetes. These users or an application developer can then update the deployables or subscriptions to reference the placement rules.

   A created placement rule can be updated when needed in Git and then pushed through a continuous delivery process to update the rule on the hub cluster. After the hub cluster is updated, the change is made to the rule on the managed clusters that are connected to the hub cluster. Any subsequent change for deployables that is based on the rule change is then started.

5. Users for your organization then complete any required updates for the application resource definitions, such as to configure overrides for deployables, or to adjust or reference placement rules.

Once the deployables and other resources are created, the subscription operator can automatically identify new or updated deployables and begin any associated deployment of the source resources. When further changes to the source resources or deployables occur, the changes are automatically detected and the updated resources are retrieved from the channel source and deployed. The process to automatically identify and deploy changes, can resemble the following high-level steps:

   1. When all resource kinds exist, each subscription monitors, or watches, to the channels that are defined for each subscription for new and updated deployables.
   2. When a new or updated deployable exists, the subscription operator retrieves the source Kubernetes deployable object or Helm release that is wrapped or represented by the deployable.
   3. The subscription operator then uses the associated placement rule to place the Kubernetes deployable object or Helm release on the managed cluster. The subscription operator then continues to watch for further changes.

To create or edit the resource definitions developers and other users can use the console, the Kubernetes CLI tool, or REST API.

## Deploying application resources

Red Hat Advanced Cluster Management for Kubernetes supports multiple options for the deployment of deployable objects and Helm charts. The deployment option that you use can depend upon whether you need to deploy to a single cluster or multiple clusters and the frequency that you need to deploy updates. For more information, see [Deploying an application resource](deployment_app.md).

## Managing applications with the console  {#manage-apps-console}

The console includes an applications dashboard for managing the application lifecycle. You can use the console dashboard to create and manage application, subscriptions, channels, and placement rules. You can also use the console to view the status of your applications, channels, subscriptions, and related deployments.

The dashboard includes enhanced capabilities, which your developers and operations personnel can use to create, deploy, update, manage, visualize, and monitor applications across your clusters. With this application dashboard, you can complete the following tasks:

* View your deployment pipeline to view all deployed applications across your clusters, including any associated channels and subscriptions.
* View events in the context of an application, including deployments, updates, and monitoring data.
* Access an improved topology view that encompasses the new application resource definitions, including channels, subscriptions, and placement rules.
* View a the logs tab for applications to view logs for each application resource.
* When Grafana is installed, you can open the Grafana dashboard for your applications from the applications dashboard to monitor your applications.
* Add and edit channels, subscriptions, placement rules, and applications from different views and pages.

  **Note:** The application dashboard shows only the application resources that use the new custom resource definitions. Application resources that are based on the definitions that were used for previous versions of Red Hat Advanced Cluster Management for Kubernetes are not displayed. You can migrate your older style application resources to use the new definitions and have your resources display in the console.

For more information, see [Managing applications with the console](managing_apps_console.md).
