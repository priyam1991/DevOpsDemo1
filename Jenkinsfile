pipeline {
    agent any

    tools {
        
        maven "maven_3.8.5"
    }

    stages {
        stage('Build') {
            steps {
              checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ampatil/devops-automation.git']])
              sh "mvn -Dmaven.test.failure.ignore=true clean package" 
            }
        }
        
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t ampatil/devops-integration .'
                }
            }
        }
        
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                   sh 'docker login -u ampatil -p ${dockerhub}'

                   }
                   sh 'docker push ampatil/devops-integration'
                }
            }
        }
        
         stage('deploy to remote server'){
            steps{
                ansiblePlaybook credentialsId: 'ansibleid', installation: 'ansible', disableHostKeyChecking: true, inventory: 'inventory', playbook: 'playbook.yml'
            }
        }
    }
}
