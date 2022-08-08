pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('Saeed-aws-secret-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('saeed-aws-secret-key-id')
        ARTIFACT_NAME = 'artifact-sda.jar'
        AWS_S3_BUCKET = 'sda-learning-jenkins'
        AWS_EB_APP_NAME = 'Saeed-sample-application'
        AWS_EB_ENVIRONMENT = 'Saeedsampleapplication-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
    } 
    stages {

        
        
         stage('Quality Scan') {
            steps {
               
            sh "mvn -Dmaven.test.failure.ignore=true clean compile"

            sh "mvn clean verify sonar:sonar \
  -Dsonar.projectKey=Onsite-Saeed_Alghamdi-B2D2 \
  -Dsonar.host.url=http://52.23.193.18 \
  -Dsonar.login=sqp_51bf35e07572a01c166a857e75696f47b7a2abe6"
               
      
               
            }

            
        }
        
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/skag07/BeltExam2-day2-jenkins.git'

                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true package"

                
            }
            
             post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
                success {
                    
                    archiveArtifacts 'target/*.jar'
                }
            }
            
        }
        
       stage('Publish') {
            steps {
                sh 'aws configure set region us-east-1'
                sh 'aws s3 cp ./target/spring-petclinic-2.3.1.BUILD-SNAPSHOT.jar s3://sda-learning-jenkins/artifact-sda.jar'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
            post {
                success {
                    echo 'success Jenkinsfile'
                    // bat 'aws configure set region us-east-1'
                    // bat 'aws s3 cp ./target/calculator-0.0.1-SNAPSHOT.jar s3://sda-learning-jenkins/calculator.jar'
                }
            }
        }
    }
}