#!groovy
import static java.util.UUID.randomUUID
def branchName=env.BRANCH_NAME;
try {
  if (branchName) {
    branchName = URLDecoder.decode(env.BRANCH_NAME, "UTF-8");
}
  else {
    branchName="master"
  }
}
catch (UnsupportedEncodingException e) {
  echo "URLDecoder exception ${e}"
}
finally {
  echo "Branch : ${branchName}"
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