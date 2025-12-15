pipeline {
    agent any

    environment {
        IIS_PATH = "C:\\inetpub\\smswebapp"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/NehaDugane/smswebapp.git'
            }
        }

        stage('Restore') {
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build --configuration Release'
            }
        }

        stage('Publish') {
            steps {
                bat '''
                if exist publish rmdir /s /q publish
                dotnet publish -c Release -o publish
                '''
            }
        }

        stage('Deploy to IIS') {
            steps {
                bat '''
                echo Stopping IIS site
                %windir%\\system32\\inetsrv\\appcmd stop site smswebapp

                echo Copying files
                xcopy publish "%IIS_PATH%" /E /Y /I

                echo Starting IIS site
                %windir%\\system32\\inetsrv\\appcmd start site smswebapp
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment to IIS successful ✅"
        }
        failure {
            echo "Deployment failed ❌"
        }
    }
}
