pipeline {
    agent { label 'ansible'}

    stages {
        stage('Pre-Requisite') {
            steps {
                sh 'echo "jenkins" | sudo -S -k chown jenkins:jenkins /var/run/docker.sock'
                sh "docker stop tomcat9 || true"
                sh "docker rmi tomcat:9.0 || true"
                sh "docker rmi mywebapp || true"
                sh "rm -rf /opt/jenkins/CICD_Pipeline/ROOT.war || true"
                sh "rm -rf /var/lib/jenkins/workspace/CICD_Job/target/sample.war || true"
            }
        }
        stage('Build and Test') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/ShwetKetu/CICD_Pipeline.git'
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                sh 'cp /var/lib/jenkins/workspace/CICD_Job/target/sample.war /opt/jenkins/CICD_Pipeline/ROOT.war'

            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                 //   archiveArtifacts 'target/*.jar'
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "ansible-playbook /opt/jenkins/CICD_Pipeline/build_run_docker.yaml"
            }
        }
    }
}
