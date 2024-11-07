   pipeline {
    agent any

    tools {
        // Specify the JDK and Maven versions installed on the Jenkins server
        jdk 'JDK17'
        maven 'Maven3'
		}

    environment {
        // Set any environment variables needed, e.g., JAVA_HOME, MAVEN_HOME, SONARQUBE
        JAVA_HOME = "${tool 'JDK17'}"
        MAVEN_HOME = "${tool 'Maven3'}"		
        PATH = "${env.MAVEN_HOME}/bin:${env.PATH}"
        SONAR_TOKEN = credentials('sonarqube-token') 
		SONARQUBE_SERVER = 'sonarqube'
        scannerHome = tool 'sonarqube-scanner';
        docker = tool 'docker'
        DOCKER_TOKEN = credentials('dockerhub-credentials') 
        
		
		
    }


    stages {
       
       stage('1-clean workspace'){
            steps{
                cleanWs()
            }
        }


        stage('2-Checkout-github') {
            steps {
                // Checkout code from the repository
                git url: 'https://github.com/jaiswaladi246/Boardgame.git', branch: 'main'
            }
        }
        
        stage('3-MVN-Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('4-MVN-Test') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('5-OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('6-TRIVY-File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage('7-SonarQube Analsyis') {
            steps {
               withSonarQubeEnv(credentialsId: 'sonarqube-token', installationName: 'sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=test -Dsonar.projectName='test' -Dsonar.java.binaries=. "
                }
            }
        }
        
        stage('8-MVN-Build') {
            steps {
                sh "mvn package"
            }
        }
        
        stage("Docker Build & Push"){
           steps{
                script{
                   withDockerRegistry(credentialsId: 'dockerhub-credentials', toolName: 'docker'){
                       
                       sh "docker build -t psschiatanya/boardshack:latest ."
                       //sh "docker build -t amazon ."
                       //sh "docker tag amazon bhubon33/amazon:latest "
                       //sh "docker push bhubon33/amazon:latest "
                    }
                }
            }
        }
        
        
        
        
        
       
        
        
    }
}    
    