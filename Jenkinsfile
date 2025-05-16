pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        // ansiColor('xterm')
    }
    environment {
        def appVersion = ''
        nexusUrl = 'nexus.mydaws.fun:8081'
    }
    stages {
        stage('read the version') {
            steps {
                script {
                    def packegJson = readJSON file: 'package.json'
                    appVersion = packegJson.version
                    echo "application version is $appVersion"
                }
            }
        }
        // stage('install Dependencies') {
        //     steps {
        //         sh """
        //          npm install
        //          ls -ltr
        //          echo "application version is $appVersion"
        //         """
        //         }
        //     } 
        stage('built') {
            steps {
                sh """
                zip -q -r frontend-${appVersion}.zip * -x Jenkinsfile -x frontend-${appVersion}.zip
                ls -ltr
                """
                }
            } 
            stage('Nexus Artifact Upload') {
            steps {
                script{
                     nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appVersion}",
                        repository: "frontend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: "frontend",
                            classifier: '',
                            file: "frontend-" + "${appVersion}" + '.zip',
                            type: 'zip']
                        ]
             )
                }
                }
            } 
            // stage('Deploy') {
            //     steps {

            //         script {
            //             def params = [
            //             string(name: 'appVersion', value: "${appVersion}"),
            //         ]
            //             build job: 'frontend-deploy', parameters: params, wait: false  
            //         }
            //     }
            // } 
        }           
    
        post { 
            always { 
                echo 'I will always say Hello again!'
                deleteDir()
            }
            success { 
                echo 'I will run when pipeline is successful!'
            }
            failure { 
                echo 'I will run when pipeline is failed!'
            }
        }
    }

   
    
