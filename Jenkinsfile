pipeline {
    agent any
    tools {
        maven 'maven'
    }
     stages {
        stage('Cloning code from GitHub') {
            steps {
                 git url: 'https://github.com/Deepanshu-goswami/java-app.git' , branch: 'main'
            }
        }
        stage('Application building with Maven') {
            steps {
                // Define steps for the Build stage
                echo 'Building the project...'
                sh 'mvn clean package'
            }
        }
        stage('Code Analysis with Sonarqube') {
           steps{
            script{
               withSonarQubeEnv(installationName: 'sonar', credentialsId: 'jenkins-sonar-token')
                {
                  sh 'mvn sonar:sonar'
                }
               
            }
         }
                
        }
    	stage('Building our image with Docker') {
		 steps{                
	        sh 'docker build -t java-application-new:$BUILD_NUMBER .'           
             echo 'Build Image Completed'                
             }
        }
        stage('Image Scanning with Trivy'){
            steps{
                sh 'trivy image java-application-new:$BUILD_NUMBER'
            }
        }
        stage('Image pushing to Google Artifact Registry') {
            steps {
                // Define steps for the image push...
                echo 'Image pushing...'
                sh 'gcloud auth configure-docker \
                    asia-south2-docker.pkg.dev'
                sh'docker tag java-application-new:$BUILD_NUMBER asia-south2-docker.pkg.dev/vishwas-426408/jenkins-repo/java-application-new:$BUILD_NUMBER'
                sh 'docker push asia-south2-docker.pkg.dev/vishwas-426408/jenkins-repo/java-application-new:$BUILD_NUMBER'
                
            }
        }
    }
 post {
        success {
            echo 'Pipeline succeeded!!'
            mail to: 'Deepanshu.goswami95@gmail.com',
                 subject: "CI-Pipeline Succeeded: ${currentBuild.fullDisplayName}",
                 body: "Congratulations! The pipeline ${env.BUILD_URL} has successfully completed."
        }
        failure {
            echo 'Pipeline failed!'
            mail to: 'Deepanshu.goswami95@gmail.com',
                 subject: "CI-Pipeline Failed: ${currentBuild.fullDisplayName}",
                 body: "Oops! The pipeline ${env.BUILD_URL} has failed. Check the logs for details.Try Again"
        }
    }
}
