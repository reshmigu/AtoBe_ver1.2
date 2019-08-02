node (label: 'windows'){

    withMaven(maven:'maven') {

        stage('Checkout') {
            git url: 'https://github.com/reshmigu/AtoBe_ver1.2.git', credentialsId: 'master', branch: 'master'
        }

		stage('Build') {
            bat 'mvn clean package shade:shade'
            def pom = readMavenPom file:'pom.xml'
            env.version = pom.version
        }

        stage('Image') {
                bat 'docker stop restassured || exit 0'
				bat 'docker rm restassured || exit 0'
                cmd = "docker rmi restassured:${env.version} || exit 0"
                bat cmd
                bat "docker build -t restassured:${env.version} ."
            
        }

        stage ('Run') {

       		 print "${params}"
		bat "dir"
		 
        	 if ("${params.modes}" == "DRY_RUN") {
       			 bat "docker run -p 8081:8081 -h restassured --name restassured --net host -m=500m restassured:${env.version} DRY_RUN"
      	     }
      	     else if("${params.modes}" == "RUN") {
	  	 	 	 bat "docker run -p 8081:8081 -h restassured --name restassured --net host -m=500m restassured:${env.version} RUN"
      	     }
      	     else if("${params.modes}" == "FULL_RUN") {
	  	 		 bat "docker run -p 8081:8081 -h restassured --name restassured --net host -m=500m restassured:${env.version} FULL_RUN"
      	     }
	  bat "docker cp restassured:/test-output ."
		
        def config = [:]
	def subject = config.subject ? config.subject : "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - ${currentBuild.currentResult}!"      
        // Attach buildlog when the build is not successfull
        def attachLog = (config.attachLog != null) ? config.attachLog : (currentBuild.currentResult != "SUCCESS")
		
        env.ForEmailPlugin = env.WORKSPACE
        emailext mimeType: 'text/html',
	attachLog :attachLog,
	compressLog : true,
        body: '${FILE, path="test-output/emailable-report.html"}',
        subject: subject,	
        to: 'dhananjaya.k@thinkpalm.com,arun.j@thinkpalm.com,reshmi.g@thinkpalm.com'
		
          	  
	   	
        }

    }

}
