pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t playwright-agents-example .'
                }
            }
        }
        
        stage('Run Playwright Tests in Docker') {
            steps {
                script {
                    bat '''
                        docker run --rm ^
                        -v %WORKSPACE%\\test-results:/app/test-results ^
                        -v %WORKSPACE%\\playwright-report:/app/playwright-report ^
                        playwright-agents-example
                    '''
                }
            }
        }
    }
    
    post {
        always {
            // Raporları sakla
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'playwright-report',
                reportFiles: 'index.html',
                reportName: 'Playwright Test Report',
                reportTitles: ''
            ])
            
            // Test sonuçlarını arşivle
            archiveArtifacts artifacts: 'playwright-report/**/*', allowEmptyArchive: true
            archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true
        }
    }
}
