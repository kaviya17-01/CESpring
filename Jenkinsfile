pipeline {
    agent any

    environment {
        IMAGE_NAME = 'kaviya1701/course-enrollment-app'
        JAVA_OPTS = "-Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=86400"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/kaviya17-01/CESpring.git'
            }
        }

        stage('Build with Maven') {
            steps {
                dir('CourseEnrollment') {
                    sh 'mvn clean package -DskipTests=true -Dmaven.test.skip=true'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('CourseEnrollment') {
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                dir('CourseEnrollment') {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh 'docker push $IMAGE_NAME'
                    }
                }
            }
        }

        stage('Run Container (Optional)') {
            steps {
                sh 'docker run -d -p 8081:8081 $IMAGE_NAME'
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
            echo ' Pipeline failed. Check the logs.'
        }
    }
}

