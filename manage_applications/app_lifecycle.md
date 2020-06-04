# Application management lifecycle

The application model is based on subscribing to one or more Kubernetes resource repositories (_channel_ resource) that contains resources that are deployed on managed clusters. 

The _subscription_ component uses a _placement rule_ resource to define the managed clusters where the Kubernetes resource will be deployed. 

The last piece in the application model is the _application_ resource that references one or more repositories subscriptions. The purpose for the application is to group deployed Kubernetes resources and provide data aggregation that is easy to understand and manage. 

See [Deploying by using application resources](deploying.md) to learn to set up and use channels, subscriptions, and placement rules for deployments. 

Learn more about the Application lifecycle from the following topics:

  * [Application model and definitions](app_model.md)
  * [Applications console](app_console.md)
