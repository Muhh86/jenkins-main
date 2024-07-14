@Library ('jenkins-shared-lib')_
import getMyName.getMyName

pipeline {
    agent any
    
    parameters {
        string(name: 'Fname', defaultValue: '', description: 'Enter your first name')
        string(name: 'Lname', defaultValue: '', description: 'Enter your last name')
        booleanParam(
            name: 'SHOW_NAME', 
            defaultValue: false, 
            description: 'Check this box if you want to display the name'
        )
    }
    
    stages {
        stage('Input Decision') {
            steps {
                script {
                    // Based on user input, decide whether to display the name or not.
                    def x = new getMyName()
                    echo x.MyName()
                    // getMyName.MyName()
                    if (params.SHOW_NAME) {
                        echo "Name: ${params.Fname}  ${params.Lname}"
                    } else {
                        echo "Name display skipped."
                    }
                }
            }
        }
        stage('DB & User Input'){
            steps{
                script{
                    bat '''
                    @echo off
                    setlocal

                    set XML_FILE="%malkheliwy%\\Desktop\\serverConfig.xml"

                    echo Checking database configurations in %XML_FILE%
                    powershell -Command ^
                        "([xml](Get-Content %XML_FILE%)).serverConfig.Database | ForEach-Object { " ^
                        "Write-Host 'databaseName: ' $_.databaseName;" ^
                        "Write-Host 'databaseIP:   ' $_.databaseIP;" ^
                        "Write-Host 'databasePort: ' $_.databasePort;" ^
                        "Write-Host '' }"

                    endlocal
                    '''
                    
                }
            }
        }
        stage('run commands'){
            steps{
                
                getHelloWorld()
                
                bat """
                echo ${params.Fname} ${params.Lname} >> names.txt
                """
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
