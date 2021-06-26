node{
        stage('list repo contents and working directory') {
            script {
                    sh "ls"
                    sh "pwd"
                    sh "whoami"
                }
        } 
        stage('create build') {
            script {
                    sh "./gradlew build"
                }
        }
       
        stage('list the created build file') {
            script {
                    sh "ls build/libs/"
            }
        }
        stage('Remove old jar from DOCKER') {
            script {
                    sh "sudo rm DOCKER/spring-boot-with-prometheus-0.1.0.jar"
                }
        }
        stage('copy jar to DOCKER') {
            script {
                    sh "sudo cp build/libs/spring-boot-with-prometheus-0.1.0.jar DOCKER/"
                }
        }
        stage('building docker image') {
            script {
                    sh "cd DOCKER; sudo docker build -t 10141730/microk8s:v_${BUILD_NUMBER} ."
                }
        }
        stage('docker push') {
            script {
                    sh "sudo docker push 10141730/microk8s:v_${BUILD_NUMBER}"
                }
        }
        stage('deploying on microk8s') {
            script {
                    sh "sudo microk8s.helm3 upgrade --install microk8s --set image.tag=v_${BUILD_NUMBER} /home/madhur/kubernets-poc/"
                    sh "sudo microk8s.kubectl rollout status deployment.apps/microk8s-kubernets-poc"
                    sh "sudo docker images > unused_images_cid"
                }
        }
        stage ('remove images from build server') {
            script {
                    try {
                        sh "sudo docker images -a -q > images_cid; sudo docker rmi `cat images_cid`"
                    } catch (err) {
                        echo err.getMessage()
                    }
                }
                echo currentBuild.result
            }
       
}
