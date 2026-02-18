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
                sh 'docker build -t playwright-agents-example .'
            }
        }
        
        stage('Run Playwright Tests in Docker') {
            steps {
                sh '''
                    docker run --rm \
                    -v ${WORKSPACE}/test-results:/app/test-results \
                    -v ${WORKSPACE}/playwright-report:/app/playwright-report \
                    playwright-agents-example
                '''
            }
        }
    }
    
    post {
        always {
            // Test sonuçlarını arşivle
            archiveArtifacts artifacts: 'playwright-report/**/*', allowEmptyArchive: true
            archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true
            
            // Not: HTML report görmek için Jenkins'te HTML Publisher plugin yükleyin
            // Dashboard -> Manage Jenkins -> Plugins -> Available plugins -> HTML Publisher
        }
    }
}
