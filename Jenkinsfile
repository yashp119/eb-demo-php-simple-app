pipeline {
    agent any

    environment {
        BuildName = "version-${BUILD_NUMBER}"
        BucketName = "php-bucket11"
        ApplicationName = "php-machine"
        EnvironmentName = "Php-machine-env"
        Region = "us-east-1"  // Update with your desired AWS region
    }

    stages {
        stage('Checkout') {
            steps {
                // Assuming you are using Git for version control
                checkout scm
            }
        }

        stage('Upload to S3') {
            steps {
                script {
                    sh "aws s3 sync . s3://${BucketName}/ --region ${Region}"
                }
            }
        }

        stage('Create Beanstalk Application Version') {
            steps {
                script {
                    sh "aws elasticbeanstalk create-application-version --application-name '${ApplicationName}' --version-label '${BuildName}' --description 'Build created from JENKINS. Job:${JOB_NAME}, BuildId:${BUILD_DISPLAY_NAME}, GitCommit:${GIT_COMMIT}, GitBranch:${GIT_BRANCH}' --source-bundle S3Bucket=${BucketName},S3Key=${BuildName}.zip --region ${Region}"
                }
            }
        }

        stage('Update Beanstalk Environment') {
            steps {
                script {
                    sh "aws elasticbeanstalk update-environment --environment-name '${EnvironmentName}' --version-label '${BuildName}' --region ${Region}"
                }
            }
        }
    }
}
