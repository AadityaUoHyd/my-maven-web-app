node{
    
    stage('Clone repo'){
        git credentialsId: 'GIT-Credentials', url: 'https://github.com/AadityaUoHyd/my-maven-web-app.git'
    }
    
    stage('Maven Build'){
        def mavenHome = tool name: "Maven-3.8.6", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    }
    
    stage('SonarQube analysis') {       
        withSonarQubeEnv('Sonar-Server-7.8') {
       	sh "mvn sonar:sonar"    	
    }
        
    stage('upload war to nexus'){
	steps{
		nexusArtifactUploader artifacts: [	
			[
				artifactId: 'my-maven-web-app',
				classifier: '',
				file: 'target/my-maven-web-app.war',
				type: war		
			]	
		],
		credentialsId: 'nexus3',
		groupId: 'org.aadi',
		nexusUrl: '',
		protocol: 'http',
		repository: 'aadi-release'
		version: '1.0.0'
	}
}
    
    stage('Build Image'){
        sh 'docker build -t aadiraj48dockerhub/my-maven-web-app .'
    }
    
    stage('Push Image'){
        withCredentials([string(credentialsId: 'DOCKER-CREDENTIALS', variable: 'DOCKER_CREDENTIALS')]) {
            sh 'docker login -u aadi -p ${DOCKER_CREDENTIALS}'
        }
        sh 'docker push aadiraj48dockerhub/my-maven-web-app'
    }
    
    stage('Deploy App'){
        kubernetesDeploy(
            configs: 'maven-web-app-deploy.yml',
            kubeconfigId: 'Kube-Config'
        )
    }    
}
