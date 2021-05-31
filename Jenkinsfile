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
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=Maven-web -Dsonar.host.url=http://192.168.0.120:9000 -Dsonar.login=6c7eaea8d853f2f3db2f982a0e87c741ab477806 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/mvn/**,**/src/test/**,**/model/**,**Application.java"
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
        stage ('Deploy Test ENV') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://192.168.0.120:8001/')], contextPath: 'api', war: 'target/api.war'
            }
        }
        stage ('Deploy Prod ENV') {
            steps {
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }
    }

}