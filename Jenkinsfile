pipeline {
    agent any
    tools {nodejs "node"}
    stages {
        stage('Git-Checkout'){
            steps{
                echo "Checkout from git"
                git 'https://github.com/mrvincentoti/cicd-pipeline-image-filter-api.git'
            }
        }

        stage('Create Archive'){
            steps {
                echo "Compile Backend"
                sh 'rm -rf www && mkdir www'
                sh 'zip -r www/Archive.zip . -x www/** -x node_modules/**'
            }
        }

        stage('DeployToStaging') {
            // when {
            //     branch 'master'
            // }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
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
                                        sourceFiles: 'www/Archive.zip',
                                        removePrefix: 'www/',
                                        remoteDirectory: '/home/cloud_user/vincent',
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }

        // stage('JUnit'){
        //     steps {
        //         echo "JUnit Passed Successfully"
        //     }
        // }

        // stage('Quality-Gate'){
        //     steps {
        //         echo "SonarQube Quality Check Passed Successfully"
        //     }
        // }

        // stage('Deploy'){
        //     steps {
        //         echo "Passed Successfully"
        //     }
        // }
    }
    post {
        always{
            echo "This will always run"
        }
        success{
            echo "This will only run on success"
        }
        failure{
            echo "This will run on failure"
        }
        unstable{
            echo "This will only run if the run was marked unstable"
        }
        changed{
            echo "This will only run if the stage in the pipeline has changed"
            echo "For example if the pipeline was initially failing, but now successful"
        }
    }
}