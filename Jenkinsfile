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
                C:\\Windows\\System32\\inetsrv\\appcmd stop site smswebapp

               echo Deploying files...
               xcopy publish\\* C:\\inetpub\\smswebapp\\ /E /Y /I

               echo Starting IIS site...
               C:\\Windows\\System32\\inetsrv\\appcmd start site smswebapp
             '''
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
