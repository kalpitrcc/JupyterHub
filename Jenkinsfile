pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: git
            image: maven:alpine
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
          sh 'docker build -t kalpit191/jupyterhub:latest .'
        }
      }
    }
//     stage('Login-Into-Docker') {
//       steps {
//         container('docker') {
//           sh 'docker login -u <docker_username> -p <docker_password>'
//       }
//     }
//     }
//      stage('Push-Images-Docker-to-DockerHub') {
//       steps {
//         container('docker') {
//           sh 'docker push ss69261/testing-image:latest'
//       }
//     }
//      }
  }
    post {
      always {
        container('docker') {
          sh 'docker images'
      }
      }
    }
}
