  def label = "eosagent"
  def env = "dev"
  podTemplate(label: label, yaml: """
  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      name: build
  spec:
    serviceAccount: jenkins
    containers:
    - name: build
      image: dpthub/eos-jenkins-agent-base:latest
      command:
      - cat
      tty: true
  """
  ) {
      node (label) {

          stage ('Checkout SCM'){
            git credentialsId: 'git_key', url: 'https://github.com/rajeshprakashtalla/eos-admin-deployment.git', branch:  "${env}"
          }

          stage ('Helm Chart') {
            container('build') {
                withCredentials([usernamePassword(credentialsId: 'Jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
                      sh '/usr/local/bin/helm repo add eos-helm-local  https://rajesh12345.jfrog.io/artifactory/eos-helm-local --username $username --password $password'
                      sh "/usr/local/bin/helm repo update"
                      sh "/usr/local/bin/helm upgrade  --install --force micro-services-admin  --namespace ${env} -f values.yaml eos-helm-local/micro-services-admin"
                      sh "/usr/local/bin/helm list -a --namespace ${env}"
                      sh "rm -rf values.yaml"
              }
          }
          }
      }
  }