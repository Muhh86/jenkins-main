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
                    
                    // Read XML content using readFile
                    def xmlContent = readFile(file: XML_FILE).trim()
                    
                    // Parse XML using XmlParser
                    def xml = new XmlParser().parseText(xmlContent)
                    
                    // Find and extract current database configuration
                    def currentDB = xml.serverConfig.Database.find { it.@databaseName == 'ABSHER2_DB' }
                    def oldDB_name = currentDB.databaseName.text()
                    def oldDB_ip = currentDB.databaseIP.text()
                    def oldDB_port = currentDB.databasePort.text()
                    
                    // Prompt user for input
                    def userInput = input(
                        message: 'Specify New Database Configuration',
                        parameters: [
                            string(defaultValue: oldDB_name, description: 'New database name', name: 'newDB_name', trim: true),
                            string(defaultValue: oldDB_ip, description: 'New database IP', name: 'newDB_ip', trim: true),
                            string(defaultValue: oldDB_port, description: 'New database port', name: 'newDB_port', trim: true)
                        ]
                    )
                    
                    // Log old values
                    echo "Old Database Configuration:"
                    echo "  Database Name: ${oldDB_name}"
                    echo "  Database IP: ${oldDB_ip}"
                    echo "  Database Port: ${oldDB_port}"
                    
                    // Update XML with new values
                    currentDB.databaseName.replaceBody(userInput.newDB_name)
                    currentDB.databaseIP.replaceBody(userInput.newDB_ip)
                    currentDB.databasePort.replaceBody(userInput.newDB_port)
                    
                    // Serialize XML to string
                    def updatedXmlContent = groovy.xml.XmlUtil.serialize(xml)
                    
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
