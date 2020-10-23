pipeline {
    agent any
    tools {
        maven 'Maven 3.6.0'
        jdk 'jdk8'
    }
    environment {
        GIT_COMMIT_SHORT = sh(
                script: "printf \$(git rev-parse --short HEAD)",
                returnStdout: true
        )
        def MAVEN_OPTIONS=''
    }
    stages {
		  // No checkout stage ? That is not required for this case 
		  // because Jenkins will checkout whole repo that contains Jenkinsfile, 
		  // which is also the tip of the branch that we want to build
		  stage ('Build') {
		  	steps {
		    	sh 'env'
				script {
					def pom = readMavenPom file: 'pom.xml'
				    VERSION = pom.version.replaceAll('SNAPSHOT', BUILD_TIMESTAMP + "." + GIT_COMMIT_SHORT)
					}
					echo VERSION
				  sh """
			          mvn -B org.codehaus.mojo:versions-maven-plugin:2.5:set -DprocessAllModules -DnewVersion=${VERSION}
			      """
			      sh """
			        mvn -B clean compile ${MAVEN_OPTIONS}
			      """
		    	}
			}
			
			stage('Unit Tests') {
			    // We have seperate stage for tests so 
			    // they stand out in grouping and visualizations
			    steps {
			    	withMaven(mavenSettingsConfig: 'MyMavenSettings') {
			 			script {
			 				sh """ 
			 				mvn -B test $MAVEN_OPTIONS
			 				"""
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
			  stage('Deploy') {
			    steps {
			      // Finally deploy all your jars, containers, 
			      // deliverables to their respective repositories
			      withMaven(mavenSettingsConfig: 'MyMavenSettings') {
			 			script {
			 				sh """ 
			 				mvn -B deploy $MAVEN_OPTIONS
			 				"""
			 			}
	            	}
			    }
			  }
			  stage('Integration Tests') {
			    steps {
			    	withMaven(mavenSettingsConfig: 'MyMavenSettings') {
			 			script {
			 				sh """ 
			 				mvn -B integration-test $MAVEN_OPTIONS
			 				"""
			 			}
	            	}
			    }
			  }
			
			  
    	}
}
