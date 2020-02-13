def M2_PATH = "\$HOME/.m2"

node {
  def build

  stage('Clone repository') {
    sh '''
      if [ -d "${FOLDER_NAME}" ]; then
        rm -rf ${FOLDER_NAME}
      fi

      git clone ${GITHUB_REPO} ${FOLDER_NAME}
      cd "${FOLDER_NAME}"
      git checkout ${BRANCH_NAME}
    '''

  }

  stage('Build image') {
    dir("${env.WORKSPACE}/${FOLDER_NAME}/${MICROSERVICE}") {
      sh "docker build -t ${BUILD_IMAGE_NAME}:${BUILD_IMAGE_TAG} ."
    }
  }

  stage('Build app') {
    // Stop and remove container if it exists
    sh "docker stop ${BUILD_CONTAINER_NAME} || true && docker rm ${BUILD_CONTAINER_NAME} || true"
    sh "docker run --name ${BUILD_CONTAINER_NAME} --mount type=bind,source=${M2_PATH},target=/root/.m2 ${BUILD_IMAGE_NAME}:${BUILD_IMAGE_TAG}"
  }

  stage('Copy built file to host') {
    dir("${env.WORKSPACE}/${FOLDER_NAME}/${MICROSERVICE}") {
      sh " [ ! -d \"jar\" ] && mkdir jar || true"
      sh "docker cp ${BUILD_CONTAINER_NAME}:target/${JAR_FILE}.jar jar/"
      sh "cd jar && docker build -t ${DEPLOY_IMAGE_NAME}:${DEPLOY_IMAGE_TAG} ."
    }
  }

  // stage('Tag and push image to registry') {
  //   sh "docker tag ${DEPLOY_IMAGE_NAME}:${DEPLOY_IMAGE_TAG} localhost:5000/${DEPLOY_IMAGE_NAME}:${DEPLOY_IMAGE_TAG}"
  //   sh "docker push localhost:5000/${DEPLOY_IMAGE_NAME}:${DEPLOY_IMAGE_TAG}"
  // }

  stage('Remove build image') {
    sh "docker stop ${BUILD_CONTAINER_NAME} && docker rm ${BUILD_CONTAINER_NAME}"
    sh "docker image rm ${BUILD_IMAGE_NAME}:${BUILD_IMAGE_TAG}"
  }

}