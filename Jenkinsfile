pipeline {
    agent any
    environment{
        SONAR_HOME= tool "Sonar"
    }

    stages {
        stage("Git Clone from GitHub") {
            steps {
                git url: "https://github.com/techimanshu/wanderlust-new.git", branch: "devops-new"
            }
        }
        stage("SonarQube Quality Analysis"){
            steps{
                withSonarQubeEnv("Sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                }
            }
        }
        stage("Owasp Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Sonar Quality Gate SCan") {
            steps {
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan") {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deploy using Docker compose") {
            steps {
                sh "docker-compose up -d"
            }
        }
    }
}
