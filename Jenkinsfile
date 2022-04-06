pipeline{
    agent any
    
    stages{
        stage("Git Checkout"){
            steps{
                git credentialsId: 'github', url: 'https://github.com/xxxx/xxxx'
            }
        }
	      
  
        stage("Maven Build"){
            steps{
                script{
                // Get Home Path of Maven 
                def mvnHome = tool name: 'maven-3', type: 'maven'
                sh "${mvnHome}/bin/mvn clean package"
                }
            }
        }
	    
        
        stage("Deploy to Tomcat Server"){
            steps{
                sshagent(['aws-ec2-keypair']) {
                sh """
		            echo $WORKSPACE
		    
		            mv target/*.war target/javawebapplication.war
		    
                    scp -o StrictHostKeyChecking=no target/javawebapplication.war  ec2-user@private-ip-tomcat-server:/opt/tomcat8/webapps/
                    
                    ssh ec2-user@private-ip-tomcat-server /opt/tomcat8/bin/shutdown.sh
                    
                    ssh ec2-user@private-ip-tomcat-server /opt/tomcat8/bin/startup.sh
                
                """
                }
            
            }
        }
    }
    post {
	    always {
		echo 'Deleting the Workspace'
		deleteDir() /* Clean Up our Workspace */
	    }
	    success {
		mail to: 'xxx@gmail.com',
		     subject: "Success Build Pipeline: ${currentBuild.fullDisplayName}",
		     body: "The pipeline ${env.BUILD_URL} completed successfully"
	    }
	    failure {
		mail to: 'xxx@gmail.com',
		     subject: "Failed Build Pipeline: ${currentBuild.fullDisplayName}",
		     body: "Something is wrong with ${env.BUILD_URL}"
	    }
    }	    
}
