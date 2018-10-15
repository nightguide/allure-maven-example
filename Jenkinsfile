#!groovy
  
pipeline {
  agent { label 'jenkins-slave' }
  stages {
    stage('Maven Install') {
      agent {
        docker {
          image 'maven:3.5.0'
		  reuseNode true
        }
      }
      
      steps {
        //Send to Slack notify
        slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        //Build
        sh 'mvn clean test'
	
	
      }   
    }
    
     
     }
post {
    success {
      slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      allure includeProperties: false, jdk: '', results: [[path: 'first-module/target/allure-results'], [path: 'second-module/target/allure-results']] 
      pangolinTestRail( [testRailProject: 'Test Project', configs: [[testPath: 'Master\\Section1\\Section2', failIfUploadFailed: false, format: 'junit', resultPattern: '**/first-module/target/surefire-reports/*.xml', testRun: 'Maven Example']],])
      testRail(testrailProject: 'Test Project', testrailSuite: '', 
         junitResultsGlob: 'second-module/target/allure-results', createNewTestcases: 'true'])
    }

    failure {
      slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      allure includeProperties: false, jdk: '', results: [[path: 'first-module/target/allure-results'], [path: 'second-module/target/allure-results']] 	
      pangolinTestRail( [testRailProject: 'Test Project', configs: [[testPath: 'Master\\Section1\\Section2', failIfUploadFailed: false, format: 'junit', resultPattern: '**/first-module/target/surefire-reports/*.xml', testRun: 'Maven Example']],])

    }     
 }
}

