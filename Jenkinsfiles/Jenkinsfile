#!/usr/bin/env groovy

// list of services
def list = [
  "service1",
  "service2",
  "service3",
  "service4",
  "service4v2",
  "service5",
  "service6",
  "service6listener",
  "service7",
  "service8",
  "service8v2",
  "service9",
  "service10"
]
pipeline {
    options {
        buildDiscarder(logRotator(daysToKeepStr: '6'))
    }
    agent any
    stages {
        stage('Preparing updated services') {
            steps {
                script {
                    // for loop to check all the services
                    for(int i=0; i < list.size(); i++) {
                        if("${list[i]}"!="service4" && "${list[i]}"!="service4v2" && "${list[i]}"!="service9" && "${list[i]}"!="service9v2" && "${list[i]}"!="service6" && "${list[i]}"!="service6listener" && "${list[i]}"!="service7" && "${list[i]}"!="service8" ){
                            matches1 = sh (
                                script: "git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$2'} | uniq | grep -x '${list[i]}'", returnStatus: true
                            ) == 0
                            echo "Matches1: $matches1 for Service: ${list[i]}"
                        }
                     
                                     // condition for version 2 services
                        else if ("${list[i]}"=="service4v2" || "${list[i]}"=="service9v2"){
                            redir1 = "${list[i]}".toString().replaceAll("v2","")
                            matches2 = sh (
                                script: "git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$2,\$3'} | uniq | grep -x '${redir1} v2'", returnStatus: true
                            ) == 0
                            echo "Matches2: $matches2 for Service: ${list[i]} "
                        }
                        
                         else if ("${list[i]}"=="service4" || "${list[i]}"=="service9") {
                            matches3 = sh (
                                script: "(git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$2,\$3'} | uniq | grep -x '${list[i]} api')", returnStatus: true
                            ) == 0
                            echo "Matches3: $matches3 for Service: ${list[i]}"
                        }

                        else if ("${list[i]}"=="service6listener") {
                            redir2 = "${list[i]}".toString().replaceAll("listener","")
                            matches4 = sh (
                                script: "git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$1,\$2'} | uniq | grep -x 'listeners ${redir2}'", returnStatus: true
                            ) == 0
                            echo "Matches4: $matches4 for Listener: ${list[i]}"
                        }


                        else if ("${list[i]}"=="service6") {
                            matches5 = sh (
                                script: "git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$1,\$2'} | uniq | grep -x 'services ${list[i]}'", returnStatus: true
                            ) == 0
                            echo "Matches5: $matches5 for Service: ${list[i]}"
                        }

                        else if ("${list[i]}"=="servive8") {
                            matches6 = sh (
                                script: "git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$3'} | uniq | grep -x '${list[i]}'", returnStatus: true
                            ) == 0
                            echo "Matches6: $matches6 for dotnet Service: ${list[i]}"
                        }


                        else if ("${list[i]}"=="service7") {
                            matches7 = sh (
                                script: "git diff --name-only HEAD~1 | sort -u | awk -F/ {'print \$2'} | uniq | grep -x '${list[i]}'", returnStatus: true
                            ) == 0
                            echo "Matches7: $matches7 for Service: ${list[i]}"
                        }

                    }
                    
                    stage('Build Microserices'){
                        
                        // failFast true   
                     parallel { 
                     
                            stage ('check condition 1'){
                                
                                steps{
                                if("$matches1"!="false"){
                                stage(list[i] + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script {
                                            dir ("services/${list[i]}") {
                                                sh "docker build -f build/Dockerfile ."
                                            }
                                        }    
                                    }
                                }
                            }
                                }
 
                               }
                               
                            stage ('check condition 2'){
                                steps{
                            if("$matches2"!="false"){
                                stage(list[i] + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script{
                                            dir ("services/${redir1}/v2"){
                                                sh "docker build -f build/Dockerfile ."
                                            }    
                                        }
                                    }
                                }
                            }

                                }
 
                            }
                            
                            stage ('check condition 3'){
                                
                                steps{
                        // condition for version 1 services (which contains v2)
                            if("$matches3"!="false"){
                                stage(list[i] + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script {
                                            dir ("services/${list[i]}") {
                                                sh "docker build -f build/Dockerfile ."
                                            }    
                                        }
                                    }
                                }
                            }  
                                }
 
                            }
                            stage ('check condition 4'){
                                
                                steps{
                        // condition for listeners
                            if("$matches4"!="false"){
                                stage(redir2 + ' listener' + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script {
                                            dir ("listeners/${redir2}") {
                                                sh "docker build -f build/Dockerfile ."
                                            }
                                        }    
                                    }
                                }
                            }  
                                }
 
                            } 
                            stage ('check condition 5'){
                                
                            steps{
                             // condition for services                        
                            if("$matches5"!="false"){
                                stage(list[i] + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script {
                                            dir ("services/${list[i]}") {
                                                sh "docker build -f build/Dockerfile ."
                                            }    
                                        }
                                    }
                                }
                            } 
                                }
                                
                            }  
                            stage ('check condition 6'){
                                
                                steps{
                        // condition for dotnet services        
                            if("$matches6"!="false"){
                                stage(list[i] + ' dotnet' + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script {
                                            dir ("dotnet/services/${list[i]}") {
                                                sh "dotnet clean"
                                                sh "dotnet build"
                                            }
                                        }
                                        script {
                                            dir ("dotnet/tests/CalculatedDepthSeriesTests") {
                                            sh "dotnet test --no-restore"
                                            }       
                                        }
                                        script{
                                            dir ("dotnet") {
                                                sh " docker build -f ./services/${list[i]}/build/Dockerfile ."
                                            }
                                        }   
                                    }
                                }
                            } 
                                }
 
                            } 
                            stage ('check condition 7'){
                                
                                steps{
                        // condition for gometaserver
                            if("$matches7"!="false"){
                                stage(list[i] + ' build'){
                                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE'){
                                        script {
                                            dir ("tools/${list[i]}") {
                                            sh "docker build -f Dockerfile ."
                                            }
                                        }  
                                    }
                                }
                            }  
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
    }
}