//START-OF-SCRIPT
	node {
	    def SONARQUBE_HOSTNAME = 'sonarqube'
	

	    def GRADLE_HOME = tool name: 'gradle-4.10.2', type: 'hudson.plugins.gradle.GradleInstallation'
	    sh "${GRADLE_HOME}/bin/gradle tasks"
	

	    stage('prep') {
	        git url: 'https://github.com/cloudacademy/devops-webapp.git'                
	    }
	

	    stage('build') {
	        sh "${GRADLE_HOME}/bin/gradle build"
	    }
		node {
    checkout scm

    def customImage = docker.build("faisal2018/train-schedule:${env.BUILD_ID}")

    customImage.inside {
        sh 'make test'
    }
}

node {
    checkout scm
	docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login')
    def customImage = docker.build("faisal2018/train-schedule:${env.BUILD_ID}")
    customImage.push()
}

stage('sonar-scanner') {
	      def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
	      withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
	        sh "${sonarqubeScannerHome}/bin/sonar-scanner -e -Dsonar.host.url=http://${SONARQUBE_HOSTNAME}:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=WebApp -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=src/main/ -Dsonar.tests=src/test/ -Dsonar.java.binaries=build/**/* -Dsonar.language=java"
	      }
	    }
	

	}
	//END-OF-SCRIPT





