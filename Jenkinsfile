pipeline {
    agent any
    tools {
        maven 'Maven 3.6.0'
        jdk 'jdk8'
    }
    stages {
		  // No checkout stage ? That is not required for this case 
		  // because Jenkins will checkout whole repo that contains Jenkinsfile, 
		  // which is also the tip of the branch that we want to build
		  stage ('Build') {
		  	steps {
		    	sh 'env'
				sh '''
					SHORTREV=`git rev-parse --short HEAD`
				'''
				script {
					def pom = readMavenPom file: 'pom.xml'
				    VERSION = pom.version.replaceAll('SNAPSHOT', BUILD_TIMESTAMP)
					}
		    	}
			}
			
			stage('Unit Tests') {
			    // We have seperate stage for tests so 
			    // they stand out in grouping and visualizations
			    steps {
			    	withMaven(mavenSettingsConfig: 'MyMavenSettings') {
			 			script {
			 				sh ''' mvn -B tes'''
			 			}
	            	}
			    }
			    // Note that, this requires having test results. 
			    // But you should anyway never skip tests in branch builds
			    post {
			      always {
			        junit '**/target/surefire-reports/*.xml'
			      }
			    }    
			  }
			  
			  stage('Integration Tests') {
			    steps {
			    	withMaven(mavenSettingsConfig: 'MyMavenSettings') {
			 			script {
			 				sh ''' mvn -B integration-test'''
			 			}
	            	}
			    }
			    post {
			      always {
			        junit '**/target/failsafe-reports/*.xml'
			      }
			    }    
			  }
			
			  stage('Deploy') {
			    steps {
			      // Finally deploy all your jars, containers, 
			      // deliverables to their respective repositories
			      withMaven(mavenSettingsConfig: 'MyMavenSettings') {
			 			script {
			 				sh ''' mvn -B deploy'''
			 			}
	            	}
			    }
			  }
    	}
}
