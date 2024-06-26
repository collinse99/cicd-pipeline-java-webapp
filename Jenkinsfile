pipeline {
    agent any
    tools {
        maven 'maven'
    }
     environment {
         ArtifactId = "Erics Artifiact"
         Version = "1.1"
         GroupId = "ID_12"
         Name = "Build_Eric"
     }
    stages {
stage('Build') {
            steps {
                script {
                    // Use a script block to execute Groovy code
                    //def pom = readMavenPom file: 'pom.xml'
                    sh 'mvn clean install'
                    // Now you can use the 'pom' object for further processing
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Publish to Nexus') {
            steps { 
                script {
                    def NexusRepo = Version.endsWith("SNAPSHOT") ? "MyLab-SNAPSHOT" : "MyLab-RELEASE"
                    
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: "${ArtifactId}", 
                            classifier: '', 
                            file: "target/${ArtifactId}-${Version}.war", 
                            type: 'war'
                        ]
                    ], 
                    credentialsId: 'nexus', 
                    groupId: "${GroupId}", 
                    nexusUrl: '10.0.0.167:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: "${NexusRepo}", 
                    version: "${Version}"
                }
            }
        }
        stage('Print Environment variables') {
            steps {
                echo "Artifact ID is '${ArtifactId}'"
                echo "Group ID is '${GroupId}'"
                echo "Version is '${Version}'"
                echo "Name is '${Name}'"
            }
        }
        stage('Deploy to Docker') {
            steps {
                echo 'Deploying...'
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'ansible-controller', 
                    transfers: [
                        sshTransfer(
                            sourceFiles: 'download-deploy.yaml, hosts',
                            remoteDirectory: '/playbooks',
                            cleanRemote: false,
                            execCommand: 'cd playbooks/ && ansible-playbook download-deploy.yaml -i hosts', 
                            execTimeout: 120000, 
                        )
                    ], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)
                ])
            }
        }
    }
}
