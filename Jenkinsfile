pipeline {

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('osaid-dockerhub-token')
		AWS_ACCESS_KEY_ID     = credentials('osaid-aws-secret-key-id')
  		AWS_SECRET_ACCESS_KEY = credentials('osaid-aws-secret-access-key')
		ARTIFACT_NAME = 'Dockerrun.aws.json'
		AWS_S3_BUCKET = '	osaid-s3-upload-123'
		AWS_EB_APP_NAME = 'osaid-Belt2-Artifacts-123'
        AWS_EB_ENVIRONMENT_NAME = 'Osaidbelt2artifacts123-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
	}

	stages {

		stage('Build') {

			steps {
				sh 'docker build -t oalmaymani/runaway:latest .'
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push oalmaymani/runaway'
			}
		}

        stage('Deploy') {
            steps {
                sh 'aws configure set region us-east-1	'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
    }
	post {
		always {
			sh 'docker logout'
		}
	}

}