pipeline {
    agent any
    tools {
        maven 'mvn'
    }

    stages {
        stage('Clone the repo from github') {
            steps {
                git 'https://github.com/eadalakrishna/JavaWebCalculator.git'
            }
        }
        
        stage('Build using maven'){
            steps {
    
                    sh 'mvn clean install package'
 
            }
        }
        stage('Sonar analysis') {
            steps {
                withSonarQubeEnv('sonar') {

                        sh 'mvn sonar:sonar'

                }
            }
        }

        stage("Publish to Nexus Repository Manager") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'webapp', classifier: '', file: '/var/lib/jenkins/workspace/integratepipeline/target/webapp-0.1.war', type: 'war']], 
                credentialsId: 'nex', 
                groupId: 'com.web.cal<', 
                nexusUrl: '54.146.231.132:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'kittu-snap', 
                version: '1.0-SNAPSHOT'
                
        }    
    }
    
        stage('Artifactory config') {
            steps {
                rtUpload(
                serverId: 'jfrog',
                    spec: '''{
                        "files": [
                            {
                                  "pattern": "/var/lib/jenkins/workspace/integratepipeline/target/*.war",
                                  "target": "example-repo-local"
                            }
                            ]
                            }''',
        buildName: 'holyFrog',
        buildNumber: '42',
        project: 'my-project-key'
)
            }
        }
    
        stage('Deploy to Tomcat') {
            steps {
                sshagent(['tomcat-ssh']) {
                    sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/integratepipeline/target/*.war ec2-user@18.212.206.131:/home/ec2-user/apache-tomcat-9.0.84/webapps'
                }
            }
        }
    }
}
