# spring-demo-with-maven

This demo uses the snapshot for [fabric8-maven-plugin](https://github.com/Microsoft/fabric8-maven-plugin/tree/snap)'s snap branch.

## Requirements

### Local Environment
* [Java 7 or above](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Maven 3](http://maven.apache.org/)
* [Groovy](http://groovy-lang.org/) - If not install this, the fabric8-maven-plugin's integration test will fail but intalling package to local will success.
* [Docker](https://www.docker.com/)

### Prepare your Kubernetes cluster and private docker registry
* [Kubernetes on Azure Container Service](https://docs.microsoft.com/en-us/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
* [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-azure-cli)

> The Kubernetes on ACS should be created in an empty resource group.

## Install the fabric8-maven-plugin in local machine
Run the following command to download and install this maven plugin, since by default the the code is running on a linux machine, the Windows director sperator `:` will cause a test failed. Here simply ignore the test when running on Windows machine.

```bash
git clone https://github.com/Microsoft/fabric8-maven-plugin.git
cd fabric8-maven-plugin
git checkout snap
mvn install -Dmaven.test.skip=true
cd it
mvn install
cd ..
mvn install -Dmaven.test.skip=true
```

## Configure server for this demo

Sync this repo to your local machine and swith to the repo folder.

Edit the `~/.m2/settings.xml` configure the `<Server>` part with your private docker registry information.

> The server id field must be the registry URL for your docker registry as [fabric8 maven plugin](https://maven.fabric8.io/#authentication)'s documentation.

```xml
<server>
  <id>ydocker.io(this should be the docker registry URL)</id>
  <username>username</username>
  <password>password for your docker registry</password>
  <configuration>
    <email>foo@foo.com</email>
  </configuration>
</server>
```
In the `pom.xml`, configure the `<docker.image.prefix>` with your docker registry URL, which also is the server id in `settings.xml`.

```xml
<properties>
    <docker.image.prefix>docker.io</docker.image.prefix>
</properties>
```

## Run this demo

You can combine the following commands into one line or run them separately:

```bash
mvn package docker:build docker:push fabric8:secrets fabric8:resource fabric8:apply-secrets fabric8:apply
```

### Push image to your registry

Run the following command to package your jar and build the docker image

```bash
mvn package docker:build
```

Push the docker image to your registry

```bash
mvn docker:push
```

### Build the Kubernetes resource yaml file

```bash
mvn fabric8:secrets fabric8:resource
```

### Apply the Kubernetes resource yaml file to cluster

```bash
mvn fabric8:apply-secrets fabric8:apply
```
