pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
		sh 'tar -cvzf /home/jenkins/workspace/hellowhale-cd_master/html.tar.gz *'
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
                                        sourceFiles: '/home/jenkins/workspace/hellowhale-cd_master/html.tar.gz',
                                        removePrefix: '/home/jenkins/workspace/hellowhale-cd_master/',
                                        remoteDirectory: '/tmp',
                                        execCommand: '/etc/init.d/apache2 stop && rm -rf /var/www/html/* && tar -xvzf /tmp/html.tar.gz /tmp/ && cp -r /tmp/html/* -d /var/www/html/ && /etc/init.d/apache2 start'
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
                                        sourceFiles: '/home/jenkins/workspace/hellowhale-cd_master/html.tar.gz',
                                        removePrefix: '/home/jenkins/workspace/hellowhale-cd_master/',
                                        remoteDirectory: '/tmp',
					execCommand: '/etc/init.d/apache2 stop && rm -rf /var/www/html/* && tar -xvzf /tmp/html.tar.gz /tmp/ && cp -r /tmp/html/* -d /var/www/html/ && /etc/init.d/apache2 start'
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
