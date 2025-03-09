@Library('tase-shared-groovy-lib@main') _

pipeline {
    agent any
    
    triggers {
        // Poll SCM every 5 minutes
        pollSCM('H/5 * * * *')
    }
    
    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Call our shared library function
                    buildInfo = prepareEnv()
                    
                    // Set version for artifact
                    env.ARTIFACT_VERSION = "${buildInfo.shortCommit}-${env.BUILD_NUMBER}"
                    echo "Building version: ${env.ARTIFACT_VERSION}"
                }
            }
        }
        
        stage('Build Artifact') {
            steps {
                script {
                    // Create a simple text file as our "artifact"
                    def content = """
                    ====================================
                    Build Artifact v${env.ARTIFACT_VERSION}
                    ====================================
                    
                    Commited by: ${buildInfo.committer}
                    Commit: ${buildInfo.commitId}
                    Branch: ${buildInfo.branch}
                    
                    ✨ Hello Hiring Team! ✨
                    
                    I'm Steve, and I'd love to join your team!
                    My DevOps skills include Jenkins, CI/CD pipelines,
                    Docker, and more. This artifact demonstrates
                    my ability to create automated build processes.
                    
                    Let's build great things together! 😊
                    
                    ====================================
                    """
                    
                    // Write content to file
                    writeFile file: "artifact-${env.ARTIFACT_VERSION}.txt", text: content
                    echo "Artifact created successfully"
                }
            }
        }
        
        stage('Package Artifact') {
            steps {
                script {
                    // Create a zip file
                    sh "zip artifact-${env.ARTIFACT_VERSION}.zip artifact-${env.ARTIFACT_VERSION}.txt"
                    echo "Artifact packaged as ZIP"
                }
            }
        }
        
        stage('Upload to Repo') {
            steps {
                script {
                    echo "Artifact ready for upload: artifact-${env.ARTIFACT_VERSION}.zip"
                    
                    // Add GitHub release upload with credentials
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                        // Create GitHub release and upload artifact
                        sh """
                            curl -X POST \
                            -H "Authorization: token ${GITHUB_TOKEN}" \
                            -H "Accept: application/vnd.github.v3+json" \
                            -H "Content-Type: application/json" \
                            https://api.github.com/repos/CharmingSteve/tase-groovy/releases \
                            -d '{
                                "tag_name": "v${env.ARTIFACT_VERSION}",
                                "name": "Release ${env.ARTIFACT_VERSION}",
                                "body": "Automated release from Jenkins",
                                "draft": false,
                                "prerelease": false
                            }'
                        """
                        
                        // Get release ID and upload asset
                        def releaseId = sh(script: """
                            curl -s \
                            -H "Authorization: token ${GITHUB_TOKEN}" \
                            -H "Accept: application/vnd.github.v3+json" \
                            https://api.github.com/repos/CharmingSteve/tase-groovy/releases/tags/v${env.ARTIFACT_VERSION} | grep -o '"id": [0-9]*' | head -1 | cut -d' ' -f2
                        """, returnStdout: true).trim()
                        
                        sh """
                            curl -X POST \
                            -H "Authorization: token ${GITHUB_TOKEN}" \
                            -H "Content-Type: application/zip" \
                            -H "Accept: application/vnd.github.v3+json" \
                            --data-binary @artifact-${env.ARTIFACT_VERSION}.zip \
                            "https://uploads.github.com/repos/CharmingSteve/tase-groovy/releases/${releaseId}/assets?name=artifact-${env.ARTIFACT_VERSION}.zip"
                        """
                    }
                    
                    // For demo purposes, we'll archive it in Jenkins
                    archiveArtifacts artifacts: "artifact-${env.ARTIFACT_VERSION}.zip", fingerprint: true
                    
                    echo "✅ Pipeline completed successfully!"
                }
            }
        }
    }
    
    post {
        success {
            echo "Build succeeded! Artifact version: ${env.ARTIFACT_VERSION}"
        }
        failure {
            echo "Build failed. Please check the logs for details."
        }
    }
}
