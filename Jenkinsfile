node {
  // Repo içeriğini workspace'e al (Pipeline script from SCM'de çoğu zaman gerekir)
  stage('Checkout') {
    checkout scm
    sh 'pwd && ls -la'
  }

  // Değişkenler
  env.CHART_PATH = '.'
  env.RELEASE    = 'myapp'
  env.NAMESPACE  = 'app'
  env.IMAGE      = 'umut062/myapp'
  env.IMAGE_TAG  = 'latest'

  stage('Helm dry-run') {
    sh """
      helm dependency update "${env.CHART_PATH}" || true
      helm lint "${env.CHART_PATH}" || true
      helm upgrade --install "${env.RELEASE}" "${env.CHART_PATH}" \\
        -n "${env.NAMESPACE}" --create-namespace \\
        -f "${env.CHART_PATH}/values.yaml" \\
        --set image.repository="${env.IMAGE}" \\
        --set image.tag="${env.IMAGE_TAG}" \\
        --dry-run
    """
  }

  stage('Helm deploy') {
    sh """
      helm upgrade --install "${env.RELEASE}" "${env.CHART_PATH}" \\
        -n "${env.NAMESPACE}" --create-namespace \\
        -f "${env.CHART_PATH}/values.yaml" \\
        --set image.repository="${env.IMAGE}" \\
        --set image.tag="${env.IMAGE_TAG}"
    """
  }
}
