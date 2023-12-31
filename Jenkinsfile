node {
    stage('Build') {
        docker.image('node:16-buster-slim').inside('-p 3000:3000') {
            sh 'npm install'
        }
    }
    stage('Test') {
        docker.image('node:16-buster-slim').inside('-p 3000:3000') {
            sh './jenkins/scripts/test.sh'
        }
    }
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    stage('Deploy') {
        try {
            sshPublisher(
                publishers : [
                    sshPublisherDesc(
                        configName: "ec2-react-app",
                        verbose: true,
                        transfers: [
                            sshTransfer(
                                sourceFiles: 'build/**',
                                removePrefix: 'build/',
                            ),
                            sshTransfer(execCommand: 'docker run -d -p 3000:3000 -v $(pwd):/app --name react-app node:16-buster-slim sh -c "cd /app/react-app && npm install -g serve && serve -l 3000"')
                        ]
                    )
                ],
                continueOnError: false, 
                failOnError: true,
            )
        } catch (err) {
            currentBuild.result = 'FAILED'
            throw err
        } finally {
            def currentResult = currentBuild.result ?: 'SUCCESS'
            if (currentResult == 'SUCCESS') {
                sleep(time: 1, unit: 'MINUTES')
                // sshPublisher(
                //     publishers : [
                //         sshPublisherDesc(
                //             configName: "ec2-react-app",
                //             verbose: true,
                //             transfers: [
                //                 sshTransfer(execCommand: 'docker container stop react-app && docker container rm react-app && rm -rf ./*')
                //             ]
                //         )
                //     ],
                //     continueOnError: false, 
                //     failOnError: true,
                // )
            } else {
                sh 'echo "Build failed, skipping deploy"'
            }
        }
    }
}
