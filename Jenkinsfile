pipeline {
    agent {
        node {
            label 'slave_1'
        }
    }

    tools {
        maven 'MAVEN_HOME'
        jdk 'JAVA_HOME'
    }

    stages {
        stage('Pre-Build Step') {
            steps {
                sh 'echo "Pre Build Step"'
            }
        }

        stage('Git Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/cuty-goud/Maven-project.git']]
                )
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Unit Test') {
            steps {
                echo 'Running Unit Tests'
                sh 'mvn test'
            }
        }

        stage('Static Code Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps {
                echo 'Running SonarQube Analysis'
                withSonarQubeEnv('SONAR_HOME') {
                    sh '${scannerHome}/bin/sonar-scanner'
                }
            }
        }

        stage('Tomcat Deployment') {
            steps {
                script {
                    deploy adapters: [tomcat9(
                        alternativeDeploymentContext: '',
                        credentialsId: 'tomcat-deploy',
                        path: '',
                        url: 'http://3.109.59.60:8080'
                    )],
                    contextPath: '/java17-demo',
                    onFailure: false,
                    war: 'target/java17-demo-1.0.0.war'
                }
            }
        }

        stage('Post-Build Step') {
            steps {
                sh 'echo "Pipeline completed successfully"'
            }
        }
    }
}
