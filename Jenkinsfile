#!/usr/bin/env groovy

pipeline {
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    agent any
    stages {
        stage ('parallel builds'){
            parallel {
                stage('test1 docker build') {
                    when {
                        expression {
                            return "true" == sh(returnStdout: true, script: 'services/test1/trackCICDchanges.sh services/test1/folders.watch | tail -n 1').trim()
                        }
                    }
                    steps {
                        dir ("services/test1") {
                        sh " docker build -f subdir2/Dockerfile ."
                        }
                    }
                }
                stage('test2 docker build') {
                    when {
                        expression {
                            return "true" == sh(returnStdout: true, script: 'services/test2/trackCICDchanges.sh services/test2/folders.watch | tail -n 1').trim()
                        }
                       // branch 'dev'
                    }
                    steps {
                        dir ("services/test2") {
                            sh " docker build -f subdir3/Dockerfile ."
                        }
                    }
                }
            }
        }
    }
    post{
        always{
            cleanWs()
        }
    }
}
