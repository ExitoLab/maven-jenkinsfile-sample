pipeline {
    agent any

    tools {
        maven "maven"
        jdk "jdk"
    }

    stages {
        stage('Initialize'){
            steps{
                echo "PATH = ${M2_HOME}/bin:${PATH}"
                echo "M2_HOME = /opt/maven"
            }
        }

        stage('Checkout project') {
            steps {
                git url: 'https://github.com/ExitoLab/devops-mentee-pratice.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                dir("/var/lib/jenkins/workspace/New_demo/my-app/") {
                sh 'mvn -B -DskipTests clean package'
                }
            
            }
        }
     }
    post {
       always {
          junit(
        allowEmptyResults: true,
        testResults: '*/test-reports/.xml'
      )
      }
   } 
}
