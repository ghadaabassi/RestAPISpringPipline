pipeline {
    agent any
    tools {
        maven 'maven'
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


    }
}
