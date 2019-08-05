pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '~/hellowhale-cd-pipeline/html',
                                        removePrefix: '~/hellowhale-cd-pipeline/',
                                        remoteDirectory: '/tmp',
                                        execCommand: '/etc/init.d/apache2 stop && rm -rf /var/www/html/* && cp -r /tmp/html/* -d /var/www/html/ && /etc/init.d/apache2 start'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '~/hellowhale-cd-pipeline/html',
                                        removePrefix: '~/hellowhale-cd-pipeline/',
                                        remoteDirectory: '/tmp',
                                        execCommand: '/etc/init.d/apache2 stop && rm -rf /var/www/html/* && cp -r /tmp/html/* -d /var/www/html/ && /etc/init.d/apache2 start'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
}
