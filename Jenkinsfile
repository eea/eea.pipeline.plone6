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
            sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-ploneback" -e GIT_BRANCH="master" -e GIT_NAME="plone-backend" -e DOCKERHUB_REPO="eeacms/plone-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS"  -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DEPENDENT_DOCKERFILE_URL="eea/eea-website-backend/blob/master/Dockerfile eea/advisory-board-backend/blob/master/Dockerfile eea/fise-backend/blob/master/Dockerfile eea/insitu-backend/blob/master/Dockerfile eea/clms-backend/blob/master/Dockerfile eea/ied-backend/blob/master/Dockerfile eea/freshwater-backend/blob/master/Dockerfile" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
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

    stage('FISE-BACKEND') {
    stages {
      stage('Build & Tests - FISE-BACKEND - new PLONE-BACKEND release') {
        steps {
          build job: '../fise-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
        }
      }
      stage('Release - FISE-BACKEND') {
        steps {
          node(label: 'docker') {
            withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'fise-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
              sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-fise-back" -e GIT_BRANCH="master" -e GIT_NAME="fise-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/fise-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
            }
          }
        }
      }
    }}        
      

    stage('INSITU-BACKEND') {
    stages {
      stage('Build & Tests - INSITU-BACKEND - new PLONE-BACKEND release') {
        steps {
          build job: '../insitu-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
        }
      }
      stage('Release - INSITU-BACKEND') {
        steps {
          node(label: 'docker') {
            withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'insitu-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
              sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-insitu-back" -e GIT_BRANCH="master" -e GIT_NAME="insitu-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/insitu-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
            }
          }
        }
      }
    }}        

      
    stage('CLMS-BACKEND') {
    stages {
      stage('Build & Tests - CLMS-BACKEND - new PLONE-BACKEND release') {
        steps {
          build job: '../clms-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
        }
      }
      stage('Release - CLMS-BACKEND') {
        steps {
          node(label: 'docker') {
            withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'clms-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
              sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-clms-back" -e GIT_BRANCH="master" -e GIT_NAME="clms-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/clms-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
            }
          }
        }
      }
    }}       
      
    stage('IED-BACKEND') {
    stages {
      stage('Build & Tests - IED-BACKEND - new PLONE-BACKEND release') {
        steps {
          build job: '../ied-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
        }
      }
      stage('Release - IED-BACKEND') {
        steps {
          node(label: 'docker') {
            withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'ied-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
              sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-ied-back" -e GIT_BRANCH="master" -e GIT_NAME="ied-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/ied-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
            }
          }
        }
      }
    }}
      
    stage('FRESHWATER-BACKEND') {
    stages {
      stage('Build & Tests - FRESHWATER-BACKEND - new PLONE-BACKEND release') {
        steps {
          build job: '../freshwater-backend/master', parameters: [[$class: 'StringParameterValue', name: 'TARGET_BRANCH', value: 'master']]
        }
      }
      stage('Release - FRESHWATER-BACKEND') {
        steps {
          node(label: 'docker') {
            withCredentials([string(credentialsId: 'eea-jenkins-token', variable: 'GITHUB_TOKEN'), string(credentialsId: 'freshwater-backend-trigger', variable: 'TRIGGER_MAIN_URL'),usernamePassword(credentialsId: 'jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
              sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG-nightly-freshwater-back" -e GIT_BRANCH="master" -e GIT_NAME="freshwater-backend" -e EXTRACT_VERSION_SH="calculate_next_release.sh" -e GIT_TOKEN="$GITHUB_TOKEN" -e TRIGGER_MAIN_URL="$TRIGGER_MAIN_URL" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DOCKERHUB_REPO="eeacms/freshwater-backend" -e DEPENDENT_DOCKERFILE_URL=""  -e TRIGGER_RELEASE="" -e GITFLOW_BEHAVIOR="TAG_ONLY" eeacms/gitflow'''
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
