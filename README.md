# ContinuousDeployment
This Repository will tell Ascending students how to prepare for Continuous Deployment process

Students should have completed following stages before they meet up with DevOps engineer.
- Upload source code to GitHub repository
- Fullfill unit test stage in docker container
- Package **.war** file in docker container
- Build Docker image with **.war** file and Dockerfile
- Launch containeried application successfully

## Upload source code to GitHub repository

Make sure you have uploaded the most current version of source code in a public GitHub repository, also make sure **no credential (including but not limited to AWS credentials, database URL, username and password, third-party credentials) is stored in plaintext in your source code.**

## Complete unit test stage in docker container

Firstly, retrieve maven:3.6.0-jdk-8 image
```bash
docker pull maven:3.6.0-jdk-8
```
Then run a container on this image and interact with it
```bash
docker run -it maven:3.6.0-jdk-8 /bin/bash
``` 
A docker container can be regarded as an independent linux system, please try basic linux command lines like **ls, whoami, cd and pwd** to get familiar with it.
Then you can use **git clone** command to retrieve the source code from GitHub.
On the other side, you should have your postgresql database available. please use this command line to find the internal IP address of container
```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ${CONTAINER_ID}
```
When working inside this maven:jdk container, it will be pointless to try to connect database on localhost:5432. Localhost means the system itself, and it's obvious that no database runs on 5432 port in maven:jdk container. Instead, the DATABASE_URL should be the internal IP address we got before.
Then you can run unit test and troubleshoot error in this container.

```bash
mvn clean compile flyway:migrate -P unit -Ddb_url=localhost ...
mvn test -Ddb_url=localhost ...
```
As we can't use IDE tools in container, we should excute database migration and unit test in linux command line. You can find an example command above, and you should revise it based on your own configuration.

## Package **.war** file in docker container
In the same container above, package a **.war** file. 
```bash
mvn compile package -DskipTests=true -q
```
Answer following questions:
1. Does this **.war** file contain database information?
2. If the answer to Question 1 is Yes, what should you do if you want to change database configuration?
3. If the answer to Question 1 is No, how should you input the database configuration?

## Build Docker image with **.war** file, setenv.sh and Dockerfile in Local Machine
Before we start this part, please answer following questions:
[What does ENV and COPY in Dockerfile stand for?](https://docs.docker.com/engine/reference/builder/)
[How does setenv.sh and ROOT.war work?](https://dotcms.com/docs/latest/deploy-as-a-war-in-tomcat)
Then try to build your iamge with dockerfile (including environment variables like DB_URL, DB_PASSWORD), setenv.sh (use environment variables as JVM options) and **.war** file.

## Launch Containerized Application Locally
The last stage is to launch a containerized application in your local machine. The command is
```bash
docker run -e ENV_VAR=VAL ${IAMGE_ID}
```
Try this command line, collect the logs and troubleshoot.
