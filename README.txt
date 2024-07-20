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


9. apt-get / yum
apt-get update
apt-get upgrade
apt install vimy
apt install openjdk-17-jdk openjdk-17-jre
apt-get install iputils-ping
apt-get install net-tools

yum list installed | grep odbc
wget https://download.docker.com
curl --no-check-certificate https://download.docker.com/linux/centos/docker-ce.repo > /etc/yum.repos.d/docker-ce.repo

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


16. ansible pull
docker pull edowon0623/ansible-server:ubuntu22.04
docker run --privileged --name ansible-server -itd -p 20022:22 -p 8082:8080 -e container=docker -v /sys/fs/cgroup:/sys/fs/cgroup edowon0623/ansible:latest /usr/sbin/init

ssh root@192.168.0.9 -p 20022 / P@ssw0rd


17. Docker bridge
C:\Users\dsme>docker network inspect bridge
ansible ip설정
"Subnet": "172.17.0.0/16","Gateway": "172.17.0.1"
"Name": "jenkins-server","IPv4Address": "172.17.0.2/16",
"Name": "ansible-server","IPv4Address": "172.17.0.5/16"
"Name": "docker-server" ,"IPv4Address": "172.17.0.3/16"
"Name": "tomcat9"       ,"IPv4Address": "172.17.0.4/16"


18. ssh-keygen
[root@738fc5e47a0f ~]# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:itoL9goyMhq9spRyxVhV9Y30WljwkRWIm4pBY/28+oY root@738fc5e47a0f
The key's randomart image is:
+---[RSA 3072]----+
|      ..o.. +.++o|
|     . + . + O.. |
|    . o . o * =  |
|   +   .   = o   |
|  . o   S . o    |
| ... . o . .     |
|Oo= . .   o      |
|BB *     E .     |
|oo+.+.    o.     |
+----[SHA256]-----+


19. ssh-copy-id
[root@738fc5e47a0f ~]# ssh-copy-id root@172.17.0.3
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host '172.17.0.3 (172.17.0.3)' can't be established.
ECDSA key fingerprint is SHA256:NFvA1cbhtoxDOMM7dfg4EC4cl32HEkUQXH6rToTU7pQ.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys

ssh root@172.17.0.3


20. ansible
[root@738fc5e47a0f ~]# vi /etc/ansible/hosts
[devops]
//172.17.0.2  //jenkins-server
172.17.0.3  //ansible-server
172.17.0.4  //docker-server

ansible-playbook first-playbook.yml
- name: Add an ansible hosts
  hosts: localhost
  tasks:
    - name: Add an ansible hosts
      blockinfile:
        path: /etc/ansible/hosts
        block: |
          [mygroup]
          172.17.0.5

ansible-playbook playbook-sample1.yml
cp test.txt sample.txt
- name: Ansible Copy Example Local to Remote
  hosts: devops
  tasks:
    - name: copying file with playbook
      copy:
        src: ~/sample.txt
        dest: /tmp
        owner: root
        mode: 0644

ansible-playbook playbook-sample2.yml
- name: Download Tomcat9 from tomcat.apache.org
  hosts: devops
  tasks:
   - name: Create a Directory /opt/tomcat9
     file:
       path: /opt/tomcat9
       state: directory
       mode: 0755
   - name: DDownload Tomcat using get_url
     get_url:
       url: https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.91/bin/apache-tomcat-9.0.91.tar.gz
       dest: /opt/tomcat9
       mode: 0755
       checksum: sha512:https://downloads.apache.org/tomcat/tomcat-9/v9.0.91/bin/apache-tomcat-9.0.91.tar.gz.sha512

ansible-playbook -i hosts first-devops-playbook.yml --check
ansible-playbook -i hosts first-devops-playbook.yml
- hosts: all
#   become: true

  tasks:
  - name: build a docker image with deployed war file
    command: docker build -t cicd-project-ansible .
    args:
        chdir: /root
  - name: create a container using cicd-project-ansible image
    command: docker run -d --name my_cicd_project -p 8081:8080 cicd-project-ansible

ansible-playbook -i hosts second-devops-playbook.yml
- hosts: all
#   become: true

  tasks:
  - name: stop current running container
    command: docker stop my_cicd_project
    ignore_errors: yes

  - name: remove stopped container
    command: docker rm my_cicd_project
    ignore_errors: yes

  - name: remove current docker images
    command: docker rmi cicd_project_ansible
    ignore_errors: yes

  - name: build a docker image with deployed war file
    command: docker build -t cicd_project_ansible .
    args:
       chdir: /root

  - name: create a container using cicd-project-ansible image
    command: docker run -d --name my_cicd_project -p 8081:8080 cicd_project_ansible