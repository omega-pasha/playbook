pipeline {
    agent {
        label 'linux'
    }
    stages {
        stage("Git clone") {
            steps {
                git branch: 'hw-8-4', credentialsId: 'bca4984b-8781-4b5b-a5f9-51a9f1f0cdcc', url: 'https://github.com/omega-pasha/playbook.git'
            }
        }
        stage("Download molecule"){
            steps {
                sh "pip3 install molecule molecule_docker ansible-lint yamllint"
            }
        }
        stage("run test molecule"){
            steps {
                sh "cd ./roles/clickhouse && ls -lah && molecule test"
            }
        }
    }
}
