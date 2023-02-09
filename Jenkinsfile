pipeline{

    agent any
    environment {
        PATH = "/opt/maven3.6/bin/:$PATH"
    }

    stages {

        stage('Git Checkout'){

            steps{

                script{

                    git branch: 'master', url: 'https://github.com/buddipammu2616/buddipammufeb9.git'
                }
            }
        }
        stage('UNIT testing'){

            steps{

                script{

                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){

            steps{

                script{

                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('build code') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('sonarqube analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-key') {
                      sh 'mvn clean package sonar:sonar'
}
    }
            }
        }
        stage('quality gate check') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-key'
                }
            }
        }
        stage ('nexus artifact') {
            steps {
                script {
                    def readpomversion = readMavenPom file: 'pom.xml'
                    def nexusrepo = readpomversion.version.endsWith("SNAPSHOT") ? "helloworldapp-snapshot" : "helloworldapp-release"
                    nexusArtifactUploader artifacts: 
                        [
                            [artifactId: 'buddipammu', 
                             classifier: '',
                             file: 'target/buddipammu.war',
                             type: 'war'
                            ]
                        ], 
                        credentialsId: 'nexus-credentials', 
                        groupId: 'com.ustglobal', 
                        nexusUrl: '13.127.52.62:8081', 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: nexusrepo, 
                        version: "${readpomversion.version}"
                }
            }
        }
    }
}
