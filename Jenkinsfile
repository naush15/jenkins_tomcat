pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        MAVEN = '/opt/homebrew/bin/mvn'
        WAR_FILE = 'target/java-tomcat-maven-example.war'
        APP_PATH = '/java-tomcat-maven-example'
        TOMCAT_URL = 'http://localhost:7080'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Tool Install') {
            steps {
                sh '${MAVEN} -version'
                sh 'git --version'
            }
        }

        stage('Clean Project') {
            steps {
                sh '${MAVEN} clean'
            }
        }

        stage('Build Project') {
            steps {
                sh '${MAVEN} package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'tomcat-credentials',
                    usernameVariable: 'TOMCAT_USER',
                    passwordVariable: 'TOMCAT_PASSWORD'
                )]) {
                    sh '''
                        curl --fail --show-error --silent \
                          --user "$TOMCAT_USER:$TOMCAT_PASSWORD" \
                          --upload-file "$WAR_FILE" \
                          "$TOMCAT_URL/manager/text/deploy?path=$APP_PATH&update=true"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Application successfully deployed to Tomcat!'
        }
        failure {
            echo 'Pipeline failed. Check the Console Output.'
        }
    }
}
