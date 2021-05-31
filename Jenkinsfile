pipeline {
    agent any
    stages {
        stage ('Build') {
            steps{
                sh 'mvn clean package'
            }
        }
        stage ('Unit test') {
            steps {
                sh 'mvn test'
            }
        }
        stage ('SonarQube Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL') {
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=Simple-Java -Dsonar.host.url=http://192.168.0.120:9000 -Dsonar.login=f61e2296c7ae27622f895cc6b772b08fe68927ab -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage ('Quality Gate Analysis') {
            steps{
                sleep(15)
                timeout(time: 1, unit:'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }            
            }
        }
        stage ('Deploy Test') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://192.168.0.120:8001/')], contextPath: 'simple', war: 'target/simple.war'
            }
        }
    }

}