pipeline{
    agent any
    tools {
        maven "mymaven"
        jdk "myjdk"
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
                echo "========Success========"
            }
        }
    }
    
}