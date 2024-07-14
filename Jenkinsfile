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
                     def XML_FILE = "C:\\Users\\malkheliwy\\Desktop\\serverConf.xml"
                    
                    // Execute script using bat
                    bat '''
                    @echo off
                    setlocal
                    
                    set "XML_FILE=C:\\Users\\malkheliwy\\Desktop\\serverConf.xml"
                    
                    echo Reading current database configuration from "%XML_FILE%"
                    powershell -Command ^
                        "$xmlContent = Get-Content \"%XML_FILE%\" -Raw; " ^
                        "$xml = [xml]$xmlContent; " ^
                        "$db = $xml.serverConfig.Database | Where-Object { $_.databaseName -eq 'ABSHER2_DB' }; " ^
                        "if ($db) { " ^
                            "$env:OLD_DB_name = $db.databaseName; " ^
                            "$env:OLD_DB_ip = $db.databaseIP; " ^
                            "$env:OLD_DB_port = $db.databasePort; " ^
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
