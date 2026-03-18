pipeline {
    agent {
        label 'docker'
    }

    environment {
        JAVA_HOME = '/opt/java/openjdk'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
        SONARQUBE = 'http://localhost:9000'
    }

    stages {

        stage('Check env') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
            }
        }

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/mailong245/demo-jenkins'
            }
        }

        stage('Unit Test') {
            steps {
                sh "mvn clean verify"
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh """
                      mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=demo-jenkins \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=sqp_9e1fc6941e8c0696f1e908ccb65da17571a183ab

                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}