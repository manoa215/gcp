#========Docker====================
Create Dockerfile inside /Docker folder
	# Alpine Linux with OpenJDK JRE
	FROM openjdk:8-jre-alpine
	# copy JAR into image
	COPY employees-management-1.0.0.war /app.war 
	# run application with this command line 
	CMD ["/usr/bin/java", "-jar", "-Dspring.profiles.active=default", "/app.war"]

This copies the jar into the docker directory as part of the package build target. 

Make sure your pom.xml has this block in the plugins section:
	<plugin>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-maven-plugin</artifactId>
		<executions>
			<execution>
				<goals>
					<goal>repackage</goal>
				</goals>
				<configuration>
					<mainClass>com.ust.efx.poc.usermanagement.UserManagementApplication</mainClass>
					<outputDirectory>${project.basedir}/docker</outputDirectory>
				</configuration>
			</execution>
		</executions>
	</plugin>

# goto Java root dir
$ mvn package
$ cd docker
$ docker build -t employees-management:latest .
$ docker image ls
$ docker run -d  -p 8080:80 employees-management:latest
$ docker run -p 8080:8080 --name my-employees-app employees-management:latest
$ docker ps
$ docker history employees-management:latest
$ docker stop [container_id]

gcloud config list project
cd docker && docker build -t employees-management:0.1 .
docker tag employees-management:0.1 gcr.io/[project-id]/employees-management:0.1
docker images
docker push gcr.io/[project-id]/employees-management:0.1
docker stop $(docker ps -q)
docker rm $(docker ps -aq)

#To Test Optional - Remove Child images
docker rmi employees-management:0.1 gcr.io/[project-id]/employees-management employees-management:0.1
docker rmi node:6
docker rmi $(docker images -aq) # remove remaining images
docker images
#Now we have pseudo env.. Test docker
docker pull gcr.io/[project-id]/employees-management:0.1
docker run -p 8080:8080 -d gcr.io/[project-id]/employees-management:0.1
curl http://localhost:8080


#==========Kubernetes==============
gcloud config set compute/zone us-central1-a
gcloud container clusters create [CLUSTER-NAME]
gcloud container clusters get-credentials [CLUSTER-NAME]
kubectl run employee-api-server --image=gcr.io/learngcpniran/employees-management:0.1 --port 8080
kubectl expose deployment employee-api-server --type="LoadBalancer"
kubectl get service employee-api-server
curl http://[EXTERNAL-IP]:8080
