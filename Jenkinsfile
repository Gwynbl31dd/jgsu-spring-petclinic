pipeline {
    agent any
    triggers {
        pollSCM 'H * * * *'
    }

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    stages {
        stage('Checkout'){
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', url: 'https://github.com/Gwynbl31dd/jgsu-spring-petclinic.git'
            }
        }
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=false clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    emailext attachLog: true, 
                    body: 'Please go to \'${BUILD_URL}\' and verify the build', 
                    compressLog: true, 
                    recipientProviders: [requestor()], 
                    to: "test@jenkins",
                    subject: 'Job \'${JOB_NAME}\' (${BUILD_NUMBER}) is waiting for input'
                }
                success {
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}
