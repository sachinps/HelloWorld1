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
  willPush=false;
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
                bat '''
                git branch -d $TMP_MERGE_BRANCH || true
                git branch $TMP_MERGE_BRANCH
                git checkout $TMP_MERGE_BRANCH
                git log --max-count=5 --graph --decorate --oneline --abbrev-commit origin/master $TMP_MERGE_BRANCH
                git branch -f master $TMP_MERGE_BRANCH
                git checkout master
                git branch -d $TMP_MERGE_BRANCH
                git symbolic-ref HEAD || true
                git rev-parse refs/heads/master || true
                git rev-parse HEAD || true
                git status
                git push https://sachinps:H7tDUgcl@github.com/sachinps/HelloWorld1.git master
                ''' 
            }
        }
    }
}	