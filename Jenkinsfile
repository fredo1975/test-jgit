pipeline {
    agent any
    tools {
        maven 'Maven 3.6.0'
        jdk 'jdk8'
    }
    environment {
    	VERSION = readMavenPom().getVersion()
    	def pom = readMavenPom file: 'pom.xml'
    	def NVERSION = pom.version.replace("-SNAPSHOT", "")
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "VERSION = ${VERSION}"
                    echo "pom = ${pom}"
                    echo "NVERSION = ${NVERSION}"
                    echo "DEV_VERSION = ${DEV_VERSION}"
                '''
            }
        }

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
