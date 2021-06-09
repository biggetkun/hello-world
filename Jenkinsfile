pipeline {
    agent any
  tools {
        maven "Maven 3.6.3"
    }
  options {
        parallelsAlwaysFailFast()
    }
    stages {
        stage('Hello-world maven') {
            steps {
                git 'https://github.com/crunchy-devops/hello-world.git'
                sh "mvn clean install package"
                archiveArtifacts artifacts: '**/*.war', followSymlinks: false
            }
        }
        stage('Hello-world sonar') {
            steps {
                withSonarQubeEnv('SonarQube') {
                sh "mvn  clean package sonar:sonar -Dsonar.host_url=$SONAR_HOST_URL "
                }
            }
        }
        stage('Hello-world Nexus') {
            steps {
                nexusPublisher nexusInstanceId: 'Nexus', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'webapp/target/webapp.war']], mavenCoordinate: [artifactId: 'maven-project', groupId: 'com.example.maven-project', packaging: 'war', version: '1.1']]]
                }
        }
        stage('Hello-world Docker build') {
            steps {
                sh " rm -Rf webapp.war && \
                     wget http://nexus:8081/repository/maven-releases/com/example/maven-project/maven-project/1.1/maven-project-1.1.war -O ${WORKSPACE}/webapp.war && \
                     docker build -t hello-world-afip:latest ."
                }
        }
        stage('Hello-world Docker Test') {
            steps {
                script {
                def set_container = sh(script: ''' CONTAINER_NAME="hello-world-run"
                                                   OLD="$(docker ps --all --quiet --filter=name="$CONTAINER_NAME")"
                                                   if [ -n "$OLD" ]; then
                                                    docker rm -f $OLD
                                                   fi
                                                   docker run -d --name hello-world-run -p 8090:8080 hello-world-afip
                                            ''')
                }
            }
        }
        stage('Parallel Stage') {
            parallel {
                stage('Hello-world jmeter') {
                    steps {
                        sh "jmeter -Jjmeter.save.saveservice.output_format=xml -Jjmeter.save.saveservice.response_data.on_error=true -n -t jmeter_test_plan.jmx  -l testresult.jlt"
                    }
                }
                stage('Hello-world Selenium') {
                steps {
                        git 'https://github.com/crunchy-devops/hello-world.git'
                        sh "mvn test"
                    }
                }

            }
        }

    }
}

// Hello-world build pipeline
pipeline {
    agent any

    tools {
        maven "Maven 3.6.3"
    }
    stages {
        stage('Build Hello-world') {
           steps{
              // Run the maven build
              sh "mvn clean install package"
           }
        }
    }
}
