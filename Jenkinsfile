pipeline {
    agent { label 'docker-agent-python' }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/ahmedse/devops-demo1.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                sh 'python -m unittest discover'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('devops-demo1')
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // SSH into the production server and run the Docker container
                    sh """
                    ssh ahmed@91.108.121.172 << EOF
                        docker stop devops-demo1 || true
                        docker rm devops-demo1 || true
                        docker load < <(docker save devops-demo1)
                        docker run -d --name devops-demo1 -p 80:80 devops-demo1
                    EOF
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up local Docker images...'
            sh 'docker rmi devops-demo1 || true'
        }
    }
}
