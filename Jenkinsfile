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
                    sh "mvn clean install"
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


stage("Publish to Nexus") {
    steps {
        dir("RestAPISpringPipline") {
            sh """
                mvn deploy:deploy-file \
                -Durl=${NEXUS_URL} \
                -DrepositoryId=nexus-repo \
                -Dfile=target/backend.jar \
                -DgroupId=com.example \
                -DartifactId=backend \
                -Dversion=1.0.0 \
                -Dpackaging=jar \
                --settings C:\\Users\\Ghada\\.m2\\settings.xml
            """
        }
    }
}

    }
}
