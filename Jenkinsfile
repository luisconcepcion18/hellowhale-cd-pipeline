pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
		sh 'tar -cvzf html.tar.gz *'
		archiveArtifacts artifacts: 'html.tar.gz'
                
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
                                        sourceFiles: 'html.tar.gz',
                                       // removePrefix: 'home/jenkins/workspace/hellowhale-cd_master/',
                                        remoteDirectory: '/tmp',
                                        execCommand: '/etc/init.d/apache2 stop && rm -rf /var/www/html/* && mv /tmp/html.tar.gz /var/www/html/ && tar -xvzf /var/www/html/html.tar.gz && /etc/init.d/apache2 start'
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
                                        sourceFiles: 'html.tar.gz',
                                        //removePrefix: '/home/jenkins/workspace/hellowhale-cd_master/',
                                        remoteDirectory: '/tmp',
					execCommand: '/etc/init.d/apache2 stop && rm -rf /var/www/html/* && mv /tmp/html.tar.gz /var/www/html/ && tar -xvzf /var/www/html/html.tar.gz && /etc/init.d/apache2 start'
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
