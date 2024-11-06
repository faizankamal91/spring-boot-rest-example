pipeline{
    agent any
    tools {
        maven "mymaven"
        jdk "myjdk"
    }

    environment {
        NEXUS_URL = '192.168.56.104'
        NEXUS_REPO = 'SonarDemoRepo'
        NEXUS_CREDENTIALS_ID = 'nexus'

        SLACK_CHANNEL = '#devopscicd'
    }

    stages{
        stage("Fetch Code"){
            steps{
                echo "========executing A========"
                git branch: 'main', url:'https://github.com/faizankamal91/spring-boot-rest-example.git'
            }
        }
        
        stage("Unit Test"){
            steps{
                echo "========executing Testing========"
                sh 'mvn test'
            }
        }

        stage("Build"){
            steps{
                echo "========executing build========"
                sh 'mvn install -DskipTests'
            }
            post{
                success {
                    echo "========Success========"
                }
                failure {
                    echo "========Success========"
                }
            }
        }

        stage("Code Analysis"){
            steps{
                echo "========executing Testing========"
                sh 'mvn test'
            }
        }

        stage("Checkstyle Analysis"){
            steps{
                echo "========executing Testing========"
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage("SonarQube Analysis"){
            environment {
                scannerHome = tool 'mysonar'
            }
            steps{
                echo "========executing Testing========"
                withSonarQubeEnv('mysonarurl') {
                sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=springrest \
                   -Dsonar.projectName=springrest \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/javatpoint/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }

        stage("Quality Gate"){
            steps{
                echo "========Quality gate========"
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                echo "======== Upload to Nexus ========"
                nexusArtifactUploader(
                    nexusVersion:'nexus3',
                    protocol: 'http',
                    nexusUrl: NEXUS_URL,
                    groupId: 'QA',
                    repository: NEXUS_REPO,
                    credentialsId: NEXUS_CREDENTIALS_ID,
                    version: "${env.BUILD_ID}",
                    artifacts: [
                        [
                            artifactId: 'springdemo',
                            file: "target/springdemo-v2.jar",
                            type: 'jar',
                            classifier: ''
                        ]
                    ]
                )
            }
        }
    }

    post {
        always {
            slackSend(
                channel: SLACK_CHANNEL,
                message: "Pipeline completed. More info: <${env.BUILD_URL}|Build Link>",
                color: 'good'
            )
        }
        success {
            slackSend(
                channel: SLACK_CHANNEL,
                message: "Pipeline completed successfully! More info: <${env.BUILD_URL}|Build Link>",
                color: 'good'
            )
        }
        failure {
            slackSend(
                channel: SLACK_CHANNEL,
                message: "Pipeline failed. More info: <${env.BUILD_URL}|Build Link>",
                color: 'danger'
            )
        }
    }
}