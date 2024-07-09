pipeline {
    agent any
    stages {
        stage('User Input') {
            steps {
                script {
                    /* groovylint-disable-next-line LineLength */
                    def userInput = input(message: 'Enter your name:', parameters: [string(defaultValue: 'Mohammed Alkheliwy', description: 'Name')])
                    echo "Hello, ${userInput}!"
                }
            }
        }
    }
}
