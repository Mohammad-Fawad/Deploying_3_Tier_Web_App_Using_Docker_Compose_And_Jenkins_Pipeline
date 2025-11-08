pipeline {
    agent { label 'ubuntu3' }

    environment {
        // Only non-secret variables are defined here
        DOCKER_COMPOSE_CMD = 'docker-compose'
        REPO_URL = 'https://github.com/MUGHEESULHASSAN/Deploying_3_Tier_Web_App_Using_Docker_Compose_And_Jenkins_Pipeline.git'
        BRANCH = 'main'
    }

    stages {
        // --- STAGE 1: Checkout Code ---
        stage('Checkout Code') {
            steps {
                echo "Cloning repository: ${env.REPO_URL}"
                // This step ensures the ./backend/.env file is in the workspace
                git branch: env.BRANCH, url: env.REPO_URL
            }
        }

        // --- STAGE 2: Deploy Application (No more withEnv needed) ---
        stage('Deploy Application') {
            steps {
                script {
                    echo 'Stopping old containers...'
                    sh "${env.DOCKER_COMPOSE_CMD} down || true" 

                    echo 'Running new containers using local .env file...'
                    
                    // FIX: Removed the entire withEnv block.
                    // The command now runs directly, and Docker Compose reads the .env file.
                    sh "bash -c '${env.DOCKER_COMPOSE_CMD} up -d --pull always'"
                }
            }
        }

        // --- STAGE 3: Verify Deployment ---
        stage('Verify Deployment') {
            steps {
                echo 'Checking running containers...'
                sh 'docker ps'
            }
        }
    }

    // --- POST: Cleanup ---
    post {
        always {
            echo 'Cleaning up containers after build...'
            sh "${env.DOCKER_COMPOSE_CMD} down"
        }
    }
}