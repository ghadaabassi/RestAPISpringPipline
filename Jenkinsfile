pipeline {
    agent any
    tools {
        maven 'maven'
    }
      environment {
        NEXUS_URL = 'http://192.168.209.8:8081/repository/maven-nexus-repo/'
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
                sh 'git clone https://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@github.com/ghadaabassi/RestAPISpringPipline.git'
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

                        filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                        if (filesByGlob.size() == 0) {
                            error "No artifact found in target directory for packaging type ${pom.packaging}"
                        }

                        artifactPath = filesByGlob[0].path;
                        echo "Uploading artifact: ${artifactPath} with groupId: ${pom.groupId}, artifactId: ${pom.artifactId}, version: ${pom.version}"
                        nexusArtifactUploader(
                            nexusVersion: 'nexus2',
                            protocol: 'http',
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: 'nexus-repo',
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
            

    

