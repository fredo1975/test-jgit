pipeline {
    agent any
    tools {
        maven 'Maven 3.6.0'
        jdk 'jdk8'
    }
    /*environment {
    	def VERSION = readMavenPom().getVersion()
    	def pom = readMavenPom file: 'pom.xml'
    	def NVERSION = pom.version.replace("-SNAPSHOT", "")
    }*/
    stages {
    	stage ('Initialize') {
            steps {
                sh '''
                    SHORTREV=`git rev-parse --short HEAD`
                '''
            }
        }
		  // No checkout stage ? That is not required for this case 
		  // because Jenkins will checkout whole repo that contains Jenkinsfile, 
		  // which is also the tip of the branch that we want to build
		  stage ('Build') {
		    steps {
		    	withMaven(mavenSettingsConfig: 'MyMavenSettings') {
				    	// For debugging purposes, it is always useful to print info 
				      // about build environment that is seen by shell during the build
				      sh 'env'
				      script {
				            def pom = readMavenPom file: 'pom.xml'
				            sh '''
			                    SHORTREV=`git rev-parse --short HEAD`
			                    VERSION = `pom.version.replaceAll(\'SNAPSHOT\', BUILD_TIMESTAMP + "." + SHORTREV)`
			                '''
				            // Now you have access to raw version string in pom.version
				            // Based on your versioning scheme, automatically calculate the next one        
				            echo "pom.version = ${pom.version}"
				            
				            echo "VERSION = ${VERSION}"
				      }      
				      // We never build a SNAPSHOT
				      // We explicitly set versions.
				      sh """
				          mvn -B org.codehaus.mojo:versions-maven-plugin:2.5:set -DprocessAllModules -DnewVersion=${VERSION}  $MAVEN_OPTIONS
				      """
				      sh """
				        mvn -B clean compile $MAVEN_OPTIONS
				      """
				    }
		    	}
		}
    }
}
