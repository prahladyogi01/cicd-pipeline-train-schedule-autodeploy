pipeline {

    agent { label 'sl2' }
	

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
				git 'https://github.com/prahladyogi01/cicd-pipeline-train-schedule-autodeploy.git'
            }
        }
        stage('Build Docker Image') {
            steps {
				sh 'sudo docker version'
				sh "sudo docker build -t prahladyogi/cicdpipeline:${BUILD_NUMBER} ."
				sh 'sudo docker image list'
				sh "sudo docker tag prahladyogi/cicdpipeline:${BUILD_NUMBER} prahladyogi/cicdpipeline:latest"
            }
        }
        stage('Approve - push Image to dockerhub'){
            steps{
                
                //----------------send an approval prompt-------------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required Choose "yes" | "Abort"'
                       }
                //-----------------end approval prompt------------
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "sudo docker push prahladyogi/cicdpipeline:latest"
			}
		}

        stage('Approve - Deploy to Kubernetes'){
            steps{
                
                //----------------send an approval prompt-------------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required Choose "yes" | "Abort"'
                       }
                //-----------------end approval prompt------------
            }
        }
        stage('Deploy Kubernetes') {
            steps {
                script {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'k8master', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'sudo kubectl apply  -f train-schedule-kube.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/home/ubuntu', remoteDirectorySDF: false, removePrefix: '.', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }   
            }
        }



 
    }
}
