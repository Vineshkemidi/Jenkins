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
                withCredentials([string(credentialsId: 'docker-hub', variable: 'hubPwd')]) {
                    sh "docker login -u vineshkemidi -p ${hubPwd}"
                    sh "docker push vineshkemidi/hiring-app:${commit_id()}"
                }
            }
        }
        stage('Docker Deploy') {
            steps {
                sh "docker rm -f hiring || true"  // Remove the existing container, ignore error if it doesn't exist
                sh "docker run -d -p 8081:8080 --name hiring vineshkemidi/hiring-app:${commit_id()}"
            }
        }

    }
}

def commit_id(){
    id = sh returnStdout: true, script: 'git rev-parse HEAD'
    return id
}
