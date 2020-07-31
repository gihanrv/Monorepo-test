#!/usr/bin/env groovy

def list = [
    "test1", 
    "test2", 
    "test3"
]
pipeline {
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    agent any
    stages {
        // stage ('parallel builds'){
            // parallel {
                stage('changed services') {
                    steps {
                        script {
                            for(int i=0; i < list.size(); i++) {
                                matches1 = sh (
                                script: "git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$2'} | uniq | grep -x '${list[i]}'", returnStatus: true
                            ) == 0
                            if("$matches1"!="false"){
                                stage(list[i] + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script {
                                            dir ("services/${list[i]}/") {
                                                // sh "docker build -f build/Dockerfile ."
                                                sh "pwd"
                                                sh "ls -al"
                                            }
                                        }    
                                    }
                                }
                            }
                            }
                        }
                    }
                }
                // stage('test2 docker build') {
                //     when {
                //         expression {
                //             return "true" == sh(returnStdout: true, script: 'services/test2/trackCICDchanges.sh services/test2/folders.watch | tail -n 1').trim()
                //         }
                //        // branch 'dev'
                //     }
                //     steps {
                //         dir ("services/test2") {
                //             sh " docker build -f subdir3/Dockerfile ."
                //         }
                //     }
                // }
            // }
        // }
    }
    post{
        always{
            cleanWs()
        }
    }
}
