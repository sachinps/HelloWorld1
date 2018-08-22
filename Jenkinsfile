#!groovy
import static java.util.UUID.randomUUID
def branchName=env.BRANCH_NAME;
pipeline {	
    agent any

    stages {
        stage('Build') {
            steps {
                echo "Building.:${branchName}"
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying....'
                sh 'echo hello world'
            }
        }
    }
}	