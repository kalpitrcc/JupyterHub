pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: git
            image: alpine/git:latest
            command:
            - cat
            tty: true
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
             - mountPath: /var/run/docker.sock
               name: docker-sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock    
        '''
    }
  }
  environment {
    DOCKER_HUB_CREDENTIALS = credentials('DEVSDS-DOCKERHUB')
    }
  
  
  stages {
    stage('Clone') {
      steps {
        container('git') {
          git branch: 'main', changelog: false, poll: false, url: 'https://github.com/kalpitrcc/JupyterHub.git'
        }
      }
    }  
    
   
    
    stage('Build-Docker-Image') {
      steps {
        container('docker') {
          sh 'docker build -t devsds/jupyterhub:v1.0_$BUILD_NUMBER .'
        }
      }
    }
    stage('Login-Into-Docker') {
      steps {
        container('docker') {
           steps{
                sh 'echo $DOCKER_HUB_CREDENTIALS_PSW | sudo docker login -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin'
            }
      }
    }
    }
     stage('Push-Images-Docker-to-DockerHub') {
      steps {
        container('docker') {
          sh 'docker push devsds/jupyterhub:v1.0_$BUILD_NUMBER'
      }
    }
     }
  }
    post {
      always {
        container('docker') {
          sh 'docker images'
      }
      }
    }
}
