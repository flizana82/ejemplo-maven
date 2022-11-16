pipeline {
    agent any
     environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://nexus:3002"
        NEXUS_REPOSITORY = "devops-usach-nexus"
        NEXUS_CREDENTIAL_ID = "jenkins-nexus"
    }
    stages {
        stage('compile') {
            steps {
                echo 'Source code compilation in progress.....'
                script {
                    if(isUnix()) {
                        echo 'Unix OS'
                        sh './mvnw clean compile -e'
                    } else {
                        echo 'Windows OS'
                        bat 'mvnw clean compile -e'
                    }
                }
                echo '.....Source code compilation completed'
            }
        }
        stage('test') {
            steps {
                echo 'Source code testing in progress.....'
                script {
                    if(isUnix()) {
                        echo 'Unix OS'
                        sh './mvnw clean test -e'
                    } else {
                        echo 'Windows OS'
                        bat 'mvnw clean test -e'
                    }
                }
                echo '.....Source code testing completed'
            }
        }
        stage('package') {
            steps {
                echo 'Source code packaging in progress.....'
                script {
                    if(isUnix()) {
                        echo 'Unix OS'
                        sh './mvnw clean package -e'
                    } else {
                        echo 'Windows OS'
                        bat 'mvnw clean package -e'
                    }
                }
                echo '.....Source code packaging completed'
            }
        }
        stage('sonar') {
            steps {
                echo 'Sonar scan in progress.....'
                withSonarQubeEnv(credentialsId: 'jenkins-sonar', installationName: 'sonar-jenkins') {
                    script {
                        if(isUnix()) {
                            echo 'Unix OS'
                                sh './mvnw clean verify sonar:sonar \
                                     -Dsonar.projectKey=example-maven2'
                        } else {
                            echo 'Windows OS'
                                bat 'mvnw clean verify sonar:sonar \
                                    -Dsonar.projectKey=example-maven2'
                        }
                        echo '.....Sonar scan completed'
                    }
                }
            }
        }
        stage("uploadNexus") {
            steps {
                echo 'Uploading to nexus in progress.....'
                script {
                    pom = readMavenPom file: "pom.xml";
                    files = findFiles(glob: "build/*.${pom.packaging}");
                    artifactPath = files[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                        /*nexusPublisher(
                            nexusInstanceId: NEXUS_INSTANCE_ID,
                            nexusRepositoryId: NEXUS_REPOSITORY,
                            packages: [
                                [
                                    $class: 'MavenPackage',
                                    mavenAssetList: [
                                        [classifier: '',
                                        extension: '0.0.1',
                                        filePath: artifactPath]
                                        ],
                                    mavenCoordinate:
                                        [artifactId: pom.artifactId,
                                        groupId: pom.groupId,
                                        packaging: pom.packaging,
                                        version: pom.version]
                                 ]
                            ]
                        )*/
                    echo '.....Artifact Uploaded successfully'
                    } else {
                        error "File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
        /*stage('download & test') {
            steps {
                script {
                    echo "Downloading artifact from nexus"
                }
            }
        }*/
    }
 }