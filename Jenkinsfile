def label = "eosagent"
def env = "main"
podTemplate(label: label, yaml: """
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: build
spec:
  serviceAccount: jenkins-admin
  containers:
  - name: eosagent-container
    image: rajeshtalla0209/eos-jenkins-agent-base:latest
    command:
    - cat
    tty: true
"""
) {
    node (label) {
        stage ('Checkout SCM'){
            git credentialsId: 'git', url: 'https://github.com/RKDevops1234/eos-admin-deployment.git', branch: "${env}"
        }

        stage ('Helm Chart') {
            container('eosagent-container') {
                withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
                    sh '/usr/local/bin/helm repo add eos-helm-local  https://rkdevops12.jfrog.io/artifactory/eos-helm-local --username $username --password $password'
                    sh "/usr/local/bin/helm repo update"
                    sh "/usr/local/bin/helm upgrade  --install --force micro-services-admin  --namespace ${env} -f values.yaml eos-helm-local/micro-services-admin"
                    sh "/usr/local/bin/helm list -a --namespace ${env}"
                    sh "rm -rf values.yaml"
                }
            }
        }
    }
}
