pipeline {
    agent any

    environment {
        DOTNET = 'dotnet'
        PUBLISH_DIR = 'C:\\inetpub\\wwwroot\\smswebapp'
    }

    stages {

        stage('Restore') {
            steps {
                bat 'dotnet restore smswebapp/smswebapp.csproj'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build smswebapp/smswebapp.csproj -c Release --no-restore'
            }
        }

        stage('Publish') {
            steps {
                bat '''
                if exist publish rmdir /s /q publish
                dotnet publish smswebapp/smswebapp.csproj -c Release -o publish
                '''
            }
        }

        stage('Deploy to IIS') {
            steps {
                bat '''
                echo Stopping IIS site...
                %windir%\\system32\\inetsrv\\appcmd stop site "Default Web Site"

                if exist "%PUBLISH_DIR%" rmdir /s /q "%PUBLISH_DIR%"
                mkdir "%PUBLISH_DIR%"

                xcopy publish "%PUBLISH_DIR%" /E /I /Y

                echo Starting IIS site...
                %windir%\\system32\\inetsrv\\appcmd start site "Default Web Site"
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful ✅'
        }
        failure {
            echo 'Deployment failed ❌'
        }
    }
}
