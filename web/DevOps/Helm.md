https://www.bmc.com/blogs/kubernetes-helm-charts/

## What is Helm?

In simple terms, Helm is a package manager for Kubernetes. Helm is the K8s equivalent of yum or apt. Helm deploys charts, which you can think of as a packaged application. It is a collection of all your versioned, pre-configured application resources which can be deployed as one unit. You can then deploy another version of the chart with a different set of configuration.

Helm helps in three key ways:

-   Improves productivity
-   Reduces the complexity of deployments of microservices
-   Enables the adaptation of cloud native applications

## Why use Helm?

Writing and maintaining Kubernetes **YAML manifests** for all the required Kubernetes objects can be a time consuming and tedious task. For the simplest of deployments, you would need at least 3 YAML manifests with duplicated and hardcoded values. Helm simplifies this process and creates a single package that can be advertised to your cluster.

[Helm](https://www.bmc.com/blogs/kubernetes-helm/) is a client/server application and, until recently, has relied on Tiller (the helm server) to be deployed in your cluster. This gets installed when installing/initializing helm on your client machine. Tiller simply receives requests from the client and installs the package into your cluster. Helm can be easily compared to RPM of DEB packages in Linux, providing a convenient way for developers to package and ship an application to their end users to install.

Once you have Helm installed and configured ([details below](https://www.bmc.com/blogs/kubernetes-helm-charts/#_Installing_Helm_and)), you are able to install production-ready applications from software vendors, such as [MongoDB](https://www.bmc.com/blogs/mongodb-overview-getting-started-with-mongodb/), MySQL and others, into your Kubernetes cluster with one very simple **helm install** command. Additionally, removing installed applications in your cluster is as easy as installing them.

## What are Helm charts?

Helm Charts are simply Kubernetes YAML manifests combined into a single package that can be advertised to your Kubernetes clusters. Once packaged, installing a Helm Chart into your cluster is as easy as running a single **helm install**, which really simplifies the deployment of containerized applications.

## Describing Helm

Helm has two parts to it:

-   The client (CLI), which lives on your local workstation.
-   The server (Tiller), which lives on the Kubernetes cluster to execute what’s needed.

The idea is that you use the CLI to push the resources you need and tiller will make sure that state is in fact the case by creating/updating/deleting resources from the chart. To fully grasp helm, there are 3 concepts we need to get familiar with:

-   **Chart**: A package of pre-configured Kubernetes resources.
-   **Release**: A specific instance of a chart which has been deployed to the cluster using Helm.
-   **Repository**: A group of published charts which can be made available to others.

## Benefits of Helm

Developers like Helm charts for many reasons:

### Boosts productivity

Software engineers are good at writing software, and their time is best spent doing just that. Using Helm allows software to deploy their test environments at the click of a button.

An example of this might be that, in order to test a new feature, an engineer needs a SQL database. Instead of going through the process of installing the software locally, creating the databases and tables required, the engineer can simply run a single **Helm Install** command to create and prepare the database ready for testing.

### Reduces duplication & complexity

Once the chart is built once, it can be used over and over again and by anyone. The fact that you can use the same chart for any environment reduces complexity of creating something for dev, test and prod. You can simply tune you chart and make sure it is ready to apply to any environment. And you get the benefit of using a production ready chart in dev.

### Smooths the K8S learning curve

It’s no secret that the learning curve for Kubernetes and containers is long for your average developer. Helm simplifies that learning curve: developers do not require a full, detailed understanding of the function of each Kubernetes object in order to start developing and deploying container applications.

Helm easily integrates into [CI/CD pipelines](https://www.bmc.com/blogs/deployment-pipeline/) and allows software engineers to focus on writing code—not deploying applications.

### Simplifies deployments

Helm Charts make it easy to set overridable defaults in the **values.yaml** file, allowing software vendors or administrators of charts to define a base setting. Developers and users of charts can override these settings when installing their chart to suit their needs. If the default installation is required, then no override is required.

[Deploying applications to Kubernetes](https://www.bmc.com/blogs/kubernetes-deployment/) is not a straightforward process, with different objects being tightly coupled. This required specific knowledge of these objects and what their functions are in order to be able to successfully deploy. Helm takes the complexity out of that doing much of the hard work for you.