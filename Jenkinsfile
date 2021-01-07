pipeline {
        agent any
        environment {
            AWS_ACCESS_KEY_ID = credentials('aws-access-secret-id')
            AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
			ARTIFACT_NAME = 'Dockerrun.aws.json'
            AWS_S3_BUCKET = 'solr-artifacts'
			AWS_EB_APP_NAME = 'solr'
			AWS_EB_ENVIRONMENT = 'Solr-env'
			AWS_EB_APP_VERSION = "${BUILD_ID}"
            }

        stages {
            stage('pkr inspect') {
                steps {
                    sh 'packer inspect packer/solr.json'
                }
            }
            stage('pkr validate') {
                steps {
                    sh 'packer validate packer/solr.json'
                }
            }
            stage('pkr build') {
                steps {
                    sh 'packer build packer/solr.json'
                }
            }
            stage('publish') {
                steps {
		    sh 'aws configure set region us-east-1'
                    sh 'aws s3 cp app/Dockerrun.aws.json s3://solr-artifacts/'
                }
            }			
            stage('Deploy') {
                steps {
		    sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
		    sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
                }
            }			

        }
    }
