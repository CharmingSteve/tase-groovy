## TASE DevOps Pipeline Assignment

This repository demonstrates a complete CI/CD pipeline for the Tel Aviv Stock Exchange DevOps assignment, showcasing automated build, packaging, and release processes.

### Assignment Requirements

Create a Jenkins pipeline that:
1. Triggers automatically from SCM changes
2. Builds an artifact
3. Packages the artifact
4. Uploads the artifact to a repository
5. Uses Groovy Shared Libraries with a `prepareEnv` function to extract SCM information

### Implementation

#### Pipeline Overview

The pipeline is defined in the `Jenkinsfile` and performs the following steps:

1. **Trigger**: Polls the SCM repository every 5 minutes for changes
2. **Prepare Environment**: Uses the shared library function `prepareEnv()` to extract SCM information
3. **Build Artifact**: Creates a text file with build information and a personal message
4. **Package Artifact**: Compresses the text file into a ZIP archive
5. **Upload to Repository**: Creates a GitHub release and uploads the artifact

#### Shared Library

The pipeline uses a shared library from [tase-shared-groovy-lib](https://github.com/CharmingSteve/tase-shared-groovy-lib) which provides the `prepareEnv()` function to extract commit information.

#### Versioning

Artifacts are versioned using a combination of:
- Short commit hash (first 7 characters)
- Build number

Example: `artifact-f7d8b8a-4.zip`

#### GitHub Releases

The pipeline automatically creates GitHub releases for each build:
- Tag name: `v{commit-hash}-{build-number}` (e.g., `vf7d8b8a-4`)
- Release name: `Release {commit-hash}-{build-number}` (e.g., `Release f7d8b8a-4`)
- Attached artifact: ZIP file containing the build artifact

### Artifact Contents

The artifact contains a personalized message to the hiring team, along with build information:

```
====================================
Build Artifact v{version}
====================================

Committed by: {committer}
Commit: {commit-id}
Branch: {branch}

✨ Hello Hiring Team! ✨

I'm Yishayahu, and I'd love to join your team!
My DevOps skills include Jenkins, CI/CD pipelines,
Docker, and more. This artifact demonstrates
my ability to create automated build processes.

Let's build great things together! 😊

====================================
```

### Setup Instructions

1. Install Jenkins (via Docker or directly)
2. Configure the shared library in Jenkins:
   - Go to Manage Jenkins → System → Global Pipeline Libraries
   - Add library with name `tase-shared-groovy-lib` pointing to the shared library repository
3. Create a Jenkins pipeline job:
   - Configure it to use SCM with this repository URL
   - Set the script path to `Jenkinsfile`
4. Add GitHub credentials in Jenkins:
   - Create a credential of type "Secret text" with ID `github-token`
   - Use a GitHub personal access token with `repo` permissions

### Security Considerations

In a production environment, additional security measures would be implemented:
- Private repositories with strict access controls
- Webhook security with proper authentication
- Artifact signing to verify integrity
- Secure credential management

