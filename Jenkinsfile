pipeline {
    agent any // Run on any available agent

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
    }
}