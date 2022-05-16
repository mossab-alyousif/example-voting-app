pipeline {
    agent any

    tools {
        maven 'Maven 3.8.5' 
    }

    stages {
        stage('build') {

            when{
                changeset '**/worker/**'
            }
            steps {
                echo 'building...'
                dir('worker'){
                    sh 'mvn compile'
                }
                
            }

            // post {
            //     // If Maven was able to run the tests, even if some of the test
            //     // failed, record the test results and archive the jar file.
            //     success {
            //         junit '**/target/surefire-reports/TEST-*.xml'
            //         archiveArtifacts 'target/*.jar'
            //     }
            // }
        }
         stage('test') {
            when{
                changeset '**/worker/**'
            }
            steps {
                echo 'testing...'
                dir('worker'){
                    sh 'mvn clean test'
                }
            }
        }
        stage('package') {
            when{
                branch 'master'
                changeset '**/worker/**'
            }
            steps {
                echo 'package'
                dir('worker'){
                    sh 'mvn package -DskipTests'
                }
            }
        }
            stage('docker-package'){
            steps{
              echo 'Packaging worker app with docker'
              script{
                docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                    def workerImage = docker.build("initcron/worker:v${env.BUILD_ID}", "./worker")
                    workerImage.push()
                    workerImage.push("latest")
                }
              }
            }
        }
               
    post{
        always{
            echo  'post always'
        }
    }
}
}
