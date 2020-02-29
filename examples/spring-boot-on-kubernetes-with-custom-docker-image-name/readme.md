#Example:  Add custom container image name to generated yaml manifests 

An example that showcases how to change the container image property name in the generated manifest. 
To customize the generated manifests you need to add dekorate properties to your `application.yml` or `application.properties` descriptors. 
and have the following dependency in your class path:

    <dependency>
          <groupId>io.dekorate</groupId>
          <artifactId>dekorate-spring-boot</artifactId>
          <version>${project.version}</version>
    </dependency>

The following dekorate docker properties (if you use an application.properties file)
```
dekorate.docker.group=custom-registry
dekorate.docker.name=custom-image
dekorate.docker.version=1.1

```

Compile the project using:

    mvn clean install
    
You can find the generated deployment under: `target/classes/dekorate/kubernetes.yml` that should look like:
```---
---
apiVersion: "v1"
kind: "Service"
metadata:
  labels:
    app: "spring-boot-on-kubernetes-without-annotations"
    version: "0.10-SNAPSHOT"
    group: "custom-image-on-kubernetes"
  name: "spring-boot-on-kubernetes-without-annotations"
spec:
  ports:
  - name: "http"
    port: 8080
    targetPort: 8080
  selector:
    app: "spring-boot-on-kubernetes-without-annotations"
    version: "0.10-SNAPSHOT"
    group: "custom-image-on-kubernetes"
  type: "ClusterIP"
---
apiVersion: "apps/v1"
kind: "Deployment"
metadata:
  labels:
    app: "spring-boot-on-kubernetes-without-annotations"
    version: "0.10-SNAPSHOT"
    group: "custom-image-on-kubernetes"
  name: "spring-boot-on-kubernetes-without-annotations"
spec:
  replicas: 1
  selector:
    matchLabels:
      app: "spring-boot-on-kubernetes-without-annotations"
      version: "0.10-SNAPSHOT"
      group: "custom-image-on-kubernetes"
  template:
    metadata:
      labels:
        app: "spring-boot-on-kubernetes-without-annotations"
        version: "0.10-SNAPSHOT"
        group: "custom-image-on-kubernetes"
    spec:
      containers:
      - env:
        - name: "KUBERNETES_NAMESPACE"
          valueFrom:
            fieldRef:
              fieldPath: "metadata.namespace"
        image: "custom-registry/custom-image:1.1"
        imagePullPolicy: "IfNotPresent"
        name: "spring-boot-on-kubernetes-without-annotations"
        ports:
        - containerPort: 8080
          name: "http"
          protocol: "TCP"
```
