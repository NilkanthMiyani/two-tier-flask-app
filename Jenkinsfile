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

        stage('Trivy File System Scan'){
         steps{
          sh "trivy fs . -o results.json"
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
                    docker tag myflaskapp $dockerHubUser/myflaskapp:latest
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
    
    post {
    success {
        mail to: 'miyaninilkanth2@gmail.com',
             subject: "Build Successful: ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
             body: "Good News: Your build is successful! Check it out here: ${env.BUILD_URL}"
    }
    failure {
        mail to: 'miyaninilkanth2@gmail.com',
             subject: "Build Failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
             body: "Bad News: Your build failed! Review the logs at: ${env.BUILD_URL}"
    }
}
}
