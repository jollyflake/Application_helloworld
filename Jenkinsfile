pipeline {
    agent any
    environment {
        IMAGE_NAME = "jaijp/javaapp" // Replace
        IMAGE_TAG = "${BUILD_NUMBER}-${BRANCH_NAME}"
    }
    
    stages {
        stage('Hello') {
            steps {
                echo 'Hello, World from Jenkins Pipeline!'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package' // Use Maven to build the project
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test' // Or your test command. Add -DskipTests to skip if needed for initial setup.
            }
            post {
                failure {
                    echo "Tests Failed. Check the console output for details."
                    // Optionally send notifications or take other actions
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_REGISTRY}:${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-credentials') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) { // Configure kubeconfig credential in Jenkins
                    sh "kubectl apply -f deployment-definition.yaml" // Your Kubernetes deployment YAML
                    sh "kubectl set image deployment/your-deployment-name your-container-name=${jaijp/javaapp}:${latest}" // Replace
                    sh "kubectl rollout status deployment/your-deployment-name" // Check rollout
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        failure {
            echo "Pipeline Failed!"
            // Add notification logic here (e.g., email, Slack)
        }
        success {
            echo "Pipeline Completed Successfully!"
        }
    }
}