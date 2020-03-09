pipeline {
  parameters {
    choice(name: 'PLATFORM_FILTER', choices:['all', 'linux', 'windows', 'mac'], description: 'build for platform')
    choice(name: 'ARCHITECTURE_FILTER', choices:['all', 'any cpu', 'x86', 'x64', 'arm', 'arm64'], description: 'build for architecture')
  }
  agent none
  environment {
    SOLUTION_TO_BUILD = "winusb-adb-driver.sln"
    GITHUB_ACCESS_KEY_ID = credentials('jenkins-github-secret-key-id')
    GITHUB_SECRET_ACCESS_KEY = credentials('jenkins-github-secret-access-key')
  }
  options {
    skipStagesAfterUnstable()
    retry(3)
    timeout(time 30: unit:'MINUTES')
  }
  stages {
    stage('Build-Test-Package') {
      when { changeRequest target: anyof {'master', 'develop', 'release'} }
      matrix {
      agent { label "agent-${PLATFORM}" }
        when { anyof {
          expression { params.PLATFORM_FILTER == 'all' }
          expression { params.PLATFORM_FILTER == env.PLATFORM }
        } }
        axes {
          axis {
            name 'PLATFORM'
            values 'linux', 'windows', 'mac'
          }
          axis {
            name 'ARCHITECTURE'
            values 'Any CPU','x86','x64','arm','arm64'
          }
          excludes {
            exclude {
              axis {
                name 'PLATFORM'
                value 'mac'
              }
              axis {
                name 'ARCHITECTURE'
                value 'x86'
              }
            }
          }
          stages{
            stage('Clone') {
              echo 'Getting sources files to build for ${PLATFORM} - ${ARCHITECTURE}...'

              checkout scm

              echo 'Got all source files for ${PLATFORM} - ${ARCHITECTURE}.'
            }
            stage('Build'){
              steps {
                echo 'Building for ${PLATFORM} - ${ARCHITECTURE}...'

                bat 'nuget restore ${SOLUTION_TO_BUILD}'
                bat "\"${tool 'MSBuild'}\" ${SOLUTION_TO_BUILD} /p:Configuration=Release /p:Platform=\"${PLATFORM}\""

                echo 'Built for ${PLATFORM} - ${ARCHITECTURE}.'
              }
            }
            stage('Test'){
              steps {
                echo 'Testing for ${PLATFORM} - ${ARCHITECTURE}...'
                echo 'Tested for ${PLATFORM} - ${ARCHITECTURE}...'
              }
            }
            stage('Package'){
              steps {
                echo 'Packaging for ${PLATFORM} - ${ARCHITECTURE}...'
                echo 'Packaged for ${PLATFORM} - ${ARCHITECTURE}...'
              }
            }
          }
        }
      }
    }
    stage('Upload') {
      when {

      }
      echo 'Uploading artefacts for ${PLATFORM}- ${ARCHITECTURE}...'
      echo 'Uploaded artefacts for ${PLATFORM}- ${ARCHITECTURE}...'
    }
  }
  post {
    always {
      echo 'Completed pipeline for ${PLATFORM} - ${ARCHITECTURE}.'
    }
    fixed {
      echo 'A failure in the previous execution of the pipeline has been fixed now.'
    }
    regression {
      echo 'Some stage of the pipeline failed.'
    }
  }
}
