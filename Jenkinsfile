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
                    def x = new getMyName()
                    echo x.MyName()
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
                    
                    def xmlContent = readFile(file: XML_FILE).trim()
                    def userChoice = input(
                        message: 'Name of desired database: ',
                        parameters: [
                            string(defaultValue: "", description: 'Name of desired database to change', name: 'choiceDB_name', trim: true)
                        ]
                    )
                    def choiceDB_name2 = "${userChoice.choiceDB_name}"
                    //values
                    def abs2StartIndex = xmlContent.indexOf("<databaseName>${choiceDB_name2}</databaseName>") + '<databaseName>'.length()
                    def abs2EndIndex = xmlContent.indexOf('</databaseName>', abs2StartIndex)
                    def ipStartIndex = xmlContent.indexOf('<databaseIP>', abs2StartIndex) + '<databaseIP>'.length()
                    def ipEndIndex = xmlContent.indexOf('</databaseIP>', ipStartIndex)
                    def portStartIndex = xmlContent.indexOf('<databasePort>', abs2StartIndex) + '<databasePort>'.length()
                    def portEndIndex = xmlContent.indexOf('</databasePort>', portStartIndex)
                    
                    //old values
                    def oldDB_name = xmlContent.substring(abs2StartIndex, abs2EndIndex).trim()
                    def oldDB_ip = xmlContent.substring(ipStartIndex, ipEndIndex).trim()
                    def oldDB_port = xmlContent.substring(portStartIndex, portEndIndex).trim()
                    
                    def userInput = input(
                        message: 'Specify New Database Configuration',
                        parameters: [
                            string(defaultValue: oldDB_name, description: 'New database name', name: 'newDB_name', trim: true),
                            string(defaultValue: oldDB_ip, description: 'New database IP', name: 'newDB_ip', trim: true),
                            string(defaultValue: oldDB_port, description: 'New database port', name: 'newDB_port', trim: true)
                        ]
                    )
                    
                    // Log old values
                    echo "Old Database Confg:"
                    echo "  Database Name: ${oldDB_name}"
                    echo "  Database IP: ${oldDB_ip}"
                    echo "  Database Port: ${oldDB_port}"
                    
                    // Replace old values with new values in the XML content
                    def updatedXmlContent = xmlContent.replaceFirst("<databaseName>${oldDB_name}</databaseName>", "<databaseName>${userInput.newDB_name}</databaseName>")
                                                      .replaceFirst("<databaseIP>${oldDB_ip}</databaseIP>", "<databaseIP>${userInput.newDB_ip}</databaseIP>")
                                                      .replaceFirst("<databasePort>${oldDB_port}</databasePort>", "<databasePort>${userInput.newDB_port}</databasePort>")
                    
                    // Write updated XML back to file
                    writeFile file: XML_FILE, text: updatedXmlContent
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
