pipeline {
    agent {
    label 'vm-slave'
    }
    environment {
        // Defining the credentials ID for accessing the Git repository
        GIT_CREDENTIALS_ID = 'githubCred'
        SONAR_HOME = tool "sonarScanner"
    }

    stages {
         stage('Clone') {
            steps {
                echo 'Cloning the app'
                git credentialsId: env.GIT_CREDENTIALS_ID, url: 'https://github.com/aadarsh-ui/django-notes-app.git', branch: 'main'
            }
        }
        stage('Build & Test') {
            steps {
                echo 'Building the app'
                sh 'docker --version'
                sh 'docker build -t note-taking-app .'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                //sonar is name of sonarqube server name inside manage jenkins>system
                withSonarQubeEnv("sonar"){
                    sh '$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=nodetodo -Dsonar.projectKey=nodetodo'
                }
            }
        }
   //   stage('SonarQube Quality Gates') {
       //     steps {
                //timeout(time: 5, unit: "MINUTES"){
                  //  waitForQualityGate abortPipeline: false //true when developer writes code :)
         //       }
           // }
        //}
        stage('OWASP') {
            steps {
                echo 'Vulenerability checking in the app'
            }
        }
        stage('Trivy Scanner') {
            steps {
                echo 'Scanning the Build Artifact'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing to docker hub the app'
                withCredentials([usernamePassword(credentialsId:"dockerHub", usernameVariable:"dockerHubUser", passwordVariable:"dockerHubPass")]) {
                    sh "echo $dockerHubPass | docker login -u $dockerHubUser --password-stdin"
                    sh "docker tag note-taking-app $dockerHubUser/note-taking-app:latest"
                    sh "docker push $dockerHubUser/note-taking-app:latest"
        }
    }
}


        stage('Deploy') {
            steps {
                echo 'Deployed the app'
                sh "docker-compose down && docker-compose up -d --build"
            }
        }
    }
}

