pipeline {
    agent any
    
    parameters {
        string(name: 'NAME', defaultValue: '', description: 'Enter your name')
    }
    
    stages {
        stage('Input Decision') {
            steps {
                script {
                    // Based on user input, decide whether to display the name or not
                    if (userInput) {
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
            echo "Done."
            // Clean up or finalize actions
        }
    }
}
