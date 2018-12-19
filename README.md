# A This repo is for Continuous Integration and Continuous Delivery by Dockerize Jenkins Pipeline.

## GitHub configuration
We will define a service on Github to call the ``Jenkins Github webhook`` because we want to trigger the pipeline. To do this go to _Settings -> Integrations & services._ The ``Jenkins Github plugin`` should be shown on the list of available services as below.

## Review important points of the Jenkins file
```
stage('Initialize'){
    def dockerHome = tool 'myDocker'
    def mavenHome  = tool 'myMaven'
    env.PATH = "${dockerHome}/bin:${mavenHome}/bin:${env.PATH}"
}
```

The ``Maven`` and ``Docker client`` tools we have defined in Jenkins under _Global Tool Configuration_ menu are added to the ``PATH environment variable`` for using these tools with ``sh command``.

```
stage('Push to Docker Registry'){
    withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
        pushToImage(CONTAINER_NAME, CONTAINER_TAG, USERNAME, PASSWORD)
    }
}
```

``withCredentials`` provided by ``Jenkins Credentials Binding Plugin`` and bind credentials to variables. We passed **dockerHubAccount** value with ``credentialsId`` parameter. Remember that, dockerHubAccount value is Docker Hub credentials ID we have defined it under _Jenkins Home Page -> Credentials -> Global credentials (unrestricted) -> Add Credentials_ menu. In this way, we access to the username and password information of the account for login.

## Sonarqube configuration

For ``Sonarqube`` we have made the following definitions in the ``pom.xml`` file of the project.

```xml
<sonar.host.url>http://sonarqube:9000</sonar.host.url>
...
<dependencies>
...
    <dependency>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>sonar-maven-plugin</artifactId>
        <version>2.7.1</version>
        <type>maven-plugin</type>
    </dependency>
...
</dependencies>
```

In the docker compose file, we gave the name of the Sonarqube service which is ``sonarqube``, this is why in the ``pom.xml`` file, the sonar URL was defined as http://sonarqube:9000.
