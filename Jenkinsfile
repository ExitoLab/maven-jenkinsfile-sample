pipeline {
    agent any

    tools {
        maven "maven"
    }

    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "127.0.0.1:8082"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "hello-world"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "nexus_admin"
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
                git url: 'https://github.com/ExitoLab/maven-jenkinsfile-sample.git', branch: 'main'
            }
        }

        stage('Maven: Test') {
            steps {
                dir("my-app") {
                    sh 'mvn -e --batch-mode test -Dmaven.test.skip=true -Dmaven.test.failure.ignore=true dependency:resolve-plugins dependency:resolve'
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }

        stage('Sonar: Scan') {
          steps {
            dir("my-app") {
                withSonarQubeEnv(installationName: 'sonar-server', credentialsId: 'sonarqube') {
                sh 'mvn sonar:sonar'
                }
            }
          }
        }

        stage('Build') {
            steps {
                dir("my-app") {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }
        
        stage('Directory') {
            steps {
                dir("my-app") {
                    sh 'ls -la'
                    sh "ls -la target/"
                }
            }
        }
        
        stage("publish to nexus") {
            steps {
                script {
                    dir("my-app") {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.*");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;

                    

                    sh "mvn --batch-mode deploy  -DskipTests"
                } }
            }
        }


     }
//     post {
//        always {
//           junit(
//         allowEmptyResults: true,
//         testResults: '*/test-reports/.xml'
//       )
//       }
//    }

}
