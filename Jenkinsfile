pipeline {
  agent any

  environment {
    // Helm'i geçici /tmp'e indir, PATH'e ekle (lokalde de kullanırız)
    PATH         = "/tmp/helm-${BUILD_TAG}:$PATH"

    // Repo/chart ayarları
    CHART_PATH   = '.'          // Chart.yaml kökte
    RELEASE      = 'myapp'
    NAMESPACE    = 'app'
    IMAGE        = 'umut062/myapp'
    IMAGE_TAG    = 'latest'

    // Uzak RKE2 manager
    MANAGER      = '192.168.100.108'
    REMOTE_DIR   = '/root/kubapp1'
  }

  stages {
    stage('Checkout & clean') {
      steps {
        checkout scm
        sh '''
          set -e
          # Repo /önceki  temizle
          git clean -xfd || true
          find . -maxdepth 1 -type f -name helm -delete || true
          find . -maxdepth 2 -type d -name bin -exec rm -rf {} + || true
          ls -la
        '''
      }
    }

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

    stage('Sync chart to manager') {
      steps {
        sshagent(credentials: ['root-ssh']) {
          sh '''
            set -e
            # Manager'da helm yoksa kur
            ssh -o StrictHostKeyChecking=no root@${MANAGER} 'command -v helm >/dev/null 2>&1 || curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash'

            # Chart manager'a senkronla (çöp dosyalar hariç)
            rsync -az --delete \
              --exclude ".git" --exclude "bin" --exclude "helm" \
              ./ root@${MANAGER}:${REMOTE_DIR}/
          '''
        }
      }
    }

    stage('Helm dry-run on manager') {
      steps {
        sshagent(credentials: ['root-ssh']) {
          sh '''
            set -e
            ssh root@${MANAGER} "
              helm dependency update ${REMOTE_DIR} || true &&
              helm lint ${REMOTE_DIR} || true &&
              helm upgrade --install ${RELEASE} ${REMOTE_DIR} \
                -n ${NAMESPACE} --create-namespace \
                -f ${REMOTE_DIR}/values.yaml \
                --set image.repository=${IMAGE} \
                --set image.tag=${IMAGE_TAG} \
                --dry-run --debug
            "
          '''
        }
      }
    }

    stage('Helm deploy on manager') {
      steps {
        sshagent(credentials: ['root-ssh']) {
          sh '''
            set -e
            ssh root@${MANAGER} "
              helm upgrade --install ${RELEASE} ${REMOTE_DIR} \
                -n ${NAMESPACE} --create-namespace \
                -f ${REMOTE_DIR}/values.yaml \
                --set image.repository=${IMAGE} \
                --set image.tag=${IMAGE_TAG}
            "
          '''
        }
      }
    }
  }
}
