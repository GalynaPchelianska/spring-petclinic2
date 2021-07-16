pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    } 
environment {
    ArtifactId = readMavenPom().getArtifactId()
    Version = readMavenPom().getVersion()
    Name = readMavenPom().getName()
    GroupId = readMavenPom().getGroupId()
    } 

   
    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }
    
        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }
   
       // Stage3 : Create artifact and Deploy the to Nexus
        stage ('Deplot to Nexus'){
            steps {
                script {

 def NexusRepo = Version.endsWith("SNAPSHOT") ? "Artifact_storage-Snaphot" : "Artifact_storage" 
            nexusArtifactUploader artifacts:
            [[artifactId: "${ArtifactId}",
            classifier: '',
            file: "target/${ArtifactId}-${Version}.${BUILD_NUMBER}.jar",
            type: 'jar']],
            credentialsId: '8e17e711-c343-4d0f-9e22-18ffa6e11102',
            groupId: "${GroupId}",
            nexusUrl: '10.0.0.4:8081',
            nexusVersion: 'nexus3',
            protocol: 'http',
            repository: 'Artifact_storage',
            version: "${Version}.${BUILD_NUMBER}"
                    }
            }
                } 
        
        // Stage4 : Deploing
        stage ('Deploy') {
            steps {
                echo "deploing ...."
                ansiblePlaybook (
                    inventory: '/opt/playbooks'
                    playbook: '/opt/playbook/deploy.yml'                
                    )
            }
        }


    }

}