pipeline {
    agent any 
    stages {
        stage('Test') {
            steps {
                setBuildStatus("${GIT_URL}", "Testing","PENDING","Jenkins CI","${GIT_COMMIT}")
                sh """
                . ${HOME}/opt/python-venv/tox/bin/activate;
                tox
                """
            }
        }
    }
    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir() // clean up our workspace
        }
        success {
            echo 'Succeeeded'
            setBuildStatus("${GIT_URL}", "Complete","SUCCESS","Jenkins CI","${GIT_COMMIT}")
        }
        failure {
            echo 'Failed'
            setBuildStatus("${GIT_URL}", "Complete","FAILURE","Jenkins CI","${GIT_COMMIT}")
        }
    }
}

def setBuildStatus(String gitUrl, String message, String state, String context, String commitId) {
    step([
        $class: "GitHubCommitStatusSetter",
        reposSource: [$class: "ManuallyEnteredRepositorySource", url: gitUrl],
        contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
        errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
        commitShaSource: [$class: "ManuallyEnteredShaSource", sha: commitId],
        statusBackrefSource: [$class: "ManuallyEnteredBackrefSource", backref: "${BUILD_URL}flowGraphTable/"],
        statusResultSource: [$class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
    ]);
}
