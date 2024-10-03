pipeline {
    agent any

    stages {
        
        stage('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build . -t vineshkemidi/hiring-app:${commit_id()}"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub', variable: 'Vinesh@1125')]) {
                    sh "docker login -u vineshkemidi -p ${hubPwd}"
                    sh "docker push vineshkemidi/hiring-app:${commit_id()}"
                }
            }
        }
        stage('Docker Deploy') {
            steps {
                sshagent(['docker-host']) {
                    sh "ssh -o StrictHostKeyChecking=no  ec2-user@3.91.24.109 docker rm -f hiring"
                    sh "ssh  ec2-user@3.91.24.109 docker run -d -p 8080:8080 --name hiring vineshkemidi/hiring-app:${commit_id()}"
                }
            }
        }

    }
}

def commit_id(){
    id = sh returnStdout: true, script: 'git rev-parse HEAD'
    return id
}
