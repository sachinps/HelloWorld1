#!groovy
import static java.util.UUID.randomUUID
def branchName=env.BRANCH_NAME;
try {
  if (branchName) {
    branchName = URLDecoder.decode(env.BRANCH_NAME, "UTF-8");
     echo "Branch : ${branchName}"
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
                sh 'echo "Hello World"'
            }
        }
    }
}	