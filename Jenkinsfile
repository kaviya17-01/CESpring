pipeline {
    agent any

    environment {
        IMAGE_NAME = 'kaviya1701/course-enrollment-app:latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/kaviya17-01/CESpring.git'
            }
        }

        stage('Build with Maven') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    sh 'mvn clean package -DskipTests=true -Dmaven.test.skip=true'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Run Container (Optional)') {
            steps {
                sh 'docker run --rm -d -p 8080:8080 $IMAGE_NAME'
            }
        }
    }

    post {
        always {
            echo 'Cleaning workspace...'
            cleanWs()
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}



