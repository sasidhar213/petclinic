@Library('jenkins-library@master') _
 
pipeline {
    agent any
    stages {
        stage(Notify){
            steps{
                office365ConnectorSend color: 'Green', message: "Started ${env.JOB_NAME} ${env.BUILD_NUMBER}" , status: "Started", webhookUrl: "${env.TeamsWebhook}"
            }
        }
        stage('Git Checkout') {
            steps {
                Gitcheckout(
                    branch: "master",
                    url: "https://dev.azure.com/KrishnaBhimanavarjula/UNUM-TFS/_git/spring-petclinic",
                    credentialid : "778e9c88-7f73-4b2b-890e-2613d538d4c4"
                )
            }
        }
        stage("Build"){
            steps {
                echo 'Clean Build'
                bat 'mvn clean compile'
            }
        }
        stage("Test") {
            steps {
                echo 'Testing'
                bat 'mvn clean test'
            }
        }
        stage("sonarqubeAnalysis"){
            steps{
                script{
                    scan = new Sonarqubescan()
                    scan.java()
                }
            }
        }
        stage('Package') {
            steps {
                echo 'Packaging'
                bat 'mvn clean package -DskipTests'
            }
        }
    }
    post {
        always {
	    /* Use teamNotifier.groovy from shared library and provide current build result as parameter */   
            TeamsNotifier(
                webhook: "${env.TeamsWebhook}"
                )
            cleanWs()
        }
    }
}