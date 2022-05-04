node{
    
echo "job name is:  ${env.JOB_NAME}"
echo "Node name is:  ${env.NODE_NAME}"
   
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

def mavenHome = tool name: "maven3.8.4"
    
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
