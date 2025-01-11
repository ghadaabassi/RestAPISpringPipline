pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        NEXUS_URL = 'http://0.0.0.0:8081/repository/maven-nexus-repo/'
        NEXUS_CREDENTIALS = credentials('nexus-cred')
    }
    stages {
        stage("Clean up") {
            steps {
                deleteDir()
            }
        }
        stage("Clone repo") {
            steps {
                sh 'git clone https://github.com/ghadaabassi/RestAPISpringPipline.git'
            }
        }
        stage("Generate backend image") {
            steps {
                dir("RestAPISpringPipline") {
                    sh "mvn clean package"
                    sh "docker build -t backend ."
                }
            }
        }
        stage("Run docker compose") {
            steps {
                dir("RestAPISpringPipline") {
                    sh "docker compose up -d" 
                }
            }
        }
        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv("sonar-server") { 
                    dir("RestAPISpringPipline") { 
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                dir("RestAPISpringPipline") {
                    script {
                        pom = readMavenPom file: "pom.xml"
                        
                        filesByGlob = findFiles(glob: "target/*.${pom.packaging}")
                        if (filesByGlob.size() == 0) {
                            error "No artifact found in target directory for packaging type ${pom.packaging}"
                        }

                        artifactPath = filesByGlob[0].path
                        echo "Uploading artifact: ${artifactPath} with groupId: ${pom.groupId}, artifactId: ${pom.artifactId}, version: ${pom.version}"

                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: '0.0.0.0:8081',
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: 'maven-nexus-repo',
                            credentialsId: 'nexus-cred',
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
                        )
                    }
                }
            }
        }
    }
}
