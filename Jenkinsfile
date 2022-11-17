pipeline {
    agent any
    stages {
        stage('Compile') {
            steps {
                script {
                    sh './mvnw clean compile -e'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh './mvnw clean test -e'
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    sh './mvnw clean package -e'
                }
            }
        }
        stage('SonarQube') {
            steps {
                withSonarQubeEnv(credentialsId: 'jenkins-sonar', installationName: 'sonar-jenkins') {
                    script {
                        sh './mvnw clean verify sonar:sonar \
                                -Dsonar.projectKey=example-maven2'
                    }
                }
            }
        }
        stage('Upload Nexus') {
            steps {
                script {
                    nexusPublisher nexusInstanceId: 'nxs01', nexusRepositoryId: 'ejercicio-clase4-mod4', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'build/DevOpsUsach2020-1.0.0.jar']], mavenCoordinate: [artifactId: 'DevOpsUsach2020', groupId: 'com.devopsusach2020', packaging: 'jar', version: '1.0.0']]]
                }
            }
        }
        stage('Download') {
            steps {
                script {
                    sh 'curl -X GET -u admin:admin http://nexus:8081/repository/ejercicio-clase4-mod4/com/devopsusach2020/DevOpsUsach2020/1.0.0/DevOpsUsach2020-1.0.0.jar -O '
                    sh 'ls -ltr'
                }
            }
        }
    }
}