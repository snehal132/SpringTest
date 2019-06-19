//#!groovy

properties(
    [
        [$class: 'BuildDiscarderProperty', strategy:
          [$class: 'LogRotator', artifactDaysToKeepStr: '14', artifactNumToKeepStr: '5', daysToKeepStr: '30', numToKeepStr: '60']],
        pipelineTriggers(
          [
              pollSCM('H/15 * * * *'),
              cron('@daily'),
          ]
        )
    ]
)

node {
    stage('Checkout') {
        echo 'Fetching latest code from GIT'
        //deleteDir()
        //git credentialsId: 'Samrat GitHub', url: 'https://github.com/snehal132/SpringTest.git'
        checkout scm
    }
    stage('Compile') {
        echo 'Compiling Code'
        bat 'mvn clean install -DskipTests'
    }
    stage('Test') {
        echo 'Testing Junit test cases'
        bat 'mvn test'
    }
    stage('Sonar') {
        echo 'Generating Sonar report'
        bat 'mvn sonar:sonar'
    }
    stage('Archive') {
        echo 'Zip build package for deployment'
        bat 'tar -cvzf tssidemobedist.tar.gz --strip-components=1 target\\spring-rest-junit.war'
        archiveArtifacts 'tssidemobedist.tar.gz'
    }
    stage('Approve') {
         milestone()
         mail bcc: '', body: 'TSSIDemoPL_BE project is built and ready for deployment upon approval.', cc: '', from: '', replyTo: '', subject: 'TSSIDemoPL_BE Build Ready', to: 'samrat.mitra@vodafone.com'
         timeout(time: 1, unit: 'MINUTES') {
            input 'Deploy package ?'
        } 
    }
    stage('Deploy') {
        milestone()
        echo 'Deploying package to tomcat'
        bat 'copy tssidemobedist.tar.gz C:\\apache-tomcat-9.0.20\\webapps\\'
        bat 'tar xvzf C:\\apache-tomcat-9.0.20\\webapps\\tssidemobedist.tar.gz -C C:\\apache-tomcat-9.0.20\\webapps\\'
    }
}