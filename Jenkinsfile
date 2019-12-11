pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'example-solution'
            }
            steps {
                script {
                    app =sudo  docker.build("faisal2018/train-schedule5")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'example-solution'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
       stage('Deploy Docker') {
         
            steps {
                
                    script {
                        sh "docker pull faisal2018/train-schedule:${env.BUILD_NUMBER}"
                       
                       sh "docker run --restart always --name train-schedule -p 8080:8080 -d faisal2018/train-schedule:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
    }
