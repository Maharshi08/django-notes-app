pipeline {
    agent { label 'node' }

    environment {
        // Set this if your repo is private. Replace 'GIT_CRED_ID' with your Jenkins credentials ID.
        GIT_CREDENTIALS = 'GIT_CRED_ID'
    }

    stages {

        stage('Code') {
            steps {
                echo "Cloning repository..."
                // If repo is public, you can omit 'credentialsId'
                git branch: 'main', url: 'https://github.com/Maharshi08/django-notes-app.git', credentialsId: "${GIT_CREDENTIALS}"
                echo "Code cloning successful"
            }
        }

        stage('Build') {
            steps {
                echo "Building the Docker image..."
                sh "whoami"
                // Using sudo in case Jenkins user cannot access Docker socket
                sh "sudo docker build -t notes-app:latest ."
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                // Add your actual test command here if available
                // Example: sh "python manage.py test"
            }
        }
        stage("Push to DockerHub"){
    steps{
        echo "This is pushing the image to Docker Hub"
        withCredentials([usernamePassword(
            credentialsId: 'dockerHubCred',
            passwordVariable: 'dockerHubPass',
            usernameVariable: 'dockerHubUser')]){
            
            sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass} "
            sh "docker image tag notes-app:latest ${env.dockerHubUser}/notes-app:latest"
            sh "docker push ${env.dockerHubUser}/notes-app:latest"
        }
    }
}

        stage('Deploy') {
            steps {
                echo 'Deploying with Docker Compose...'
                // Using sudo for Docker Compose as well
                sh "sudo docker compose up -d"
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
