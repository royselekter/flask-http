node() { 
  def DockerImage = "webserver:v1.2"
  
  stage('Pre') { // Run pre-build steps
    cleanWs()
    sh "docker rm -f webserver || true"
  }
  
  stage('Git') { // Get code from GitLab repository
    git branch: 'master',
      url: 'https://github.com/yishaiz/opsshool-docker-home-assignment-2.git'
  }
  
  stage('Build') { // Run the docker build
    sh "docker build --tag ${DockerImage} ."
  }
  stage('Run') { // Run the built image
    sh "docker run -d --name webserver --rm -p 8081:5000 ${DockerImage}; sleep 5"
  }
  
  stage('Test') { // Run tests on container
    def dockerOutput = sh (
        script: 'curl http://192.168.15.30:8081/goaway',
        //script: 'curl http://localhost:8081/goaway',
        returnStdout: true
        ).trim()
  //  sh "docker rm -f webserver"
    
    if ( dockerOutput == 'GO AWAY!' ) {
        currentBuild.result = 'SUCCESS'
    } else {
        currentBuild.result = 'FAILURE'
        sh "echo Webserver returned ${dockerOutput}"
    }
    return
  }
 stage('Push') { // Push the built image to docker hub
    withDockerRegistry(credentialsId: 'docker_hub') {
        sh "docker tag ${DockerImage} royselekter/${DockerImage}"
        sh "docker push royselekter/${DockerImage}"
    }
 }
}
