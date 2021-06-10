// Hello-world build pipeline
pipeline {
    agent any

    tools {
        maven "Maven 3.6.3"
    }
    options {
        parallelsAlwaysFailFast()
    }

    stages {
        stage('Build Hello-world') {
            steps {
                // Run the maven build
                sh "mvn clean install package"
            }
        }
        stage('Parallel Stage') {
            parallel {
                stage('Build Hello-world Checkstyle') {
                    steps {
                        // Run the maven build with checkstyle
                        sh "mvn clean package checkstyle:checkstyle"
                    }
                }
                stage('Build Hello-world Sonarqube') {
                    steps {
                        withSonarQubeEnv('SonarQube') {
                            sh "mvn  clean package sonar:sonar -Dsonar.host_url=$SONAR_HOST_URL "
                            }
                        }
                    }
                }
            }
        }
    }
