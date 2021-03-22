/*
“Docker-outside-of-Docker”: runs a Docker-based build by connecting a Docker client inside the pod to the host daemon.
*/
podTemplate(yaml: """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:1.11
    command: ['cat']
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
""") {

  def image = "aniketingale/some-content-nginx:${BUILD_NUMBER}"
  node(POD_LABEL) {
    stage('Build Docker image') {
      git url:'https://github.com/aniket-ingale/SampleJSProject.git', branch: "main"
      container('docker') {
        sh "docker build -t ${image} ."
      }
    }
    stage('Push Docker image') {
      git url:'https://github.com/aniket-ingale/SampleJSProject.git', branch: "main"
      container('docker') {
          withCredentials([usernamePassword(credentialsId: 'dockercredentialsid',
                                  usernameVariable: 'USER', 
                                  passwordVariable: 'PASS')]) {
                sh "docker login -u $USER -p '$PASS'"
            }
        
        sh "docker push ${image}"
      }
    } 
  }
}