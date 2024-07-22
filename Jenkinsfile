@Library ('jenkins-shared-lib')_
import getMyName.getMyName


def getDatabaseNames(xmlContent) {
    def databaseNames = []
    def matcher = (xmlContent =~ /<databaseName>(.*?)<\/databaseName>/)
    matcher.each { match ->
        databaseNames << match[1]
    }
    return databaseNames
}

pipeline {
    agent any
    
    environment {
        // Increment this manually for each release
        PROJECT_VERSION = '1.0'
    }

    tools {
        maven 'Maven 3.9.8'
    }

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

        

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }


        stage('Build') {
            steps {
                dir('MavenJavaTest') {
                    bat 'mvn clean package'
                }
            }
        }
        stage('Release') {
            steps {
                dir('MavenJavaTest') {  // Change directory to where the Maven project is
                    withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                        bat """
                            mvn release:prepare release:perform -B \
                            -DpreparationGoals="clean verify" \
                            -DdevelopmentVersion=1.1-SNAPSHOT \
                            -DreleaseVersion=${env.BUILD_NUMBER}.0 \
                            -DscmCommentPrefix="[JENKINS] " \
                            -DskipTests -DskipITs \
                            -Darguments="-DskipTests -DskipITs" \
                            -s C:\\Users\\malkheliwy\\Desktop\\nexus-3.70.1-02\\system\\settings.xml
                        """
                    }
                }
            }
        }
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
        // stage('DB & User Input'){
        //     steps {
        //         script {
        //             def XML_FILE = "C:\\Users\\malkheliwy\\Desktop\\serverConf.xml"
                    
        //             def xmlContent = readFile(file: XML_FILE).trim()


        //             // i have no idea what this code and it's method is but if it works i won't ask
        //             def databaseNames = getDatabaseNames(xmlContent)
        //             def choicesString = databaseNames.join('\n')
        //             // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


        //             def userChoice = input(
        //                 message: 'Name of desired database: ',
        //                 parameters: [
        //                     string(name: 'choiceDB_name', defaultValue: '', description: "Name of desired database to change.\nAvailable options:\n${choicesString}")
        //                 ]
        //             )
        //             def choiceDB_name2 = "${userChoice}"
        //             //values
        //             def abs2StartIndex = xmlContent.indexOf("<databaseName>${choiceDB_name2}</databaseName>") + '<databaseName>'.length()
        //             def abs2EndIndex = xmlContent.indexOf('</databaseName>', abs2StartIndex)
        //             def ipStartIndex = xmlContent.indexOf('<databaseIP>', abs2StartIndex) + '<databaseIP>'.length()
        //             def ipEndIndex = xmlContent.indexOf('</databaseIP>', ipStartIndex)
        //             def portStartIndex = xmlContent.indexOf('<databasePort>', abs2StartIndex) + '<databasePort>'.length()
        //             def portEndIndex = xmlContent.indexOf('</databasePort>', portStartIndex)
                    
        //             //old values
        //             def oldDB_name = xmlContent.substring(abs2StartIndex, abs2EndIndex).trim()
        //             def oldDB_ip = xmlContent.substring(ipStartIndex, ipEndIndex).trim()
        //             def oldDB_port = xmlContent.substring(portStartIndex, portEndIndex).trim()
                    
        //             def userInput = input(
        //                 message: 'Specify New Database Configuration',
        //                 parameters: [
        //                     string(defaultValue: oldDB_name, description: 'New database name', name: 'newDB_name', trim: true),
        //                     string(defaultValue: oldDB_ip, description: 'New database IP', name: 'newDB_ip', trim: true),
        //                     string(defaultValue: oldDB_port, description: 'New database port', name: 'newDB_port', trim: true)
        //                 ]
        //             )
                    
        //             // Log old values
        //             echo "Old Database Confg: "
        //             echo "  Database Name: ${oldDB_name}"
        //             echo "  Database IP: ${oldDB_ip}"
        //             echo "  Database Port: ${oldDB_port}"
                    
        //             // Replace old values with new values in the XML content
        //             def updatedXmlContent = xmlContent.replaceFirst("<databaseName>${oldDB_name}</databaseName>", "<databaseName>${userInput.newDB_name}</databaseName>")
        //                                               .replaceFirst("<databaseIP>${oldDB_ip}</databaseIP>", "<databaseIP>${userInput.newDB_ip}</databaseIP>")
        //                                               .replaceFirst("<databasePort>${oldDB_port}</databasePort>", "<databasePort>${userInput.newDB_port}</databasePort>")
                    
        //             // Write updated XML back to file
        //             writeFile file: XML_FILE, text: updatedXmlContent
        //             echo "New Database Confg: "
        //             echo " Database Name: ${userInput.newDB_name}"
        //             echo " Database IP: ${userInput.newDB_ip}"
        //             echo " Database Port: ${userInput.newDB_port}"
        //         }
        //     }
        // }
        // stage("Database Migration") {
        //     steps {
        //         script {
        //             def yamlFilePath = 'C:\\Users\\malkheliwy\\Desktop\\BirthCertificateService\\conf\\depCfg.yml'
                    
        //             // Read the YAML file
        //             def yamlContent = readYaml file: yamlFilePath
        //             def dbscript = yamlContent.dbscripts[0]
                    
        //             if (dbscript) {
        //                 echo "${dbscript}"
        //                 def sourcePath = "C:/Users/malkheliwy/Desktop/BirthCertificateService${dbscript.source}"
        //                 echo "Constructed source path: ${sourcePath}" 

        //                 if (sourcePath) {
        //                     echo "Directory exists"
        //                     def filesOutput = bat(script: "dir /b \"${sourcePath}\"", returnStdout: true).trim()
        //                     def files = filesOutput.split('\n').collect { it.trim() }
                            
        //                     def stages = [:]
        //                     files.each { file ->
        //                         if (file.endsWith('.txt')) {
        //                             stages["Process ${file}"] = {
        //                                 stage("Processing ${file}") {
        //                                     echo "Processing file: ${file}"
                                            
        //                                     // read file content
        //                                     def fileContent = bat(script: "type \"${sourcePath}\\${file}\"", returnStdout: true).trim()
                                            
        //                                     // validate DROP and DELETE
        //                                     if (fileContent.toUpperCase().contains("DELETE") || fileContent.toUpperCase().contains("DROP")) {
        //                                         //error should be here insted of echo
        //                                         echo "WARNING: File ${file} contains DELETE or DROP statements. Skipping execution."
        //                                     } else {
        //                                         echo "File content:"
        //                                         echo fileContent
        //                                         echo "Processing of ${file} successful"
        //                                     }
        //                                 }
        //                             }
        //                         }
        //                     }
                            
        //                     parallel stages
                            
        //                 } else {
        //                     echo "Directory does not exist : ${sourcePath}"
        //                 }
                        
        //             } else {
        //                 echo "No valid dbscript entry found in YAML."
        //             }
        //         }
        //     }
        // }
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
