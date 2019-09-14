def label = "kaniko-${UUID.randomUUID().toString()}"

env.REPO = "https://github.com/kofiray/ci-demo.git"
env.IMAGE = "kofiray/ci-demo"
env.TAG_BETA = "${currentBuild.displayName}-${env.BRANCH_NAME}"

podTemplate(name: 'kaniko', label: label, yaml: """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: regcred
          items:
            - key: .dockerconfigjson
              path: config.json
""") {
  node(label) {
    stage('Build with Kaniko') {

     //  git 'https://github.com/kofiray/ci-demo.git'
       git "${env.REPO}"
        container(name: 'kaniko', shell: '/busybox/sh') {
           withEnv(['PATH+EXTRA=/busybox']) {
            sh '''#!/busybox/sh
            /kaniko/executor --context `pwd` --destination=index.docker.io/kofiray/ci-demo
            '''
           }
        }
      }
    }

    node("kubernetes") {
    stage("deploy") {
    git "${env.REPO}"
        container("helm") {
          sh "helm ls"
        }
    }
    }
  }
