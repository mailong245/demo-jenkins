pipeline {
    agent {
        label: 'docker'
    }

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk'  // update with correct path
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
                      mvn sonar:sonar \
                        -Dsonar.projectKey=demo-jenkins \
                        -Dsonar.host.url=http://localhost:9000
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