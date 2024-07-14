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
            steps {
                script {
                    def userInput = input(
                        message: 'Specify Database Configuration',
                        parameters: [
                            string(defaultValue: 'ABSHER2_DB', description: 'Would you like to change the database name?', name: 'DB_name', trim: true),
                            string(defaultValue: '172.31.200.14', description: 'Would you like to change the IP?', name: 'DB_ip', trim: true),
                            string(defaultValue: '50901', description: 'Would you like to change the port?', name: 'DB_port', trim: true)
                        ]
                    )
                    
                    // Extract user input values
                    def DB_name = userInput.DB_name
                    def DB_ip = userInput.DB_ip
                    def DB_port = userInput.DB_port
                    
                    // Define XML file path
                    def XML_FILE = "C:\\Users\\malkheliwy\\Desktop\\serverConf.xml"
                    
                    // Execute script using bat
                    bat '''
                    @echo off
                    setlocal
                    
                    set "XML_FILE=C:\\Users\\malkheliwy\\Desktop\\serverConf.xml"
                    
                    echo Checking specific database configuration in "%XML_FILE%"
                    powershell -Command ^
                        "$xmlContent = Get-Content \"%XML_FILE%\" -Raw; " ^
                        "$xml = [xml]$xmlContent; " ^
                        "$db = $xml.serverConfig.Database | Where-Object { $_.databaseName -eq 'ABSHER2_DB' }; " ^
                        "if ($db) { " ^
                            "$db.databaseName = [string]\"$env:DB_name\"; " ^
                            "$db.databaseIP = [string]\"$env:DB_ip\"; " ^
                            "$db.databasePort = [string]\"$env:DB_port\"; " ^
                            "$xml.Save(\"$env:XML_FILE\"); " ^
                        "} else { " ^
                            "Write-Host 'Database with name ABSHER2_DB not found.'; " ^
                        "}"
                    
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
