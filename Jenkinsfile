node(){
    def nginx_repo = "lokeshkamalay/nginx:latest"
    def tomcat_repo = "lokeshkamalay/tomcat"

    stage ('checkout'){
	checkout scm
    }
    
    stage('Build'){
	  
	    sh "yum -y install docker"
	    sh "systemctl start docker"
        docker.image('maven:latest').inside(){
            sh "mvn clean package"
        }
    }
    
    stage('Prepare the Image'){
       docker.withRegistry('','dockerhub') {
        def customImage = docker.build("$tomcat_repo:${env.BUILD_ID}")
		//withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'docker_password ', usernameVariable: 'docker_user ')]) {
               //sh "docker login --username=$docker_user --password=$docker_password"
		customImage.push()
            // }
       
        }
    }
    
    stage('Deploy'){
        sh """
            docker run -d -p 8081:8080 $tomcat_repo:$BUILD_ID
            docker run -d -p 8082:8080 $tomcat_repo:$BUILD_ID
            docker run -d -p 8080:80 $nginx_repo
        """
    }

}
