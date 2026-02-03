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
                    # Configure git identity from environment
                    git config user.email "${GIT_COMMITTER_EMAIL:-jenkins@local}"
                    git config user.name  "${GIT_COMMITTER_NAME:-jenkins}"

                    # Show existing manifest
                    cat deployment.yaml

                    # Update image tag
                    sed -i 's+rakhi12345/test.*+rakhi12345/test:'"$DOCKERTAG"'+g' deployment.yaml

                    # Show updated manifest
                    cat deployment.yaml

                    # Commit changes
                    git add deployment.yaml
                    git commit -m "Done by Jenkins Job changemanifest: ${BUILD_NUMBER}" || echo "No changes to commit"

                    # Push using PAT via HTTP header (no hardcoding)
                    git config --global http.https://github.com/.extraheader \
                    "AUTHORIZATION: basic $(echo -n x-access-token:${GIT_TOKEN} | base64)"

                    git push origin HEAD:main
                '''
            }
        }
    }
}

}
