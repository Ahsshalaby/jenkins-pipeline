pipeline {
    agent any

    environment {
	DOCKER_IMAGE = "ahsshalaby/hw2app"
	K8S_NAMESPACE = "default"
        KUBECONFIG_CRED_ID = 'kubeconfig-id'
	DOCKER_CREDENTIALS_ID = 'dockerhub'
    }

    stages {
     



	stage('Build Application'){
	steps {
	
	script{
			sh 'npm install'
			sh 'npm run build'
		}
	       }
	}





 stage('Build Docker image'){
        steps {

        script{
                       docker.build(DOCKER_IMAGE)
                }
               }
        }

 stage('Push Docker image'){
        steps {

        script{
                       docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE}").push('latest')
                }
               }
        }
 }



        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: KUBECONFIG_CRED_ID]) {
                        sh '''
                        kubectl set image deployment/hw2app-deployment hw2app=${DOCKER_IMAGE}:latest -n ${K8S_NAMESPACE}
                        kubectl rollout status deployment/hw2app-deployment -n ${K8S_NAMESPACE}
                        '''
                }
            }
        }
    }
    


    post {
        always {
            echo 'Cleaning up...'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
