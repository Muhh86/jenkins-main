pipeline {
    agent any
    
    parameters {
        string(name: 'NAME', defaultValue: '', description: 'Enter your name')
    }
    
    stages {
        stage('Input Decision') {
            steps {
                script {
                    // Display input message and options
                    def userInput = input(
                        message: "Do you want to display the name '${params.NAME}'?",
                        parameters: [
                            choice(
                                choices: ['Yes', 'No'],
                                description: 'Choose whether to display the name'
                            )
                        ]
                    )
                    
                    // Based on user input, decide whether to display the name or not
                    if (userInput == 'Yes') {
                        echo "Name: ${params.NAME}"
                    } else {
                        echo "Name display skipped."
                    }
                }
            }
        }
        
        // Add more stages as needed
    }
    
    post {
        always {
            // Clean up or finalize actions
        }
    }
}
