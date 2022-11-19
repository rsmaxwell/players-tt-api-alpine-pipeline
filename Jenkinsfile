pipeline {
  agent {
    kubernetes {
      yamlFile 'KubernetesPod.yaml'
    }
  }
  stages {

    stage('prepare') {
      steps {
        container('tools') {
          dir('project') {
            echo 'preparing the application'
            checkout([
              $class: 'GitSCM', 
              branches: [[name: '*/main']], 
              extensions: [], 
              userRemoteConfigs: [[url: 'https://github.com/rsmaxwell/players-tt-api']]
            ])
            sh('./scripts/prepare.sh')
          }
        }
      }
    }

    stage('build') {
      steps {
        container('golang-alpine') {
          dir('project') {

            echo '/etc/os-release:'
            sh('cat /etc/os-release')

            echo 'building the application'
            sh('./scripts/build.sh')

            echo 'testing the application (alpine)'
            sh('./scripts/test.sh')
          }
        }
      }
    }

#     stage('test') {
#       steps {
#         container('tools') {
#           dir('project') {
#             echo 'testing the application (ubuntu)'
#             sh('./scripts/test.sh')
#           }
#         }
#       }
#     }

    stage('package') {
      steps {
        container('tools') {
          dir('project') {
            echo 'packaging the application'
            sh('./scripts/package.sh x86_64-alpine')
          }
        }
      }
    }

    stage('deploy') {
      steps {
        container('maven') {
          dir('project') {
            echo 'deploying the application'
            sh('./scripts/deploy.sh x86_64-alpine')
          }
        }
      }
    }
  }
}
