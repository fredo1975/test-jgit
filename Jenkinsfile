pipeline {
    agent any
    tools {
        maven 'Maven 3.3.9'
        jdk 'jdk8'
    }
    environment {
    	VERSION = readMavenPom().getVersion()
    	def pom = readMavenPom file: 'pom.xml'
    	def NVERSION = pom.version.replace("-SNAPSHOT", "")
    	ACTION_TYPE = "${env.ACTION_TYPE}"
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
                    echo "ACTION_TYPE = ${ACTION_TYPE}"
                '''
            }
        }

        stage ('Build') {
		 	steps {
		 		withMaven(mavenSettingsConfig: 'MyMavenSettings') {
		 			script {
		 				if("${ACTION_TYPE}" == "release-start"){
		 					sh ''' mvn -X jgitflow:release-start -DdevelopmentVersion=${DEV_VERSION} '''
		 				}else if ("${ACTION_TYPE}" == "release-end") {
		 					sh ''' mvn -X jgitflow:release-finish'''
		 				}
		 			}
            	}
		    }
        }
    }
}
