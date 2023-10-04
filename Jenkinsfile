pipeline {
  agent none
  stages {
    stage('build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('test') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'test maven app'
        sh 'mvn clean test'
      }
    }
    stage('Parallel Stage') {
      parallel{
        stage('package') {
          agent {
            docker {
              image 'maven:3.6.3-jdk-11-slim'
            }
          }
          when {
            branch 'master'
          }
          steps {
            echo 'package maven app'
            sh 'mvn package -DskipTests'
            archiveArtifacts 'target/*.war'
          }
        }
        stage('Docker BnP') {
          agent any
          when {
            branch 'master'
          }
          steps {
            script {
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                def dockerImage = docker.build("kbrdev/sysfoo:v${env.BUILD_ID}", "./")
                dockerImage.push()
                dockerImage.push("latest")
                dockerImage.push("dev")
              }
            }
          }
        }
      }
    }

  }
  tools {
    maven 'Maven 3.6.3'
  }
}
