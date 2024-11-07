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
		
		
    }


    stages {
       
       stage('clean workspace'){
            steps{
                cleanWs()
            }
        }


        stage('Checkout') {
            steps {
                // Checkout code from the repository
                git url: 'https://github.com/psschiatanya/jenkins-trivy', branch: 'main'
            }
        }

 /*     stage('Compile') {
            steps {
                // Clean and compile the project
                sh 'mvn clean compile'
            }
        } */

 /*     
		
    	stage('Code Analysis') {
            steps {
                script {
                    // Run SonarQube scan
                    
                        sh '''mvn clean verify sonar:sonar  -Dsonar.java.binaries=. -Dsonar.projectKey=test  -Dsonar.projectName='test' -Dsonar.host.url=http://3.107.55.196:9000   -Dsonar.login=${SONAR_TOKEN}'''
                    
                }
            }
        }  */
        
 
		
/*		stage('Code Analysis-2') {
            steps {
                script {
                    // Run SonarQube scan
                    withSonarQubeEnv(SONARQUBE_SERVER) {
                        sh '''${MAVEN_HOME}/bin/mvn sonar:sonar  -Dsonar.java.binaries=. -Dsonar.projectKey=test  -Dsonar.projectName='test' -Dsonar.host.url=http://3.107.55.196:9000   -Dsonar.login=${SONAR_TOKEN}'''
                    }
                }
            }
        } 
*/		
		
	
		stage('Trivy FileSystem Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
    
		
	    stage('Run Sonarqube') {
            environment {
                scannerHome = tool 'sonarqube-scanner';
            }
            steps {
              withSonarQubeEnv(credentialsId: 'sonarqube-token', installationName: 'sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=test -Dsonar.projectName='test' -Dsonar.java.binaries=. "
              }
            }
        }  
		
		
	 	  stage('Quality Gate') {
            steps {
                // Wait for SonarQube quality gate result
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token'
                }
            }
        } 
       

    /*  stage (" Build_Jar_file")
                {
                steps {
                        echo "Step2: Maven Build"
                        sh 'mvn -B -DskipTests clean install'
                
                      }
                }
*/		
		stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
	  
		
    }	
	}
 
		
		
