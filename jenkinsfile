pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage("Pull SRC") {
            steps {
                 git branch: 'main', url: 'https://github.com/sinchanar1231/docker-ansible-terraform.git'
            }
        }
        stage("move the target") {
            steps {
                sh 'mv target/terraform.war .'
            }
        }
        stage("Prepare Build") {
            steps {
                sh 'mvn clean package'
            }
        }
        stage("build docker image"){
            steps{
                sh 'docker build -t app .'
            }
        }
        stage("tag image"){
            steps{
                sh 'docker tag app sinchana1231/webapp:latest'
            }
        }
        stage("push image"){
            steps{
               sh 'echo "Mydocker@12"| docker login -u sinchana1231 --password-stdin'
                sh 'docker push sinchana1231/webapp:latest'
            }
        }
        stage("remove images locally"){
            steps{
                sh 'docker rmi  app'
            }
        }
        
        stage("run ansible playbook") {
            steps {
                sshPublisher(
                    continueOnError: false, 
                    failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName: "remote",
                            transfers: [
                                sshTransfer(sourceFiles: 'play.yml'),
                                sshTransfer(execCommand: "ansible-playbook play.yml")
                            ],
                            verbose: true
                        )
                    ]
                )
            }
        }
    }
}
