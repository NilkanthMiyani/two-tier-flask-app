 pipeline {
    agent { label "dev"};

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
                docker build -t myflaskapp .
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
                    docker tag myapp $dockerHubUser/myflaskapp:latest
                    docker push $dockerHubUser/myflaskapp:latest
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
