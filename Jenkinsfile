#!groovy
import static java.util.UUID.randomUUID
def branchName=env.BRANCH_NAME;


def willPush=false
if ( branchName ==~ $/[/]?feature/.*/$ ) {
  println "Push feature branch ${branchName}"
  willPush=true
}
else if ( branchName ==~ $/[/]?sandbox/.*/$ ) {
  println "Do not push sandbox branch ${branchName}"
  willPush=true
}
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