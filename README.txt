[data 흐름]
        -> source code : github(*/main) : https://github.com/jelee0604/cicd-web-project
	-> docker : jenkins : /var/jenkins_home/workspace/My~/src,target
        -> build : pom.xml, (clean compile package) -> target/hello-world.war
        -> 빌드후조치 -> deploy war/ear to container -> war/ear file : **/*.war 
                                                                        -> tomcat9 : (deployer : credential)
                                                                        -> tomcat url : http://192.168.0.9:8088/manage/text)
                             -> SSH publish -> docker-server(system) -> source file : target/*.war
                                                                                     -> remove pre : target
                                                                                     -> remote dir : .
                                                                                     -> exec com : docker build --tag=cicd-project -f Dockerfile
                                                                                                         docker run -d -p 8080:8080 --name mytomcat cicd-project:latest
0. github
https://github.com/joneconsulting/cicd-web-project
https://github.com/jelee0604/cicd-web-project

echo "# cicd-web-project" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/jelee0604/cicd-web-project.git
git push -u origin main


1. docker-desktop
https://www.docker.com/products/docker-desktop/


2. docker execute
docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 --name jenkins-server --restart=on-failure jenkins/jenkins:lts-jdk17


3. docker container conn
docker ps 
docker images jenkins/jenkins, gitlab/gitlab-ce, alpine/git

docker (container) exec -it jenkins-server/e805404cb34c /bin/bash
C:\Users\dsme>docker container exec -it jenkins-server /bin/bash
jenkins@e805404cb34c:/$ git --version
git version 2.39.2


4. docker-jenkins
https://hub.docker.com/r/jenkins/jenkins


5. docker-jenkins install
docker pull jenkins/jenkins:lts-jdk17 or docker pull jenkins/jenkins:jdk17
docker container ls
docker container exec -it -u:root 39daasdfdfc1f /bin/bash


6. jenkins
http://localhost:8080/
admin / admin@123


7. Tomcat install
docker ps -a
docker pull tomcat
docker pull tomcat:9.0.91-jre8
docker rename heuristic_feistel tomcat9

docker container stop tomcat9
docker container commit tomcat9 tomcat90
docker run -d -it -v tomcat_home:/usr/local/tomcat -p 8888:8080 --name tomcat9 tomcat:9.0.91-jre8

docker exec -it tomcat9 /bin/bash
mv webapps webapps2
mv webapps.dist/ webapps

http://127.0.0.1:8888


8. tomcat 설정
/usr/local/tomcat/conf/tomcat-users.xml
/usr/local/tomcat/webapps/manager/META-INF/context.xml


9. vim설치
apt-get update
apt-get upgrade
apt install vimy
apt install openjdk-17-jdk openjdk-17-jre
apt-get install iputils-ping
apt-get install net-tools


10. jenkins 설정
빌드후조치
WAR/EAR files : **/*.war
Credentials : deployer/deployer
Tomcat URL : http://192.168.0.9:8088/manager/text


11. tomcat-user.xml
  <user username="admin" password="admin" roles="admin-gui,admin-script,manager-gui,manager-script,manager-jmx,manager-status"/>
  <user username="deployer" password="deployer" roles="manager-script"/>
  <user username="tomcat" password="deployer" roles="manager-gui"/>

  <role rolename="admin-gui"/>
  <role rolename="admin-script"/>
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>


12. docker cp
cp jenkins-server:/var/jenkins_home/workspace/My-Third-Project/target/hello-world.war ./
docker cp hello-world.war tomcat9:/usr/local/tomcat/webapps/ROOT/


13. docker-server
docker pull edowon0623/docker-server:ubuntu22.04
docker run --privileged --name docker-server -itd -p 10022:22 -p 8081:8080 -e container=docker -v /sys/fs/cgroup:/sys/fs/cgroup  edowon0623/docker-server:ubuntu22.04 /usr/sbin/init


14. docker-server 내 Dockerfile파일 생성
-.Dockerfile 내용
	FROM tomcat:9.0

	LABEL org.opencontainers.image.authors="edowon0623@gmail.com"

	COPY ./hello-world.war /usr/local/tomcat/webapps
-.Docker Build (image생성)
	docker build --tag docker-server -f Dockerfile .
-.Docker image생성확인
	docker images
	docker images inspect docker-server


15. docker-server 내 tomcat 구동
ex) -name : unique이름, image명 : repository:tag
docker run -d -p 8080:8080 -name mytomcat docker-server:latest


