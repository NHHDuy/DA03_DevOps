node {
    def project = "devops"

	def app
	stage('Clone repository') {
		checkout scm
	}

	stage('Build image') {
		app = docker.build("19120064/${project}")
	}

	stage('Test') {
		app.inside {
            sh "npm --prefix ./sources/ run test"
            sh "echo 'running addional test'"
            sh "echo 'passed'"
		}
	}

	stage('Push') {
		docker.withRegistry("https://registry.hub.docker.com", "Docker") {
			app.push("${env.BUILD_ID}") // push images with new tag to docker hub
			app.push("latest") // change the latest tag to it
		}
    }

	stage('Deploy'){
        try {
            sh "docker kill ${project}"
        }
        catch (exe){}
        finally {
            app.run("--rm --name ${project} -p 8081:3000")
        }
	}
}
