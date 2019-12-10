pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -Dversion=${BUILD_NUMBER} -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test -Dversion=${BUILD_NUMBER}'
            }
            post {
                always {
                    tapdTestReport frameType: 'JUnit', onlyNewModified: true, reportPath: 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Quality'){
            environment{
                scannerHome = tool 'default-sonar-scanner'
                -sonar-scanner'
            }
            steps{
                withSonarQubeEnv('DevOpsSonarQube'){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage('Package'){
            steps{
                nexusPublisher nexusInstanceId: 'test-nexus', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/example-app-${BUILD_NUMBER}.jar']], mavenCoordinate: [artifactId: 'example-app', groupId: 'com.example.app', packaging: 'jar', version: '${BUILD_NUMBER}']]]
            }
        }
        stage('Deliver') {
            steps {
                sh 'sh ./deliver.sh'
            }
        }
    }
}
