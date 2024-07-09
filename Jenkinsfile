pipeline {
    agent any
    stages {
        stage('User Input') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput', message: 'Enter your name:',
                        parameters: [string(defaultValue: 'Mohammed', description: 'Name')]
                    )
                    echo "User input: ${userInput}"
                }
            }
        }
    }
}
