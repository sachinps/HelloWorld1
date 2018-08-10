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
        stage('Git Push'){
          steps{
            echo 'Git Push'
          }
            def pushResult="pushed"
            try
            {
              withEnv(["TMP_MERGE_BRANCH=tmp-merge-${env.BUILD_NUMBER}"]){
                  sh '''
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
                  git push origin master
                  '''
              }
            }
            catch (Exception e) {
              pushResult="push failed !"
              currentBuild.result = 'FAILURE'

              echo "Error on Git Push"
              echo "Exception: ${e}"
              echo "Result: ${currentBuild.result}"
            }
            finally {
              def subject = "${buildName} ${pushResult}"
              def to      = emailextrecipients([[$class: 'DevelopersRecipientProvider']])
              def body    = "${buildName} build report : ${env.BUILD_URL}"
              notify(subject, to, body)
            }
          }
        
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}	