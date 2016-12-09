# Spring Boot and Camel using ConfigMap and Secret resources 

This quickstart demonstrates how to configure a Spring-Boot application using Kubernetes/Openshift ConfigMap and Secret objects.

A route generates sample messages that are delivered to a list of recipient endpoints 
configured through a property named `quickstart.recipients` in the `src/main/resources/application.properties` file.
The property can then be overridden using a Kubernetes ConfigMap object.
As soon as a ConfigMap named `camel-config` (containing a property named `application.properties`) is created or changed, 
 an application-context refresh is triggered and the logs change to reflect the new configuration. 
 
A sample `ConfigMap` (`sample-configmap.yml`) is contained in this repository (it change the configuration to use all available endpoints in the `recipientList`). 
It can be created by running (from the root of this repository):
  
    kubectl create -f sample-configmap.yml
    # for Openshift:
    # oc create -f sample-configmap.yml

The quickstart will run on Openshift using a `ServiceAccount` named `fis-camel-config`, with the `view` role granted.
This way, the application is allowed to listen for changes in the Openshift project and read the `ConfigMap`.

Secrets can also be used to configure the application (a sample username/password combination in this quickstart).
Unlike the `ConfigMap` objects, `Secret`s require higher permissions in order to be read using the Openshift API.
The approach used in this quickstart is to mount the secret as a volume in the POD and configure its location in 
the spring-cloud config file (`src/main/resources/bootstrap.yml`).

A sample `Secret` (`sample-secret.yml`) is contained in this repository (it change the configuration to use all available endpoints in the `recipientList`). 
It can be created by running (from the root of this repository):

    kubectl create -f sample-secret.yml
    # for Openshift:
    # oc create -f sample-secret.yml

**Note: a secret named `camel-config` must be present in the namespace before the application is deployed**
(otherwise the container remains in a pending status, waiting for the secret).

### Building

The example can be built with

    mvn clean install


### Running the example locally

The example can be run locally using the following Maven goal:

    mvn spring-boot:run


### Running the example in Kubernetes

It is assumed a running Kubernetes platform is already running. If not you can find details how to [get started](http://fabric8.io/guide/getStarted/index.html).

Assuming your current shell is connected to Kubernetes or OpenShift so that you can type a command like

```
kubectl get pods
```

or for OpenShift

```
oc get pods
```

The following command will create the required secret:

    kubectl create -f sample-secret.yml

or for Openshift

    oc create -f sample-secret.yml
    
Then the following command will package your app and run it on Kubernetes:

```
mvn fabric8:run
```

To list all the running pods:

    oc get pods

Then find the name of the pod that runs this quickstart, and output the logs from the running pods with:

    oc logs <name of pod>

You can also use the [fabric8 developer console](http://fabric8.io/guide/console.html) to manage the running pods, and view logs and much more.


### More details

You can find more details about running this [quickstart](http://fabric8.io/guide/quickstarts/running.html) on the website. This also includes instructions how to change the Docker image user and registry.

