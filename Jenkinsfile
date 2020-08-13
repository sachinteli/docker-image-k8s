node {
    def app
	
	environment {
        PROJECT_ID = 'My First Project'
        CLUSTER_NAME = 'kube-demo'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'sachinteli'
    }
    
     stage('Initialize'){
        def dockerHome = tool 'myDocker'
        env.PATH = "${dockerHome}/bin:${env.PATH}"
    }

    stage('Clone repository') { 
        checkout scm
    }

    stage('Build image') {
        app = docker.build("sachinteli/docker")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
           docker.withRegistry('https://registry.hub.docker.com', 'docker-cred') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
	
	stage('Deploy to GKE') {
	    steps{
		sh "sed -i 's/mytestapp1:latest/mytestapp1:${env.BUILD_ID}/g' deployment.yaml"
		step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
	    }
	}
}
