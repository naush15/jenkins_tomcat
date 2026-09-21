pipeline {
    agent any

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Tool Install') {
            steps {
                sh 'mvn -version'
            }
        }

        stage('Clean Project') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('Build Project') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'tomcat-credentials',
                        usernameVariable: 'TOMCAT_USER',
                        passwordVariable: 'TOMCAT_PASS'
                    )
                ]) {
                    sh '''
                        curl --upload-file target/java-tomcat-maven-example.war \
                        -u "$TOMCAT_USER:$TOMCAT_PASS" \
                        "http://localhost:7080/manager/text/deploy?path=/java-tomcat-maven-example&update=true"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Application successfully deployed to Tomcat!'
        }
    }
}
