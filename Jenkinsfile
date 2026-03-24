post {
    success {
        emailext (
            subject: "SUCCESS: Jenkins Pipeline - ${JOB_NAME}",
            body: """
            Build Successful!

            Job Name: ${JOB_NAME}
            Build Number: ${BUILD_NUMBER}
            Build URL: ${BUILD_URL}

            Docker Image: ${DOCKER_HUB}/${IMAGE_NAME}:${IMAGE_TAG}
            """,
            to: 'veeraprasad.koduri@gmail.com'
        )
    }

    failure {
        emailext (
            subject: "FAILED: Jenkins Pipeline - ${JOB_NAME}",
            body: """
            Build Failed!

            Job Name: ${JOB_NAME}
            Build Number: ${BUILD_NUMBER}
            Build URL: ${BUILD_URL}

            Please check Jenkins logs.
            """,
            to: 'veeraprasad.koduri@gmail.com'
        )
    }
}
