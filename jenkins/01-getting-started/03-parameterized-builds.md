# Jenkins Parameterized Builds

Parameterized builds allow you to pass dynamic values to your Jenkins jobs at runtime, making them flexible and reusable across different environments and scenarios.

## What are Parameterized Builds?

Parameterized builds enable you to:
- Accept user input when triggering builds
- Reuse the same job for different environments (dev, staging, prod)
- Pass dynamic values like version numbers, branch names, or deployment targets
- Create more flexible and maintainable CI/CD pipelines

<div align="center">
  <img src="../images/parameter.png" alt="Jenkins Parameters Configuration" width="800" />
</div>

## Types of Parameters

### 1. String Parameter
**Use for:** Text input like version numbers, URLs, or names
```groovy
parameters {
    string(name: 'VERSION', defaultValue: '1.0.0', description: 'Build version')
    string(name: 'DEPLOY_URL', defaultValue: 'https://staging.example.com', description: 'Deployment URL')
}
```

### 2. Choice Parameter
**Use for:** Dropdown selections with predefined options
```groovy
parameters {
    choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'production'], description: 'Target environment')
    choice(name: 'REGION', choices: ['us-east-1', 'us-west-2', 'eu-west-1'], description: 'AWS Region')
}
```

### 3. Boolean Parameter
**Use for:** Toggle features or conditional execution
```groovy
parameters {
    booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run test suite')
    booleanParam(name: 'DEPLOY', defaultValue: false, description: 'Deploy after build')
}
```

### 4. Text Parameter
**Use for:** Multi-line text input
```groovy
parameters {
    text(name: 'RELEASE_NOTES', defaultValue: '', description: 'Release notes for this build')
}
```

### 5. Password Parameter
**Use for:** Sensitive data that should be masked
```groovy
parameters {
    password(name: 'API_KEY', defaultValue: '', description: 'API key for deployment')
}
```

### 6. File Parameter
**Use for:** Uploading files to the build
```groovy
parameters {
    file(name: 'CONFIG_FILE', description: 'Configuration file to use')
}
```

## Configuring Parameters in Freestyle Jobs

### Step 1: Enable Parameters
1. Go to job configuration
2. Check **"This project is parameterized"**
3. Click **"Add Parameter"** and select parameter type

### Step 2: Configure Each Parameter
For each parameter, specify:
- **Name**: Variable name (no spaces, use UPPER_CASE)
- **Default Value**: Pre-filled value
- **Description**: Help text for users

### Step 3: Use Parameters in Build Steps
Access parameters as environment variables:
```bash
echo "Building version: $VERSION"
echo "Deploying to: $ENVIRONMENT"

if [ "$RUN_TESTS" = "true" ]; then
    echo "Running tests..."
    npm test
fi
```

## Using Parameters in Pipeline Jobs

### Declarative Pipeline
```groovy
pipeline {
    agent any
    
    parameters {
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'Version to build')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'prod'], description: 'Environment')
        booleanParam(name: 'DEPLOY', defaultValue: false, description: 'Deploy after build?')
    }
    
    stages {
        stage('Build') {
            steps {
                echo "Building version ${params.VERSION}"
                sh "docker build -t myapp:${params.VERSION} ."
            }
        }
        
        stage('Test') {
            steps {
                echo "Running tests for ${params.ENVIRONMENT}"
                sh "npm test"
            }
        }
        
        stage('Deploy') {
            when {
                expression { params.DEPLOY == true }
            }
            steps {
                echo "Deploying to ${params.ENVIRONMENT}"
                sh "./deploy.sh ${params.ENVIRONMENT} ${params.VERSION}"
            }
        }
    }
}
```

### Scripted Pipeline
```groovy
properties([
    parameters([
        string(name: 'BRANCH', defaultValue: 'main', description: 'Git branch'),
        choice(name: 'BUILD_TYPE', choices: ['Debug', 'Release'], description: 'Build configuration'),
        booleanParam(name: 'CLEAN_WORKSPACE', defaultValue: false, description: 'Clean before build')
    ])
])

node {
    if (params.CLEAN_WORKSPACE) {
        deleteDir()
    }
    
    stage('Checkout') {
        git branch: params.BRANCH, url: 'https://github.com/example/repo.git'
    }
    
    stage('Build') {
        sh "make ${params.BUILD_TYPE.toLowerCase()}"
    }
}
```

## Advanced Parameter Features

### Dynamic Parameter Values
Use Active Choices plugin for dynamic parameters:
```groovy
properties([
    parameters([
        [$class: 'ChoiceParameter', 
         name: 'ENVIRONMENT',
         choiceType: 'PT_SINGLE_SELECT',
         script: [
             $class: 'GroovyScript',
             script: [
                 script: '''
                     if (BRANCH == 'main') {
                         return ['prod', 'staging']
                     } else {
                         return ['dev', 'test']
                     }
                 '''
             ]
         ]]
    ])
])
```

### Validating Parameters
```groovy
stage('Validate Parameters') {
    steps {
        script {
            if (!params.VERSION.matches('\\d+\\.\\d+\\.\\d+')) {
                error("Invalid version format. Use semantic versioning (e.g., 1.2.3)")
            }
            
            if (params.ENVIRONMENT == 'prod' && params.BRANCH != 'main') {
                error("Production deployments must use main branch")
            }
        }
    }
}
```

### Parameter Inheritance
Pass parameters to downstream jobs:
```groovy
stage('Trigger Downstream') {
    steps {
        build job: 'downstream-job', 
              parameters: [
                  string(name: 'VERSION', value: params.VERSION),
                  string(name: 'ENVIRONMENT', value: params.ENVIRONMENT),
                  booleanParam(name: 'FROM_UPSTREAM', value: true)
              ]
    }
}
```

## Best Practices

### 1. Naming Conventions
- Use UPPER_CASE for parameter names
- Be descriptive: `DEPLOY_ENVIRONMENT` not just `ENV`
- Avoid spaces and special characters

### 2. Default Values
- Always provide sensible defaults
- Use safe defaults (e.g., `DEPLOY=false`)
- Consider the most common use case

### 3. Descriptions
- Write clear, helpful descriptions
- Include format examples
- Specify valid values or ranges

### 4. Validation
- Validate parameter values early
- Fail fast with clear error messages
- Check dependencies between parameters

### 5. Security
- Use password parameters for sensitive data
- Don't echo sensitive parameters
- Consider using Jenkins credentials instead

## Common Use Cases

### Multi-Environment Deployment
```groovy
parameters {
    choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'staging', 'prod'])
    string(name: 'VERSION', defaultValue: 'latest')
    booleanParam(name: 'SMOKE_TEST', defaultValue: true)
}

stage('Deploy') {
    steps {
        script {
            def config = readYaml file: "config/${params.ENVIRONMENT}.yaml"
            sh "kubectl apply -f k8s/${params.ENVIRONMENT}/"
            sh "kubectl set image deployment/app app=myapp:${params.VERSION}"
        }
    }
}
```

### Feature Toggle Builds
```groovy
parameters {
    booleanParam(name: 'ENABLE_FEATURE_A', defaultValue: false)
    booleanParam(name: 'ENABLE_FEATURE_B', defaultValue: true)
    booleanParam(name: 'RUN_INTEGRATION_TESTS', defaultValue: false)
}

stage('Build') {
    steps {
        sh """
            ./build.sh \
                --feature-a=${params.ENABLE_FEATURE_A} \
                --feature-b=${params.ENABLE_FEATURE_B}
        """
    }
}
```

## Triggering Parameterized Builds

### Via UI
1. Click **"Build with Parameters"**
2. Fill in parameter values
3. Click **"Build"**

## Summary

Parameterized builds make Jenkins jobs:
- **Flexible**: One job serves multiple purposes
- **Reusable**: Same logic for different environments
- **Interactive**: Users can customize builds
- **Maintainable**: Fewer jobs to manage

Start with simple string and choice parameters, then explore advanced features as your needs grow. Remember to validate inputs and provide clear documentation for your parameters. 