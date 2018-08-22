#!groovy
import static java.util.UUID.randomUUID
def branchName=env.BRANCH_NAME;
def TMP_MERGE_BRANCH = "tmp-merge-${env.BUILD_NUMBER}"
try {
  if (branchName) {
    branchName = URLDecoder.decode(env.BRANCH_NAME, "UTF-8");
     echo "Branch : ${branchName}"
     echo "TMP_MERGE_BRANCH : ${TMP_MERGE_BRANCH}"
     echo "git branch -d $TMP_MERGE_BRANCH || true"
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
                echo 'Deploying....'
                sh '''
                    echo "hello world"
                '''
            }
        }
    }
}	