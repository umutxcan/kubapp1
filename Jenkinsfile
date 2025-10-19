pipeline {
  agent any
  environment {
    PATH = "/tmp/helm-${BUILD_TAG}:$PATH"
    CHART_PATH = '.'
    RELEASE    = 'myapp'
    NAMESPACE  = 'app'
    IMAGE      = 'umut062/myapp'
    IMAGE_TAG  = 'latest'
  }
  stages {
    stage('Install helm (tmp)') {
  steps {
    sh '''
      set -e
      mkdir -p "/tmp/helm-${BUILD_TAG}"
      if ! command -v helm >/dev/null 2>&1; then
        VER=$(curl -fsSL https://api.github.com/repos/helm/helm/releases/latest | grep tag_name | cut -d '"' -f4 | sed 's/^v//')
        curl -fsSL -o /tmp/helm.tgz "https://get.helm.sh/helm-v${VER}-linux-amd64.tar.gz"
        tar -xzf /tmp/helm.tgz -C /tmp
        mv /tmp/linux-amd64/helm "/tmp/helm-${BUILD_TAG}/helm"
        rm -rf /tmp/linux-amd64 /tmp/helm.tgz
        chmod +x "/tmp/helm-${BUILD_TAG}/helm"
      fi
      helm version
    '''
  }
}

    stage('Helm dry-run') {
      steps {
        sh '''
          helm dependency update "$CHART_PATH" || true
          helm lint "$CHART_PATH" || true
          helm upgrade --install "$RELEASE" "$CHART_PATH" \
            -n "$NAMESPACE" --create-namespace \
            -f "$CHART_PATH/values.yaml" \
            --set image.repository="$IMAGE" \
            --set image.tag="$IMAGE_TAG" \
            --dry-run
        '''
      }
    }

    stage('Helm deploy') {
      steps {
        sh '''
          helm upgrade --install "$RELEASE" "$CHART_PATH" \
            -n "$NAMESPACE" --create-namespace \
            -f "$CHART_PATH/values.yaml" \
            --set image.repository="$IMAGE" \
            --set image.tag="$IMAGE_TAG"
        '''
      }
    }
  }
}
