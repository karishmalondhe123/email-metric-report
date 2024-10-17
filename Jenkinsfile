pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                // Cloning the master/main branch (make sure to specify the correct branch name)
                git branch: 'main', url: 'https://github.com/karishmalondhe123/email-metric-report.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                sh 'docker build -t ec2-metrics-report .'
            }
        }

        stage('Run Report Generation') {
            steps {
                // Run the Docker container, mounting the .aws directory and reports directory
                sh 'docker run --rm -v /var/lib/jenkins/.aws:/root/.aws -v $WORKSPACE/reports:/app/reports ec2-metrics-report'
            }
        }

        stage('Archive Report') {
            steps {
                // Archive the generated report
                archiveArtifacts artifacts: 'reports/*.xlsx', allowEmptyArchive: true
            }
        }
    }

    post {
        success {
            echo 'Report generated and archived successfully.'
            // Send email with the report as an attachment
            emailext(
                subject: 'EC2 Metrics Report - Build #${BUILD_NUMBER} Ready',
                body: '''<p>The EC2 Metrics Report has been successfully generated.</p>
                         <p>Find the report attached to this email.</p>''',
                to: 'londhekarishma6994@gmail.com, londhe.karishma61@gmail.com',
                attachmentsPattern: 'reports/*.xlsx', // Attach the generated report
                mimeType: 'text/html'
                )
        }
        cleanup {
            // Clean up unused Docker resources
            sh 'docker system prune -f'
        }
    }
}
