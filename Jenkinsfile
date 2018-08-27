#!groovy
import static java.util.UUID.randomUUID 

properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactDaysToKeepStr: '20', artifactNumToKeepStr: '30', daysToKeepStr: '20', numToKeepStr: '30']]])

// echo "E-mail recipients:"
// echo "Culprits    : ${emailextrecipients([[$class: 'CulpritsRecipientProvider']])}"
// echo "Developpers : ${emailextrecipients([[$class: 'DevelopersRecipientProvider']])}"
// echo "Requester   : ${emailextrecipients([[$class: 'RequesterRecipientProvider']])}"
// echo "Suspects    : ${emailextrecipients([[$class: 'FirstFailingBuildSuspectsRecipientProvider']])}"

def notify(subject, to, body) {
  echo "notify ${subject}"
  if (!to) return;
  if (to.isEmpty()) return;
  try {
    mail subject: subject, to: to, body: body
  }
  catch(Exception e) {
    echo "Exception: ${e}"
  }
}

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

def jobName=env.JOB_NAME;
try {
  jobName = URLDecoder.decode(env.JOB_NAME, "UTF-8");
}
catch (UnsupportedEncodingException e) {
  echo "URLDecoder exception ${e}"
}
finally {
  echo "Job : ${jobName}"
}

def buildName="${jobName} #${env.BUILD_NUMBER}"
echo "Build : ${buildName}"

throttle(['Configurators-Kitchen-Feature-Validation-Multibranch']) {
  node('agent') {
    stage('Git') {
      sh("git config --list")

      try {
          checkout scm
      }
      catch (Exception e) {
        echo "Error on Git checkout"

        def subject = "${buildName} : git checkout failed !"
        def to      = emailextrecipients([[$class: 'DevelopersRecipientProvider']])
        def body    = "${buildName} build report: ${env.BUILD_URL}"
        notify(subject, to, body)

        echo "Exception: ${e}"
        echo "Result: ${currentBuild.result}"
        throw e
      }

      echo readFile('Jenkinsfile')
      sh '''
      echo Git state after Jenkins MergeBeforeBuild plugin :
      git ls-remote --refs origin refs/heads/master
      git symbolic-ref HEAD || true
      git rev-parse refs/heads/master || true
      git rev-parse HEAD || true
      git status
      git log --max-count=5 --graph --oneline --abbrev-commit --all --decorate=full
      '''
    }

    stage('Build App') {
      dir('gulp') {
        sh '''
          npm install
          gulp build-module --verbose --release --modules "Intro;Kitchen;KitchenRTL;BryoKitchen;BryoKitchenRTL;IkeaKitchen;IkeaKitchenRTL;TemplateEditorKitchen"
        '''
      }
      dir('services') {
        sh '''
          ./build-services.sh --release
        '''
      }
    }

    stage('Test App') {
      try
      {
        sh '''
          npm install
          npm run run-tests-docker -- -m release -a preprod-platform.ikea.by.me
        '''
        junit healthScaleFactor: 5.0, testResults: '**/generated/release/**/tests-report.xml'
      }
      catch (Exception e)
      {
        currentBuild.result = 'UNSTABLE'
        echo "Error while running tests"
        echo "Exception: ${e}"
      }
      finally
      {
        // Tests are built and run in a Docker Container - generated files belong to root
        // Give these files to Jenkins user
        sh '''
        sudo chown -R $(id -u):$(id -g) ./generated
      '''
      }

      echo "Current build result: ${currentBuild.result}"
      def subject = "[Tests report] ${buildName} - " + ((currentBuild.result == 'UNSTABLE') ? "KO" : "OK")
      def to      = emailextrecipients([[$class: 'DevelopersRecipientProvider']])
      def body    = """\
                    ${buildName}
                    Build report : ${env.BUILD_URL}
                    Tests report : ${env.BUILD_URL}testReport/""".stripIndent()
      notify(subject, to, body)

      // Fail job if some tests are KO..
      /*if (currentBuild.result == 'UNSTABLE')
      {
        error("Build failed because units tests are KO...")
      }*/
    }

    def willPush=false
    if ( branchName ==~ $/[/]?feature/.*/$ ) {
      println "Push feature branch ${branchName}"
      willPush=true
    }
    else if ( branchName ==~ $/[/]?sandbox/.*/$ ) {
      println "Do not push sandbox branch ${branchName}"
    }
    else if ( branchName == "master" ) {
      echo "Do not push master branch"
    }
    else {
      echo "Do not push ${branchName} branch"
    }

    if (willPush) {
      stage('Git Push') {
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
    }
  }
}
