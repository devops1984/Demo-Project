pipeline {
    agent any
	environment {
	    PATH = "$PATH:/usr/share/maven/bin"
	}
	 stages {
                stage('Maven Build'){
		    steps{
			     sh " mvn clean package"   
			}
		}
		 stage('Sonar Analysis') {
                     steps {
                      withSonarQubeEnv('Sonarqube') {
                             sh " mvn sonar:sonar \
			          -Dsonar.host.url=http://54.183.145.72:9000 \
			          -Dsonar.login=squ_7c11429c82fede8c7f4d20ebba9ca738b52f0c6f"
                             }
                     }
               }
		stage('Upload War to Nexus'){
		    steps{
			     nexusArtifactUploader artifacts: [
				     [
					     artifactId: 'webapp', 
					     classifier: '', 
					     file: 'webapp/target/webapp.war', 
					     type: 'war'
				     ]
			     ], 
				     credentialsId: 'NEXUS3', 
				     groupId: 'com.example.maven-project', 
				     nexusUrl: '51.124.248.36:8081', 
				     nexusVersion: 'nexus3', 
				     protocol: 'http', 
				     repository: 'demo-app', 
				     version: '1.0.0'
			}
		}
		stage('Transfer artifact to Tomcat server') {
                    steps {
			    sshPublisher(publishers: 
			      [sshPublisherDesc(configName: 'jenkins', 
				  transfers: [sshTransfer(cleanRemote: false, 
				  excludes: '', 
				  execCommand: 'rsync -avh  /var/lib/jenkins/workspace/test2/webapp/target/*.war tomcat@172.31.11.194:/opt/tomcat/apache-tomcat-10.0.23/webapps/webapp.war', 
				  execTimeout: 120000, flatten: false, 
				  makeEmptyDirs: false, noDefaultExcludes: false, 
				  patternSeparator: '[, ]+', 
				  remoteDirectory: '', remoteDirectorySDF: false, 
				  removePrefix: '', sourceFiles: '')], 
				  usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
                       }
                }
	}
}   
