pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
		sh 'tar -cvzf html.tar.gz *' //Se comprime el contenido descargado del SVN y se guarda como un artefacto.
		archiveArtifacts artifacts: 'html.tar.gz'
                
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'staging' //Se especifica el branch de git correspondiente al ambiente de pruebas o staging
            }
            steps {
		//webserver_login es una variable que fue creada dentro de Jenkins y que contiene las credenciales ssh de los servidores staging y production
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
                                        remoteDirectory: '/tmp/', //directorio donde se van a transferir los archivos en el servidor remoto
                                        execCommand: '/etc/init.d/apache2 stop && rm -r /var/www/html/* && tar -xvzf /tmp/html.tar.gz --directory /var/www/html/ && /etc/init.d/apache2 start && rm /tmp/html.tar.gz'
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
                input 'Does the staging environment look OK?' // El deploy de la aplicacion debe ser aceptada en Jenkins.
                milestone(1) //Todos los builds van en orden
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
					execCommand: '/etc/init.d/apache2 stop && rm -r /var/www/html/* && tar -xvzf /tmp/html.tar.gz --directory /var/www/html/ && /etc/init.d/apache2 start && rm /tmp/html.tar.gz'
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
