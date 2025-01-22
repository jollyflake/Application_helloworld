pipeline {
    agent {
        kubernetes {
            inheritFrom 'maven-agent' // Label of a Kubernetes pod template in Jenkins
        }
    }
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
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/jollyflake/Application_helloworld.git' // Replace
                script {
                    BRANCH_NAME = env.BRANCH_NAME.replaceAll('/','-')
                }
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
        stage('Build Docker Image') {
            agent { dockerContainer { image 'docker:latest' } } // Use the Docker agent
            steps {
                sh 'docker build -t jaijp/javaapp:latest .' // Build the image
                sh 'docker images' // Optional: List images to verify
            }
        }
        stage('Push Docker Image') {
            agent { dockerContainer { image 'docker:latest' } }
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                    sh "docker push jaijp/javaapp:latest"
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