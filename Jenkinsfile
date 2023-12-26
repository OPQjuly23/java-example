pipeline {
    agent none
    parameters {
	string (defaultValue: 'dev',description:'Enter the ENV value', name:'env.BRANCH_NAME')
	choice (name: 'BRANCH_NAME', choices: ['master','dev','build'], description:'')
	  booleanParam defaultValue: true, name: 'my boolean'
  
}
	  
environment {
GITHUB_KEY = credentials('my github token')
JENKINS_USER = credentials('jenkins-username')
}


triggers {
  cron '5 * * * *'
  pollSCM '* * * * *'
}

    stages {
        stage('checkout'){
		agent {
		label "sonar"
		}
            steps{
              git 'https://github.com/OPQjuly23/Train-Ticket-Reservation-System.git'
              echo " Executing in $ENV envirnoment "
	        	echo "This is my github token ${GITHUB_KEY}"
	        	echo " This is my Jenkins username is ${JENKINS_USER_USR} "
			      echo " This is my jenkis password is ${JENKINS_USER_PSW} "
            }
        }
	         stage('stage-2 sonar') {
	             	agent {
		 label "sonar"
		 }
		  steps {
            	withSonarQubeEnv(credentialsId: 'sonarqube-Token', installationName: 'sonar-server') {
    // some block
    sh 'mvn clean verify sonar:sonar'
            	}         
		 }
	         }
		 
        stage('stage-3 maven build') {
            agent {
		 label "Tomcat"
		 }
		    when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            
        steps {
            
            sh 'mvn clean install'
	               echo "executing ${branch} on checkout code"
             }
        }
		
                stage('stage-4 deploy') {
	          	agent {
		       label "Tomcat"
		  }
		  when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
		sshagent(['tomcatcred']) {
    // some block
            sh 'scp -o strictHostkeyChecking=no target/*.war ubuntu@65.2.124.250:/opt/tomcat/apache-tomcat-9.0.68/webapps/'
			             
            }
        }
    
    }
}

    
            post {
        success {
            mail to:"ashokchowdary9928@gmail.com", subject:"SUCCESS: [job name=${env.JOB_NAME}] [Build no=${env.BUILD_NUMBER}][build url= ${env.BUILD_URL}]", body: "[job name=${env.JOB_NAME}] [Build no=${env.BUILD_NUMBER}][build url= ${env.BUILD_URL}] Build Success."
        }
        failure {
            mail to:"ashokchowdary9928@gmail.com", subject:"FAILURE: ${env.JOB_NAME} [${env.BUILD_NUMBER} ${env.BUILD_URL}", body: "[job name=${env.JOB_NAME}] [Build no=${env.BUILD_NUMBER}][build url= ${env.BUILD_URL}] Build failure."
        }
    }        
}
