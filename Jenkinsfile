pipeline {
    agent any
    tools {
        maven 'Maven 3.6.0'
        jdk 'jdk8'
    }
    environment {
    	def VERSION = readMavenPom().getVersion()
    	def pom = readMavenPom file: 'pom.xml'
    	def NVERSION = pom.version.replace("-SNAPSHOT", "")
    }
    stages {
        stage ('Build') {
		 	steps {
		 		withMaven(mavenSettingsConfig: 'MyMavenSettings') {
		 			script {
		 				// Now you have access to raw version string in pom.version
            			// Based on your versioning scheme, automatically calculate the next one            
            			VERSION = pom.version.replaceAll('SNAPSHOT', BUILD_TIMESTAMP + "." + SHORTREV)
            			echo "VERSION = ${VERSION}"
		 				//sh ''' mvn -X -U jgitflow:release-start -DdevelopmentVersion=${DEV_VERSION} jgitflow:release-finish'''
		 			}
            	}
		    }
        }
    }
}
