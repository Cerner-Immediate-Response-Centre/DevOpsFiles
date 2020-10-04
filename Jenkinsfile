node
{
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
    echo "Build number is : ${env.BUILD_NUMBER}"
    def mavenHome = tool name:"maven3.6.3"
    stage('CheckoutCode')
    {
        git branch: 'development', credentialsId: '9b2e5e2e-e0aa-4285-9600-05182d085d36', url: 'https://github.com/Cerner-Immediate-Response-Centre/maven-web-application.git'
    }
    
    stage('Build')
    {
        sh "${mavenHome}/bin/mvn clean package"
    }
    
    stage('ExecuteSonarQubeReport')
    {
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }
    
    stage('UploadArtifactIntoNexus')
    {
        sh "${mavenHome}/bin/mvn deploy"
    }
    
    stage('DeployAppIntoTomcatServer')
    {
        sshagent(['TomcatLinuxServerCredentials']) 
        {   
            sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.210.55.119:/opt/apache-tomcat-9.0.38/webapps"
        }
    }
    
    stage('EmailNotification')
    {
        mail bcc: '', body: '''Build is finished.
        Regards,
        Ramya''', cc: '', from: '', replyTo: '', subject: 'Build is done ${env.BUILD_NUMBER}', to: 'ramyaprabha42@gmail.com,gudipaatisrawan@gmail.com'
    }
}
