pipeline{
    agent any
    stages{
        stage("Build"){
            steps{
                echo "building app"
                sh './gradlew build'
                archiveArtifacts artifacts: 'dist/trainschedule.zip', fingerprint: true
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
                        publisher: [
                            sshPublisherDesc(
                                configName: "staging",
                                sshCredentials: [
                                    username: '$USERNAME',
                                    encryptedPassphrase: '$PASSWORD'
                                ],
                                transfers: [
                                    sourceFiles: 'dist/trainschedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainschedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
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
                        publisher: [
                            sshPublisherDesc(
                                configName: "production",
                                sshCredentials: [
                                    username: '$USERNAME',
                                    encryptedPassphrase: '$PASSWORD'
                                ],
                                transfers: [
                                    sourceFiles: 'dist/trainschedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainschedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
