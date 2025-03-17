pipeline {
    agent any
     environment { 
        CC = 'clang'
        dockerpass = credentials('docker-hub-password')
        dockeruserpass = credentials('docker-user-pass')
    }

    parameters { 
        string(name: 'DEPLOY_TO', defaultValue: 'staging', description: '') 
        choice(name: 'CHOICES', choices: ['one', 'two', 'three'], description: '')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))  
        timestamps()
    }

    triggers { 
        upstream(upstreamProjects: 'job1,job2', threshold: hudson.model.Result.SUCCESS)
    }
    stages {
        stage('Parallel Stage') {
            parallel {
                stage('first stage') {
                
                    steps {
                        script {
                            sh "sleep 120"
                            sh "hostname"
                            sh "java -version"
                            
                        }
                    }
                }

                stage('second stage') {
                    agent {
                        docker {
                            image 'openjdk:19-alpine'
                        }
                    }

                    environment { 
                        project = 'jenkins-demo'
                    }
                    steps {
                        script {
                                    sh "sleep 120"
                                    sh "java -version"
                                    sh "printenv"
                        
                        }
                    }
                }
            }  
        }


        stage('third stage') {
            agent {
                dockerfile {
                    filename 'Dockerfile'
                }
            }
            steps {
                script {
                    sh "hostname"
                    sh "echo jagadeesh"
                     sh "printenv"
                }
            }
        }

        stage('fourth stage') {
            when { 

                environment name: 'DEPLOY_TO', value: 'production' 

            }
             steps {
                script {
                    sh "echo $BUILD_NUMBER > test.txt"
                    sh "echo $BUILD_ID >> test.txt"
                                  }
            }
            post {
                    success {
                        archiveArtifacts artifacts: 'test.txt', followSymlinks: false
                    }
            }  
        }
    }   
    post {
        always {
            cleanWs()
        }
    }
}
