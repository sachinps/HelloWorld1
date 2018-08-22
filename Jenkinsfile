pipeline {
    agent any
    environment { 
        CC = 'clang'
    }
    stages {
        stage('Example') {
            environment { 
                DEBUG_FLAGS = '-g'
            }
            steps {
                sh """\
                    #!/bin/sh
                    echo hello sh!
                """
            }
        }
    }
}