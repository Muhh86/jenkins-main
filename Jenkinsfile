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
        booleanParam(
            name: 'Snapshot', 
            defaultValue: true, 
            description: 'Uncheck if its a release'
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
                script{
                    if (!params.Snapshot){
                        dir('MavenJavaTest') {  // Change directory to where the Maven project is
                            withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                                bat """
                                    mvn deploy:deploy-file \
                                    -DgroupId=test.devops \
                                    -DartifactId=MavenJavaTest \
                                    -Dversion=1.0.${env.BUILD_NUMBER} \
                                    -Dpackaging=jar \
                                    -Dfile=target/MavenJavaTest-1.0.jar \
                                    -DrepositoryId=nexus-releases \
                                    -Durl=http://localhost:8081/repository/maven-releases/ \
                                    -s C:\\Users\\malkheliwy\\Desktop\\nexus-3.70.1-02\\system\\settings.xml
                                """
                                //you can change the major version number by modifing the numbers before the BUILD_NUMBER_BASE above


                            }
                        }
                    }
                    else{
                        dir('MavenJavaTest'){
                            withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                                bat """
                                    mvn deploy:deploy-file \
                                    -DgroupId=test.devops \
                                    -DartifactId=MavenJavaTest \
                                    -Dversion=1.0-SNAPSHOT \
                                    -Dpackaging=jar \
                                    -Dfile=target/MavenJavaTest-1.0.jar \
                                    -DrepositoryId=nexus-snapshots \
                                    -Durl=http://localhost:8081/repository/maven-snapshots/ \
                                    -s C:\\Users\\malkheliwy\\Desktop\\nexus-3.70.1-02\\system\\settings.xml
                                """
                            }
                        }
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
        stage('Convert to CSV') {
            steps {
                script {
                    bat 'del employees.csv'

                    //Read the input file and convert to CSV using PowerShell
                    bat '''
                        @echo off
                        setlocal enabledelayedexpansion
                        (for /f "delims=" %%a in (employees.txt) do (
                            set "line=%%a"
                            set "line=!line:|=,!"
                            echo !line!
                        )) > employees.csv
                    '''
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
        // stage('Trigger Another Pipeline') {
        //     steps {
        //         build job: 'testPipeline'
        //     }
        // }
        
        // Add more stages as needed
    }
    
    post {
        success {
            echo "Build and deployment of version 1.0.${env.BUILD_NUMBER} successful!"      
            emailext (
                subject: "Employee CSV File",
                body: "Please find attached the employee CSV file.",
                to: "m.alkheliwy@gmail.com",
                attachmentsPattern: "employees.csv"
                debugMode: true
            )  
        }
        failure {
            echo "Build or deployment failed!"
        }
        
    }
}
