import java.text.SimpleDateFormat

currentBuild.displayName = new SimpleDateFormat("yy.MM.dd").format(new Date()) + "-" + env.BUILD_NUMBER
env.REPO = "https://gitlab.com/kofiray/k8s-ci.git"
env.IMAGE = "kofiray/qbe-ci"
env.TAG_BETA = "${currentBuild.displayName}-${env.BRANCH_NAME}"

node("docker") {
  stage("build") {
    git "${env.REPO}"
    sh """sudo docker image build \
      -t ${env.IMAGE}:${env.TAG_BETA} ."""
    withCredentials([usernamePassword(
      credentialsId: "docker",
      usernameVariable: "USER",
      passwordVariable: "PASS"
    )]) {
      sh """sudo docker login \
        -u $USER -p $PASS"""
    }
    sh """sudo docker image push \
      ${env.IMAGE}:${env.TAG_BETA}"""

    }
}
    node("kubernetes") {
    stage("deploy") {
    git "${env.REPO}"
        container("helm") {
            sh "helm install --name=qbe--demo --namespace cluster-ops . --set image.tag=${env.TAG_BETA}"

        }
    }
    }
