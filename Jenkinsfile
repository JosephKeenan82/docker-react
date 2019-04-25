pipeline {
	agent { any {} }
    stages {
        stage('build') {
			steps {
				//notify("Started")
				sh 'mvn clean install -DskipDockerBuild'
				archiveArtifacts 'log-service-web\\target\\*.war' 
				//notify("Success")
				//notify("Deploy?")
				input "Deploy?"
			}
		}
		stage('deploy') {
			steps {
				sh 'docker-compose -f log-service-web/docker-compose.yaml up -d'
                sh "curl -I http://localhost:8090/log/sanity"
				//notify("Deployed yo!!")
				input "Finish?"
			}
		}
		stage('Cleanup') {
			steps {
				sh 'docker stop log-service-web_log-service_1'
				sh 'docker rm log-service-web_log-service_1'
				sh 'docker rmi log-service-web_log-service' 
				//notify("Cleaned")
			}
		}
    }
	post {
		failure {
			//notify("Failure")
		}
		always {
			junit 'log-service-web\\target\\surefire-reports\\TEST-*.xml, log-service-business\\target\\surefire-reports\\TEST-*.xml'
			jacoco classPattern: '**/target/classes', execPattern: '**/target/**.exec', sourceInclusionPattern: '**/*.java, **/*.groovy' // TODO: Look into if all showing
		}
	}
} 

/*
def notify(status){
    emailext (
      to: "josephkeenan@hotmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}/console'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
*/

}