# Generator for Kubernetes resources using Kustomize and Skaffold

This generator creates the resources to deploy a Spring Boot application and a MySQL Database.

You will need to install the following command line tools

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [kustomize](https://kubernetes-sigs.github.io/kustomize/installation/)
* [skaffold](https://skaffold.dev/docs/install/)

If you want to use Helm to deploy services, install it's CLI

* [helm v3 cli](https://github.com/helm/helm/releases/tag/v3.3.1)

If you used the Starter Service, then the Generator has already been executed and you can immediately deploy the application's required services and then build and deploy the application.

## Notes before Usage

This generator relies on the [Jib Maven Plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

1. **Minikube usage** 

If you are using Minikube you should configure your terminal to use the same Docker environment:

```bash
eval $(minikube docker-env)
```

2. **Container repository setup**

If you are pushing images to a container repository, please revisit the skaffold.yaml file in the root of the generated starter and update the image prefix.
```yaml
profiles:
  - name: local
    build:
      artifacts:
        # ex.: 
        # artifactID is already pre-populated by the generator
        # GCR
        # - image: gcr.io/myproject/todos-api
        # DockerHub
        # - image: myproject/todos-api
        - image: <cloud prefix>/project-name>/artifactID
          jib:
            type: maven
            args:
              - -Plocal
```

# Building and deploying the application

**Step 1:** Build the project, create the container image, and deploy to Kubernetes:

```bash
skaffold run -p local
```

Use `kubectl get all` to verify that the resources created.

Accessing the application's endpoint varies based on the type of Kubernetes cluster you are using.  

1. If minikube is being used, look for the endpoint to access using the command `minikube service list`

2. On a remote cluster, look for the endpoint to access using `kubectl get svc`.


**Step 2:** As you make changes to the application, execute the `skaffold run -p local` command to rebuild the container and update the application running in the Kubernetes cluster.

## Deleting the application

To delete the deployment, delete the deployment using:
```bash
skaffold delete -p local
```

## Using Skaffold in Development mode
`skaffold dev` enables continuous local development on an application. 

While in dev mode, Skaffold will watch an application’s source files, and when it detects changes, will rebuild your images (or sync files to your running containers), push any new images, and redeploy the application to your cluster.

`skaffold dev` is considered Skaffold’s main mode of operation, as it allows you to leverage all of the features of Skaffold in a continuous way while iterating on your application.

To run this starter in dev mode, use:
```bash
skaffold dev -p local`
```

## Using Skaffold in Debug mode

`skaffold debug` acts like skaffold dev, but it configures containers in pods for debugging as required for each container’s runtime technology. 

The associated debugging ports are exposed and labelled so that they can be port-forwarded to the local machine. 

To run this starter in debug mode, use:
```bash
skaffold debug -p local`
```


# Generator Commands

The Tanzu Starter Service can run these generator commands on the server when creating a new project.  Alternatively, you can run the generator command using the Tanzu Starter Service CLI named `tss`.

**TODO: LINK TO DOWNLOAD CLIENT**

`k8s new` creates

* A `skaffold.yaml` file in the root directory
* A kustomize directory structure with an overlay named `local`.  The kustomize file is located in `kubernetes/overlays/local/kustomization.yaml`
* Adds the [Jib Maven Plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) to the Maven `pom.xml` under the profile `local`
* Adds an Spring Actuator dependency to the Maven `pom.xml` if it is not already present

`k8s update` creates
* A kustomize directory structure with overlays named `local-services` and `local-services-helm` for MySql if the project has a dependency on the `mysql-connector-java` library.

# Running the application and services

Once the generator commands have be executed you can run the application and services.