pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  serviceAccountName: jenkins
  securityContext:
    runAsUser: 0
    runAsGroup: 0
  containers:
  - name: kubectl
    image: dtzar/helm-kubectl:3.14.4
    command: ["cat"]
    tty: true
  - name: git
    image: alpine/git:2.45.2
    command: ["cat"]
    tty: true
"""
    }
  }

  environment {
    NAMESPACE = "onlineboutique"
  }

  stages {
    stage("Checkout") {
      steps { checkout scm }
    }

    stage("Deploy") {
      when { branch "main" }
      steps {
        container('kubectl') {
          sh '''
            set -euo pipefail
            kubectl apply -n "${NAMESPACE}" -f release/kubernetes-manifests.yaml
            kubectl rollout status -n "${NAMESPACE}" deploy/frontend --timeout=600s
          '''
        }
      }
    }
  }
}

