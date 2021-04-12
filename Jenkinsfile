node {
    try {
        notifyBuild('STARTED')

        stage('Prepare code') {
            checkout scm: [$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[credentialsId: 'test_repo_private', url: 'git@github.com:dangthang1998/prj_chat.git']]] 
        }

        stage('Testing') {
            echo 'Testing'
            echo 'Testing - publish coverage results'
        }

        stage('Staging') {
            echo 'Deploy Stage'
        }
        // @todo add checkpoint
        stage('Deploy') {
            echo 'Deploy - Backend'
            echo 'Deploy - Frontend'
            script {
                sh """ssh deployer@13.250.7.34 -i /home/jenkins/web-server.pem << EOF 
                cd /var/www/html/prj_chat/source/
                git pull origin master
                exit
                EOF"""
            }
        }
  } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'
  buildMessage = ''

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'


  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'GRAY'
    colorCode = '#ccc'
    buildMessage = 'Deploy Start'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#0097a7'
    buildMessage = 'Deploy Success'
  } else {
    color = 'RED'
    colorCode = '#b50003'
    buildMessage = 'Deploy Failed'
  }

  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"
  def details = """Deploy 
   :ghost: ${buildMessage}"""

  // Send notifications
  slackSend (channel: '#chat_dev', color: colorCode, message: details)
}