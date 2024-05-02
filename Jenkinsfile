pipeline {
    agent any
    tools{
        maven 'maven_3_9_6'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/siddireddylakshman/mit-sem2-capstone']]])
                bat 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    bat 'docker build -t student-management-system .'
                }
            }
        }
         stage('Push image to Hub'){
            steps{
                script{                                                       
                   bat 'docker login -u siddireddy --password-stdin < c:/manoj/dockerpassword.txt'                    
                   bat 'docker tag student-management-system.jar siddireddy/mymtechproj:myfirstimagepush'
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
