pipeline {
    environment {
        registry = "mahmoudshahen/hello"
        registryCredential = 'dockercred'
        dockerImage = ''
    }
    agent any
    stages {
        stage('Lint Python') {
            steps {
                sh 'echo Linting Python'
                sh 'pylint app.py'
            }
        }
        stage('Building image locally') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy Image to docker hub') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploy Kubernetes') {
            steps{
                withAWS(credentials: 'aws-static', region: 'us-east-2') {
                    sh 'sudo kubectl apply -f kub.yml'
                    sh 'sudo kubectl get service/capstone'
                }
            }
        }
    }
}
