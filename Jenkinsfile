node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh "git config user.email manishaverma70241@gmail.com"
                    sh "git config user.name Manisha148"
                    sh "cat deployment.yaml"
                    sh "sed -i 's+manishaverma/deployrepo.*+manishaverma/deployrepo:${DOCKERTAG}+g' deployment.yaml"
                    sh "git add ."
                    sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
                }
            }
        }
    }

    stage('Deploy Helm chart') {
        withCredentials([
            usernamePassword(credentialsId: 'helm-credentials', usernameVariable: 'HELM_USERNAME', passwordVariable: 'HELM_PASSWORD')
        ]) {
            sh 'helm repo add myrepo <repository-url> --username $HELM_USERNAME --password $HELM_PASSWORD'
            sh 'helm upgrade --install mychart myrepo/mychart --set tag=${env.BUILD_NUMBER} -f deployment.yaml'
        }
    }
}
