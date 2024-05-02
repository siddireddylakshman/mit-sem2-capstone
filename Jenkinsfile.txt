pipeline {
    agent any
    tools{
        maven 'maven_3_9_6'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Java-Techie-jt/devops-automation']]])
                bat 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    bat 'docker build -t javatechie/devops-integration .'
                }
            }
        }
         stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([usernamePassword(credentialsId: 'DockerNew', usernameVariable: 'PEGA_DB_USERNAME', passwordVariable: 'PEGA_DB_PASSWORD')]) {                                    
                   bat 'docker login -u siddireddy --password-stdin < f:/password.txt' 
                   }
                   bat 'docker tag javatechie/devops-integration siddireddy/mymtechproj:myfirstimagepush'
                   bat 'docker push siddireddy/mymtechproj:myfirstimagepush'
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}
