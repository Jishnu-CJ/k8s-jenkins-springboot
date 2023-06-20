# Jenkins CI/CD Pipeline setup to Deploy a Springboot application on EKS

In this demo, I've deployed a spring boot application on EKS with a CICD pipeline using Jenkins.

This CICD demo builds a spring boot application using Docker and pushes the images to the Docker hub, and the k8s will pull the images from the Docker hub and deploy them into the EKS cluster.

Tools we've used in the project:
* EKS
* GitHub
* Docker
* Jenkins
* AWS-EC2
* Dockerhub

Steps that followed

1. Create EC2 instance on AWS.
2. Connect with Ec2 instance via SSH
3. Install JDK on AWS EC2 Instance
4. Install and Setup Jenkins
5. Setup jenkins
6. Setup Gradle on Jenkins
7. Update visudo and assign administration privileges to jenkins user
8. Install Docker
9. Install and Setup AWS CLI
10. Configure AWS CLI
11. Install and Setup Kubectl
12. Create eks cluster and Node group from the AWS console
13. Add Docker and GitHub Credentials into Jenkins
14. Setup Docker Hub Secret Text in Jenkins
15. Setup GitHub Username and password into Jenkins
16. Write the jenkins pipelines
```
    node {
        stage("Git Clone"){
         git branch: 'main', credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/Jishnu-CJ/k8s-jenkins-springboot.git'
         sh "ls -lart ./*"
            }
        stage('Gradle Build') {
         sh  "chmod +x gradlew"
          sh './gradlew build'
    }
    stage("Docker build"){
        sh 'docker version'
        sh 'docker build -t jishnucj-docker-demo-test2 .'
        sh 'docker image list'
        sh 'docker tag jishnucj-docker-demo-test2 jishnucj/jishnucj-docker-demo-test2:jishnucj-docker-demo-test2'
    }
    withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
        sh 'docker login -u jishnucj -p $PASSWORD'
    }
    stage("Push Image to Docker Hub"){
        sh 'docker push  jishnucj/jishnucj-docker-demo-test2:jishnucj-docker-demo-test2'
    }
     stage("kubernetes deployment"){
        sh 'kubectl apply -f k8s-spring-boot-deployment.yml'
    }
}
```
17. Build, deploy and test CI/CD pipeline
18. Verify the deployments using kubectl commands
```
kubectl get deployments
kubectl get po
kubectl get svc
```
19. verify the application using the external IP
```
http://ae173bee2e9214528b1ca3420dfbe34c-1569324820.us-east-2.elb.amazonaws.com/hello
```
