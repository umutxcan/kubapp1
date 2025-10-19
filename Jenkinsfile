environment {
  CHART_PATH = '.'
  RELEASE    = 'myapp'
  NAMESPACE  = 'app'
  IMAGE      = 'umut062/myapp'
  IMAGE_TAG  = 'latest'
}

stages {
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
