def sendSlackNotifications(String buildStatus = 'STARTED') {
  
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

node{
    
echo "job name is:  ${env.JOB_NAME}"
echo "Node name is:  ${env.NODE_NAME}"
   
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

def mavenHome = tool name: "maven3.8.4"
    
    try{
      sendSlackNotification('STARTED')
      
    stage('CheckoutCode'){
git branch: 'development', url: 'https://github.com/rd-dharaneesh/maven-web-application.git'
}

stage('Build'){
    sh "${mavenHome}/bin/mvn clean package"
}

stage('ExecuteSonarQubeReport'){
    sh "${mavenHome}/bin/mvn sonar:sonar"
}

stage('upload'){
    sh "${mavenHome}/bin/mvn deploy"
}

stage('deploymentintomcatserver'){
    sshagent(['6d5cd2e5-8bde-4727-ae0f-d9edbe172696']) {
    sh "scp -o  StrictHostKeyChecking=no target/maven-web-application.war ec2-user@3.111.246.190:/opt/apache-tomcat-9.0.62/webapps/"
}
}
}
    catch(e){
        currentBuild.result = "FAILED"
    }
    finally{
    sendSlackNotifications(currentBuild.result)
    }

}
