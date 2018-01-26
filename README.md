# hello-world-docker-springboot

docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry


docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager

FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD build/libs/hello-world-0.0.1-SNAPSHOT.jar hello-world-0.0.1-SNAPSHOT.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","hello-world-0.0.1-SNAPSHOT.jar"]

buildscript {
	ext {
		springBootVersion = '1.5.9.RELEASE'
	}
	repositories {
		mavenCentral()
		jcenter()
		maven {
	      url "https://plugins.gradle.org/m2/"
	    }
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
		classpath "gradle.plugin.com.palantir.gradle.docker:gradle-docker:0.17.2"
		classpath 'com.bmuschko:gradle-docker-plugin:3.2.2'
	}
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
//apply plugin: 'com.palantir.docker'
apply plugin: 'com.bmuschko.docker-remote-api'

group = 'com.first.gradle.project'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
	mavenCentral()
}


dependencies {
	compile('org.springframework.boot:spring-boot-starter-hateoas')
	compile('org.springframework.boot:spring-boot-starter')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}

//mainClassName = 'com.first.gradle.project.helloworld.HelloWorldApplication'
//import com.bmuschko.gradle.docker.tasks.image.Dockerfile
import com.bmuschko.gradle.docker.tasks.image.DockerBuildImage

//task createDockerfile(type: Dockerfile) {
   // destFile = project.file('Dockerfile')
    //from 'ubuntu:12.04'
    //maintainer 'Benjamin Muschko "benjamin.muschko@gmail.com"'
//}

task buildImage(type: DockerBuildImage) {
	dependsOn assemble
	dockerFile = project.file('Dockerfile')
    inputDir = project.rootDir
    tag = "prakash3210/hello-world-gradle-docker:${project.version}"
}

docker {
	if (System.env.containsKey('DOCKER_HOST') && System.env.containsKey('DOCKER_CERT_PATH')) {
        url = System.env.DOCKER_HOST.replace("tcp", "https")
        certPath = new File(System.env.DOCKER_CERT_PATH)
    }
	//url = 'tcp://192.168.99.100:2375'
}
