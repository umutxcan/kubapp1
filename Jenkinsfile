pipeline {
  agent any
  environment {
    PATH = "${WORKSPACE}/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
    CHART_PATH = '.'
    RELEASE    = 'myapp'
    NAMESPACE  = 'app'
    IMAGE      = 'umut062/myapp'
    IMAGE_TAG  = 'latest'
  }
  stages {
    stage('Install helm locally if missing') {
      steps {
        sh '''
          set -e
          mkdir -p "$WORKSPACE/bin"
          if ! command -v helm >/dev/null 2>&1; then
            VER=$(curl -fsSL https://api.github.com/repos/helm/helm/releases/latest | grep tag_name | cut -d '"' -f4 | sed 's/^v//')
            curl -fsSL -o helm.tgz "https://get.helm.sh/helm-v${VER}-linux-amd64.tar.gz"
            tar -xzf helm.tgz
            mv linux-amd64/helm "$WORKSPACE/bin/helm"
            rm -rf linux-amd64 helm.tgz
            chmod +x "$WORKSPACE/bin/helm"
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
