// Hello-world build pipeline
pipeline {
    agent any

    tools {
        maven "Maven 3.6.3"
    }
    stages
    {
        stage('Build Hello-world') {
           steps{
              // Run the maven build
              sh "mvn clean install package"
           }
       }
       stage('Build Hello-world Checkstyle') {
                  steps{
                     // Run the maven build with checkstyle
                     sh "mvn clean package checkstyle:checkstyle"
                  }
               }
    }
}