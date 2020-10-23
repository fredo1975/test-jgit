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
		  // No checkout stage ? That is not required for this case 
		  // because Jenkins will checkout whole repo that contains Jenkinsfile, 
		  // which is also the tip of the branch that we want to build
		
		  stage ('Build') {
		    steps {
		      // For debugging purposes, it is always useful to print info 
		      // about build environment that is seen by shell during the build
		      sh 'env'      
		      sh """
		        SHORTREV=`git rev-parse --short HEAD`
		      """
		      script {
		            def pom = readMavenPom file: 'pom.xml'            
		            // Now you have access to raw version string in pom.version
		            // Based on your versioning scheme, automatically calculate the next one            
		            VERSION = pom.version.replaceAll('SNAPSHOT', BUILD_TIMESTAMP + "." + SHORTREV)
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
		  
		  stage('Unit Tests') {
		    // We have seperate stage for tests so 
		    // they stand out in grouping and visualizations
		    steps {
		      sh """
		        mvn -B test $MAVEN_OPTIONS
		      """
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
		      sh """
		        mvn -B integration-test $MAVEN_OPTIONS
		      """
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
		      sh """
		        mvn -B deploy
		      """
		    }
		  }
		}
    }
}
