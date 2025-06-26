# Jenkins Credentials Management

Credentials are a critical component of Jenkins security, allowing secure storage and usage of sensitive information like passwords, API tokens, SSH keys, and certificates.

## Why Use Jenkins Credentials?

### Security Benefits
- **Centralized Management**: Store all credentials in one secure location
- **Encryption**: Credentials are encrypted at rest in Jenkins
- **Access Control**: Limit who can view and use credentials
- **Audit Trail**: Track credential usage across jobs

### Common Use Cases
- Git repository authentication
- SSH connections to servers
- API tokens for cloud services
- Database passwords
- Docker registry credentials
- Certificate authentication

## Types of Credentials

<div align="center">
  <img src="../images/credential.png" alt="Jenkins Credentials Types" width="800" />
</div>

### 1. Username with Password
**Use for:**
- Basic authentication
- Git HTTPS access
- Database connections

**Example:**
```groovy
withCredentials([usernamePassword(
    credentialsId: 'github-creds',
    usernameVariable: 'GIT_USERNAME',
    passwordVariable: 'GIT_PASSWORD'
)]) {
    sh 'git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/org/repo.git'
}
```

### 2. SSH Username with Private Key
**Use for:**
- SSH connections to servers
- Git SSH authentication
- SCP/SFTP operations

**Components:**
- Username
- Private key (directly entered or from file)
- Optional passphrase

**Example:**
```groovy
sshagent(['ssh-key-credential']) {
    sh 'ssh user@server "deploy script"'
    sh 'git clone git@github.com:org/repo.git'
}
```

### 3. Secret Text
**Use for:**
- API tokens
- Single-value secrets
- Webhook secrets

**Example:**
```groovy
withCredentials([string(credentialsId: 'api-token', variable: 'API_TOKEN')]) {
    sh 'curl -H "Authorization: Bearer ${API_TOKEN}" https://api.example.com/deploy'
}
```

### 4. Secret File
**Use for:**
- Certificates
- Configuration files
- Kubeconfig files
- Key files

**Example:**
```groovy
withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
    sh 'kubectl apply -f deployment.yaml'
}
```

### 5. Certificate
**Use for:**
- Client certificates
- SSL/TLS authentication
- Mutual TLS

**Components:**
- Certificate file
- Private key
- Optional password

## Adding Credentials

### Step 1: Navigate to Credentials
1. Go to **Manage Jenkins** → **Manage Credentials**
2. Select the appropriate domain (usually "Global")
3. Click **Add Credentials**

### Step 2: Configure Credential
1. **Kind**: Select credential type
2. **Scope**: Choose visibility level
   - **Global**: Available to all jobs
   - **System**: Only for Jenkins system use
3. **ID**: Unique identifier (use descriptive names like `github-token`)
4. **Description**: Human-readable description

### Step 3: Enter Credential Data
Based on type, provide:
- Username/password
- SSH key content
- Secret text
- Upload files
- Certificate details

## Credential Scopes

### Global Domain
- Default domain for most credentials
- Available to all jobs and pipelines
- Most commonly used

### System Domain
- Reserved for Jenkins internal use
- Agent connections
- Plugin configurations
- Not available to regular jobs

### Folder-level Credentials
- Scoped to specific folders
- Inherit down the folder hierarchy
- Better access control

## Using Credentials in Jobs

### Freestyle Jobs
1. In job configuration, look for credential dropdowns
2. Build Environment → "Use secret text(s) or file(s)"
3. Source Code Management → Credentials dropdown

### Pipeline Jobs

#### Declarative Pipeline
```groovy
pipeline {
    agent any
    environment {
        GITHUB_TOKEN = credentials('github-token')
    }
    stages {
        stage('Build') {
            steps {
                sh 'echo $GITHUB_TOKEN | base64'
            }
        }
    }
}
```

#### Scripted Pipeline
```groovy
node {
    withCredentials([
        string(credentialsId: 'api-key', variable: 'API_KEY'),
        usernamePassword(
            credentialsId: 'db-creds',
            usernameVariable: 'DB_USER',
            passwordVariable: 'DB_PASS'
        )
    ]) {
        sh """
            export API_KEY=${API_KEY}
            export DB_USER=${DB_USER}
            export DB_PASSWORD=${DB_PASS}
            ./deploy.sh
        """
    }
}
```

## Credential Providers

### Jenkins Credential Store (Default)
- Built-in credential storage
- Encrypted with Jenkins master key
- Stored in `$JENKINS_HOME/credentials.xml`

### External Credential Stores
- **HashiCorp Vault**: Dynamic secrets management
- **AWS Secrets Manager**: AWS-native solution
- **Azure Key Vault**: Azure integration
- **CyberArk**: Enterprise password management

### Plugin Examples

#### Vault Plugin
```groovy
withVault([
    vaultSecrets: [
        [path: 'secret/jenkins/github', 
         secretValues: [
            [envVar: 'GITHUB_TOKEN', vaultKey: 'token']
         ]]
    ]
]) {
    sh 'git clone https://oauth2:${GITHUB_TOKEN}@github.com/org/repo.git'
}
```

## Summary

Jenkins Credentials provide:
- **Secure storage** for sensitive information
- **Centralized management** of authentication
- **Easy integration** with jobs and pipelines
- **Audit capabilities** for compliance

Remember:
- Always use credentials instead of hardcoding secrets
- Implement proper access controls
- Regularly rotate and audit credentials
- Consider external credential stores for enterprise use

Proper credential management is essential for maintaining security while enabling automation in your CI/CD pipelines. 