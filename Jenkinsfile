pipeline {
    agent any

    tools {
        maven 'localMaven'
        jdk 'localJDK'
    }

    parameters {
        string(name: "tomcat_dev", defaultValue: "localhost:8090", description: 'Staging Server')
        string(name: "tomcat_prod", defaultValue: "localhost:8091", description: 'Prod Server')
        //string(name: "tomcat_dev", defaultValue: "/usr/local/apache-tomcat-9.0.12-staging", description: 'Staging Server')
        //string(name: "tomcat_prod", defaultValue: "/usr/local/apache-tomcat-9.0.12-prod", description: 'Prod Server')
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
                        sh "curl http://tomcat:tomcat@${params.tomcat_dev}/manager/text/undeploy?path=/webapp&update=true"
                        sh "curl --upload-file webapp/target/*.war http://tomcat:tomcat@${params.tomcat_dev}/manager/text/deploy?path=/webapp&update=true"
                        //sh "sudo cp webapp/target/*.war ${params.tomcat_dev}/webapps"
                    }
                }
                stage('Deploy to Production'){
                    steps{
                        sh "curl http://tomcat:tomcat@${params.tomcat_prod}/manager/text/undeploy?path=/webapp&update=true"
                        sh "curl --upload-file webapp/target/*.war http://tomcat:tomcat@${params.tomcat_prod}/manager/text/deploy?path=/webapp&update=true"
                        //sh "sudo cp webapp/target/*.war ${params.tomcat_prod}/webapps"
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
