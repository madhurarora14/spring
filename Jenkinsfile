pipeline {
    agent any
    stages {
        stage('list repo files') {
            steps {
                script {
                    sh "ls"
                }
            }
        }
        stage('show working directory') {
            steps {
                script {
                    sh "pwd"
                }
            }
        }
        stage('creating build') {
            steps {
                script {
                    sh "/home/madhur/Desktop/ENVEU/spring/gradlew clean build"
                }
            }
        }
        stage('list build contents') {
            steps {
                script {
                    sh "ls build/libs"
                }
            }
        }
        stage ('remove old jar from Docker') {
            steps {
                script {
                    sh "sudo rm DOCKER/spring-boot-with-prometheus-0.1.0.jar"
                }
            }
        }
        stage('copy buid file to docker build context') {
            steps {
                script {
                    sh "sudo cp build/libs/spring-boot-with-prometheus-0.1.0.jar DOCKER/"
                }
            }
        }
        stage('build docker image and pushing to dockerhub') {
            steps {
                script {
                    sh "cd DOCKER; sudo docker build -t 10141730/microk8s:v_${BUILD_NUMBER} ."
                    sh "sudo docker push 10141730/microk8s:v_${BUILD_NUMBER}"
                }
            }
        }
        stage('deploying on microk8s') {
            steps {
            	script {
                    sh "sudo microk8s.kubectl apply -f deployment.yaml"
                    sh "sudo microk8s.kubectl set image deployments/microk8sdeploy microk8scontainer=10141730/microk8s:v_${BUILD_NUMBER}"
                }
            }
        }
        stage('delete docker images'){
            steps {
                script {
                    sh "cd DOCKER; sudo docker images -a -q"
                    sudo "docker images -a -q > images_cid; sudo docker rmi `cat images_cid`"
                    
                }
            }
        }
    }
}
