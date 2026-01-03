pipeline {
    agent any

    stages {

        stage('Cleanup') {
            steps {
                cleanWs()
            }
        }

        stage('Clone') {
            steps {
                git url: "https://github.com/nilkanthmiyani/two-tier-flask-app.git", branch: "master"
            }
        }

        stage('Build') {
            steps {
                sh '''
                docker build -t myapp .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "DockerHubCreds",
                    passwordVariable: "dockerHubPass",
                    usernameVariable: "dockerHubUser"
                )]) {
                    sh '''
                    echo "$dockerHubPass" | docker login -u "$dockerHubUser" --password-stdin
                    docker tag myapp $dockerHubUser/myapp:latest
                    docker push $dockerHubUser/myapp:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose down'
                sh 'docker compose up -d --build flask-app'
            }
        }
    }
}
