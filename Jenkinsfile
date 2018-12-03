pipeline {
    agent any

    tools {
        maven 'localMaven'
        jdk 'localJDK'
    }

    parameters {
        string(name: "tomcat_dev", defaultValue: "localhost:8090", description: 'Staging Server')
        string(name: "tomcat_prod", defaultValue: "localhost:8091", description: 'Prod Server')
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage('Deployments'){
            parallel{
                stage('Deploy to Staging'){
                    steps{
                        sh "cp webapp/target/*.war ${tomcat_dev}/webapp"
                    }
                }
                stage('Deploy to Production'){
                    steps{
                        sh "cp webapp/target/*.war ${tomcat_prod}/webapp"
                    }
                }
            }
        }
        /*stage('Deploy to Staging') {
            steps {
                build job: 'deploy-to-staging'
            }
        }
        stage('Deploy to Production') {
            steps {
                timeout(time:5, unit:'DAYS') {
                    input message:'Approve PRODUCTION Deployment?'
                }

                build job: 'deploy-to-prod'
            }
            post {
                success {
                    echo 'Code deployed to Production.'
                }
                failure {
                    echo 'Deployment failed'
                }
            }
        }*/
    }
}
