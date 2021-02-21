podTemplate(label: 'docker-build',
  containers: [
    containerTemplate(
      name: 'docker',
      image: 'docker',
      command: 'cat',
      ttyEnabled: true
    ),
    containerTemplate(
      name: 'argo',
      image: 'argoproj/argo-cd-ci-builder:latest',
      command: 'cat',
      ttyEnabled: true
    ),
  ],
  volumes: [ 
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), 
  ]
) {
    node('docker-build') {
        def dockerHubCred = "dockerhub_cred"
        def appImage
        
        stage('Deploy'){
            container('argo'){
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-ssh-private', keyFileVariable: 'SSH_KEY_FILE')]) {
                    checkout([$class: 'GitSCM',
                        branches: [[name: '*/main' ]],
                        extensions: scm.extensions,
                        userRemoteConfigs: [[
                            url: 'git@github.com:cure4itches/docker-hello-world-deployment.git',
                            credentialsId: 'jenkins-ssh-private',
                        ]]
                    ])
                    sh 'git config --global user.email "cure4itches@gmail.com"'
                    sh 'git checkout main'
                    sh 'cd env/dev && kustomize edit set image arm7tdmi/node-hello-world:${BUILD_NUMBER}'
                    sh 'echo ${SSH_KEY_FILE}'
                    sh 'git commit -a -m "updated the image tag" && git push'
                }
            }
        }
    } 
}