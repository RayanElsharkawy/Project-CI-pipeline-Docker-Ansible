pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git(
                    url: 'https://github.com/RayanElsharkawy/Project-CI-pipeline-Docker-Ansible.git',
                    credentialsId: 'github-credentials',
                    branch: 'main'
                )
            }
        }
        stage('Build and Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'mypass', usernameVariable: 'myuser')]) {
                    sh """
                        docker build -t mohamedemadeldin101/my-weather-app-image .
                        docker login -u ${myuser} -p ${mypass}
                        docker push mohamedemadeldin101/my-weather-app-image
                    """
                }
            }
        }
        stage('CD') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'mypass', usernameVariable: 'myuser')]) {
                    sh """
                        docker login -u ${myuser} -p ${mypass}
                        docker run -d -p 5000:5000 --name rayanelsharkawy/python-app  rayanelsharkawy/python-app:V01
                    """
                }
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                 
                // Debugging step to check the contents of the workspace
                sh 'ls -l'  // List files to ensure everything is present
                
                sh """
                    chmod 600 ansible-test.pem
                    export ANSIBLE_HOST_KEY_CHECKING=False
                    ansible-playbook -i inventory.ini deploy_playbook.yml
                """
            }
        }
    }
    post {
        always {
            sh 'docker logout'
            cleanWs()
        }
    }
}
