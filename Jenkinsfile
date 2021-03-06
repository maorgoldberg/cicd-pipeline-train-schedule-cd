pipeline{
    agent any
    stages{
        stage("Build"){
            steps{
                echo "building app"
                sh './gradlew build'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip', fingerprint: true
            }
        }
        stage("Deploystaging"){
            when{
                branch 'master'
            }
            steps{
                echo 'Deploying to staging server'
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sshPublisher(
                        continueOnError:false,failOnError:true,
                        publishers: [
                            sshPublisherDesc(
                                configName: "staging",
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$PASSWORD"
                                ],
                                transfers: [
                                    sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage("DeployProd"){
            when{
                branch 'master'
            }
            steps{
                input 'does this look ok?'
                milestone(1)
                echo 'Deploying to Prod server'
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sshPublisher(
                        continueOnError:false,failOnError:true,
                        publishers: [
                            sshPublisherDesc(
                                configName: "production",
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$PASSWORD"
                                ],
                                transfers: [
                                    sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
