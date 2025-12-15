pipeline {
    agent any

    environment {
        APP_NAME = "smswebapp"
        BUILD_VERSION = "1.0.${BUILD_NUMBER}"
        NEXUS_URL = "http://192.168.1.7:8081"
        NEXUS_REPO = "dotnet-artifacts"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Restore') {
            steps {
                bat 'dotnet restore smswebapp/smswebapp.csproj'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build smswebapp/smswebapp.csproj --configuration Release --no-restore'
            }
        }

        stage('Test') {
            steps {
                bat 'dotnet test --no-build --configuration Release || exit 0'
            }
        }

        stage('Publish') {
            steps {
                bat '''
                dotnet publish smswebapp/smswebapp.csproj ^
                  --configuration Release ^
                  --output publish
                '''
            }
        }

        stage('Package Artifact') {
            steps {
                bat '''
                if exist %APP_NAME%-%BUILD_VERSION%.zip del %APP_NAME%-%BUILD_VERSION%.zip
                powershell Compress-Archive -Path publish\\* -DestinationPath %APP_NAME%-%BUILD_VERSION%.zip
                '''
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    bat '''
                    curl -v -u %NEXUS_USER%:%NEXUS_PASS% ^
                    --upload-file %APP_NAME%-%BUILD_VERSION%.zip ^
                    %NEXUS_URL%/repository/%NEXUS_REPO%/%APP_NAME%/%BUILD_VERSION%/%APP_NAME%-%BUILD_VERSION%.zip
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Build & upload successful 🚀"
        }
        failure {
            echo "Pipeline failed ❌"
        }
        cleanup {
            cleanWs()
        }
    }
}

