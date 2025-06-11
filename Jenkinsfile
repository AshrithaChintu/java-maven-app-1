pipeline {
    agent any
    
    tools {
        maven "Maven3"
    }
    stages {
        stage('Clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Git clone') {
            steps {
                git branch: 'main', url: 'https://github.com/AshrithaC2000/java-maven-app.git'
            }
        }
        stage('maven war file build') {
            steps {
               sh 'mvn clean package'
            }
        }
        stage('Docker images/conatiner remove') {
            steps {
                script{
                        sh '''docker stop java-maven-app_container
                        docker rm java-maven-app_container
                        docker rmi java-maven-app AshrithaC2000/java-maven-app:latest'''
                }  
            }
        }
        stage('Docker images - Push to dockerhub') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolname: 'docker'){
                
                        sh '''docker build -t java-maven-app .
                        docker tag java-maven-app AshrithaC2000/java-maven-app:latest
                        docker push  AshrithaC2000/java-maven-app:latest'''
                      } 
                }
            }
        }
        stage('docker container of app') {
            steps {
               sh 'docker run -d -p 9000:8080 --name java-maven-app_container -t AshrithaC2000/java-maven-app:latest'
            }
        }
        
    }
    post {
    always {
        script {
            def buildStatus = currentBuild.currentResult
            def buildUser = currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')[0]?.userId ?: 'Github User'
            
            emailext (
                subject: "Pipeline ${buildStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>This is a Jenkins maven CICD pipeline status.</p>
                    <p>Project: ${env.JOB_NAME}</p>
                    <p>Build Number: ${env.BUILD_NUMBER}</p>
                    <p>Build Status: ${buildStatus}</p>
                    <p>Started by: ${buildUser}</p>
                    <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                to: 'ashrithac252000@gmail.com',
                from: 'ashrithac252000@gmail.com',
                replyTo: 'ashrithac252000@gmail.com',
                mimeType: 'text/html',
                attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
            )
           }
       }

    }
}
