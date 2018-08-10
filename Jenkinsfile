#!groovy
import static java.util.UUID.randomUUID
def branchName= 
try {
  if (branchName) {
    branchName = URLDecoder.decode(env.BRANCH_NAME, "UTF-8");
}
  else {
    branchName="master"
  }
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
        def willPush=false
        if ( branchName ==~ $/[/]?feature/.*/$ ) {
          println "Push feature branch ${branchName}"
          willPush=true
        }
        else if ( branchName ==~ $/[/]?sandbox/.*/$ ) {
          println "Do not push sandbox branch ${branchName}"
          willPush=true
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}	