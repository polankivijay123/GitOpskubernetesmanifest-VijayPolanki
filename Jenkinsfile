node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Update GIT') {
    script {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {

            withCredentials([string(credentialsId: 'github-pat', variable: 'GIT_TOKEN')]) {

                sh '''
                    set -e

                    git config user.email "${GIT_COMMITTER_EMAIL:-jenkins@local}"
                    git config user.name  "${GIT_COMMITTER_NAME:-jenkins}"

                    cat deployment.yaml

                    sed -i 's+rakhi12345/test.*+rakhi12345/test:'"$DOCKERTAG"'+g' deployment.yaml

                    cat deployment.yaml

                    git add deployment.yaml
                    git commit -m "Done by Jenkins Job changemanifest: ${BUILD_NUMBER}" || echo "No changes to commit"

                    AUTH_HEADER=$(echo -n "x-access-token:${GIT_TOKEN}" | base64)

                    git -c http.extraheader="AUTHORIZATION: basic ${AUTH_HEADER}" \
                        push https://github.com/$(git config --get remote.origin.url | sed 's#https://github.com/##') \
                        HEAD:main
                '''
            }
        }
    }
}


}
