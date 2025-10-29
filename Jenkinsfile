pipeline {
    agent any

    tools {
        jdk 'java'   // your configured JDK in Jenkins
        maven 'Maven'
    }

    environment {
        EC2_USER = 'ubuntu'
        EC2_HOST = '172.31.22.94'
        PEM_KEY = '/var/lib/jenkins/.ssh/jenfile'   // your EC2 PEM file
        JAR_NAME = 'ekart-ecommerce-backend-0.0.1-SNAPSHOT.jar'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/tsri010203/eKart-ecommerce-backend.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                # Copy the new jar to EC2
                scp -i ${PEM_KEY} -o StrictHostKeyChecking=no target/${JAR_NAME} ${EC2_USER}@${EC2_HOST}:/home/ubuntu/

                # Start the jar in background and detach SSH
                ssh -i ${PEM_KEY} -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} \
                    'nohup java -jar /home/ubuntu/${JAR_NAME} >/dev/null 2>&1 & exit'
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
