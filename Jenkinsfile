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
def dockerImage
//jenkins needs entrypoint of the image to be empty
def runArgs = '--entrypoint \'\''
pipeline {
    agent {
        label 'any'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '100', artifactNumToKeepStr: '20'))
        timestamps()
    }
    stages {
        stage('Build') {
            options { timeout(time: 30, unit: 'MINUTES') }
            steps {
                script {
                    def commit = checkout scm
                    // we set BRANCH_NAME to make when { branch } syntax work without multibranch job
                    env.BRANCH_NAME = commit.GIT_BRANCH.replace('origin/', '')

                    dockerImage = docker.build("faisal2018/train-schedule:${env.BUILD_ID}",
                        "--label \"GIT_COMMIT=${env.GIT_COMMIT}\""
                        + " --build-arg MY_ARG=myArg"
                        + " ."
                    )
                }
            }
        }
        stage('Push to docker repository') {
            when { branch 'master' }
            options { timeout(time: 5, unit: 'MINUTES') }
            steps {
                lock("${JOB_NAME}-Push") {
                    script {
                        docker.withRegistry('https://myrepo:5000', 'docker_registry') {
                            dockerImage.push('latest')
                        }
                    }
                    milestone 30
                }
            }
        }
    }
stage('sonar-scanner') {
	      def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
	      withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
	        sh "${sonarqubeScannerHome}/bin/sonar-scanner -e -Dsonar.host.url=http://${SONARQUBE_HOSTNAME}:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=WebApp -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=src/main/ -Dsonar.tests=src/test/ -Dsonar.java.binaries=build/**/* -Dsonar.language=java"
	      }
	    }
	

	}
	//END-OF-SCRIPT

}



