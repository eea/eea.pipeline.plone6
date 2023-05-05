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
            sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-ploneback" -e GIT_BRANCH="master" -e GIT_NAME="plone-backend" -e DOCKERHUB_REPO="eeacms/plone-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS"  -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DEPENDENT_DOCKERFILE_URL="eea/eea-website-backend/blob/master/Dockerfile eea/advisory-board-backend/blob/master/Dockerfile eea/forest-backend/blob/master/Dockerfile" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
          }
        }
      }
    }

    
    stage('Build & Tests & release dependents') {
    parallel {
    
    stage('EEA-WEBSITE-BACKEND') {
    stages {  
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
    }}  
      
    stage('ADVISORY-BOARD-BACKEND') {
    stages {
      stage('Build & Tests - ADVISORY-BOARD-BACKEND - new PLONE-BACKEND release') {
        steps {
          build job: '../advisory-board-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
        }
      }
      stage('Release - ADVISORY-BOARD-BACKEND') {
        steps {
          node(label: 'docker') {
            withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'advisory-board-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
              sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-advisory-back" -e GIT_BRANCH="master" -e GIT_NAME="advisory-board-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/advisory-board-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
            }
          }
        }
      }
    }}  

    stage('FOREST-BACKEND') {
    stages {
      stage('Build & Tests - FOREST-BACKEND - new PLONE-BACKEND release') {
        steps {
          build job: '../forest-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
        }
      }
      stage('Release - FOREST-BACKEND') {
        steps {
          node(label: 'docker') {
            withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'forest-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
              sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-forest-back" -e GIT_BRANCH="master" -e GIT_NAME="forest-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/forest-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
            }
          }
        }
      }
    }}        
      

      
    }
    }
    
    
      
      
  }


  post {
    always {
      cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
      script {
        def details = """<h1>${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - ${currentBuild.currentResult}</h1>
                         <p>Check console output at <a href="${env.BUILD_URL}/display/redirect">${env.JOB_BASE_NAME} - #${env.BUILD_NUMBER}</a></p>
                      """
        emailext(
        subject: '$DEFAULT_SUBJECT',
        body: details,
        attachLog: true,
        compressLog: true,
        to: '$DEFAULT_RECIPIENTS'
        )
      }
    }
  }
}
