pipeline {
  agent any
  triggers {
    cron('H 1 * * *')
  }

  stages {

    stage('Build & Tests - PLONE-BACKEND') {
      steps {
        build job: '../plone-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
      }
    }

    stage('Release - PLONE-BACKEND') {
      steps {
        node(label: 'docker') {
          withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'),  string(credentialsId: 'plone-backend-trigger', variable: 'TRIGGER_MAIN_URL'), usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
           sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-ploneback" -e GIT_BRANCH="master" -e GIT_NAME="plone-backend" -e DOCKERHUB_REPO="eeacms/plone-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS"  -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DEPENDENT_DOCKERFILE_URL="eea/eea-website-backend/blob/master/Dockerfile" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
         }
       }
     }
   }

    stage('Build & Tests - EEA-WEBSITE-BACKEND - new PLONE-BACKEND release') {
      steps {
        build job: '../eea-website-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
      }
    }

    stage('Release - EEA-WEBSITE-BACKEND') {
      steps {
        node(label: 'docker') {
          withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'eea-website-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
           sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-www-back" -e GIT_BRANCH="master" -e GIT_NAME="eea-website-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/eea-website-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
         }
       }
     }
   }

  }

  post {
    always {
      script {
        def url = "${env.BUILD_URL}/display/redirect"
        def status = currentBuild.currentResult
        def subject = "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
        def summary = "${subject} (${url})"
        def details = """<h1>${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - ${status}</h1>
                         <p>Check console output at <a href="${url}">${env.JOB_BASE_NAME} - #${env.BUILD_NUMBER}</a></p>
                      """

        def color = '#FFFF00'
        if (status == 'SUCCESS') {
          color = '#00FF00'
        } else if (status == 'FAILURE') {
          color = '#FF0000'
        }
        emailext (subject: '$DEFAULT_SUBJECT', to: '$DEFAULT_RECIPIENTS', body: details)
      }
    }
  }
}
