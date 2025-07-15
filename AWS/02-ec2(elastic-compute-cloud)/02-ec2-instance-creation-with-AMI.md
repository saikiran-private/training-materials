# EC2 Instance Creation with Custom AMI

This guide covers creating custom Amazon Machine Images (AMI) and using them to launch new EC2 instances with pre-configured software and settings.

## Overview

A custom AMI allows you to create a template of your configured EC2 instance, including the operating system, applications, configurations, and data. This enables rapid deployment of identical instances and ensures consistency across your infrastructure.

## What is an AMI?

An Amazon Machine Image (AMI) provides the information required to launch an instance:
- Operating system and software packages
- Application server and applications
- Configuration files and settings
- Data and file systems
- Launch permissions and storage settings
- Architecture (32-bit or 64-bit)

## Custom AMI Creation and Usage Flow

```mermaid
flowchart TD
    A[Start: AWS Console Login] -----> B[Navigate to EC2 Dashboard]
    B -----> C[Launch Base Instance]
    C -----> D[Choose Base AMI]
    D -----> E[Configure Instance]
    E -----> F[Connect to Instance]
    F -----> G[Install Software & Configure]
    G -----> H[Stop Instance]
    H -----> I[Create AMI from Instance]
    I -----> J[Launch New Instance from Custom AMI]
    J -----> K[Select Custom AMI]
    K -----> L[Configure New Instance]
    L -----> M[Launch Instance]
    M -----> N[Instance Running with Pre-configured Setup]
    
    D@{ img: "https://raw.githubusercontent.com/artisantek/training-materials/aws/AWS/02-ec2(elastic-compute-cloud)/images/ami-selection.png", h: 430, w: 1270, pos: "t"}
    E@{ img: "https://raw.githubusercontent.com/artisantek/training-materials/aws/AWS/02-ec2(elastic-compute-cloud)/images/instance-type.png", h: 446, w: 1544, pos: "t"}
    H@{ img: "https://raw.githubusercontent.com/artisantek/training-materials/aws/AWS/02-ec2(elastic-compute-cloud)/images/stop-instance.png", h: 400, w: 1200, pos: "t"}
    I@{ img: "https://raw.githubusercontent.com/artisantek/training-materials/aws/AWS/02-ec2(elastic-compute-cloud)/images/create-ami.png", h: 600, w: 1400, pos: "t"}
    K@{ img: "https://raw.githubusercontent.com/artisantek/training-materials/aws/AWS/02-ec2(elastic-compute-cloud)/images/custom-ami-selection.png", h: 500, w: 1300, pos: "t"}
    N@{ img: "https://raw.githubusercontent.com/artisantek/training-materials/aws/AWS/02-ec2(elastic-compute-cloud)/images/running-instance.png", h: 602, w: 2748, pos: "t"}
    
    style A fill:#e1f5fe,font-size:30px
    style D fill:#c8e6c9,font-size:30px
    style E fill:#c8e6c9,font-size:30px
    style G fill:#fff3e0,font-size:30px
    style H fill:#fff3e0,font-size:30px
    style I fill:#fff3e0,font-size:30px
    style K fill:#c8e6c9,font-size:30px
    style L fill:#c8e6c9,font-size:30px
    style N fill:#c8e6c9,font-size:30px
    style B font-size:30px
    style C font-size:30px
    style F font-size:30px
    style J font-size:30px
    style M font-size:30px
    
    %% Arrow styling - thick arrows
    linkStyle default stroke-width:10px
    linkStyle 0 stroke-width:10px
    linkStyle 1 stroke-width:10px
    linkStyle 2 stroke-width:10px
    linkStyle 3 stroke-width:10px
    linkStyle 4 stroke-width:10px
    linkStyle 5 stroke-width:10px
    linkStyle 6 stroke-width:10px
    linkStyle 7 stroke-width:10px
    linkStyle 8 stroke-width:10px
    linkStyle 9 stroke-width:10px
    linkStyle 10 stroke-width:10px
    linkStyle 11 stroke-width:10px
    linkStyle 12 stroke-width:10px
```

## Process Flow

### Phase 1: Creating Custom AMI
1. **AWS Console Login** - Access your AWS account
2. **Navigate to EC2** - Go to EC2 Dashboard
3. **Launch Base Instance** - Create instance for customization
4. **Choose Base AMI** - Select base operating system 🔑 ✅
5. **Configure Instance** - Set up instance type and settings 🔑 ✅
6. **Connect to Instance** - SSH into the instance
7. **Install & Configure** - Set up software and configurations 🔑 ✅
8. **Stop Instance** - Prepare for AMI creation 🔑 ✅
9. **Create AMI** - Generate custom image 🔑 ✅

### Phase 2: Using Custom AMI
10. **Launch from Custom AMI** - Start new instance creation
11. **Select Custom AMI** - Choose your created AMI 🔑 ✅
12. **Configure New Instance** - Set instance details 🔑 ✅
13. **Launch Instance** - Deploy the new instance
14. **Verify Setup** - Confirm pre-configured software is working 🔑 ✅

## Common Custom AMI Use Cases

### 1. Simple Web Server AMI

**Basic Apache Setup:**
```bash
#!/bin/bash
# Update system packages
yum update -y

# Install Apache web server
yum install -y httpd

# Start Apache and enable on boot
systemctl start httpd
systemctl enable httpd

# Create a simple welcome page
echo "<h1>Welcome to My Custom Web Server!</h1>" > /var/www/html/index.html
echo "<p>This server was created from a custom AMI.</p>" >> /var/www/html/index.html
```

### 2. Development Tools AMI

**Basic Development Setup:**
```bash
#!/bin/bash
# Update system
yum update -y

# Install basic development tools
yum install -y git
yum install -y nodejs npm
yum install -y python3 python3-pip

# Install a simple text editor
yum install -y nano vim

# Create a welcome message
echo "Development environment ready!" > /home/ec2-user/README.txt
echo "Git, Node.js, and Python3 are installed." >> /home/ec2-user/README.txt
```


### 3. Docker Host AMI

**Simple Docker Installation:**
```bash
#!/bin/bash
# Update system
yum update -y

# Install Docker
yum install -y docker

# Start Docker service
systemctl start docker
systemctl enable docker

# Add ec2-user to docker group
usermod -a -G docker ec2-user

# Verify installation
echo "Docker installed successfully" > /home/ec2-user/docker-status.txt
```

## AMI Best Practices

### 1. **Preparation Before Creating AMI**
- Remove sensitive data and credentials
- Clear logs and temporary files
- Update system packages
- Remove SSH keys and user-specific data
- Clear command history

### 2. **AMI Security Considerations**
```bash
# Clean up script before AMI creation
#!/bin/bash
# Remove SSH host keys (will be regenerated)
rm -f /etc/ssh/ssh_host_*

# Clear bash history
history -c
rm -f ~/.bash_history

# Remove temporary files
rm -rf /tmp/*
rm -rf /var/tmp/*

# Clear log files
find /var/log -type f -exec truncate -s 0 {} \;

# Remove cloud-init logs
rm -rf /var/lib/cloud/instances/*
```

### 3. **AMI Naming Convention**
- Use descriptive names: `company-app-version-date`
- Example: `mycompany-webserver-lamp-2024-01-15`
- Include version tags for tracking

### 4. **AMI Management**
- Regularly update base AMIs
- Implement automated AMI creation
- Set up AMI lifecycle policies
- Monitor AMI usage and costs

## AMI Creation Steps (Detailed)

### 1. Prepare Instance for AMI Creation
```bash
# Connect to your instance and run cleanup
sudo yum update -y
sudo package-cleanup --oldkernels --count=1

# Remove unnecessary packages
sudo yum autoremove -y

# Clear package cache
sudo yum clean all

# Run the cleanup script above
```

### 2. Stop Instance (Required for EBS-backed AMIs)
- Navigate to EC2 Dashboard
- Select your instance
- Actions → Instance State → Stop
- Wait for instance to be in "stopped" state

### 3. Create AMI
- Right-click on stopped instance
- Select "Create Image"
- Provide Image name and description
- Configure additional EBS volumes if needed
- Click "Create Image"

### 4. Wait for AMI Creation
- Monitor AMI creation progress in AMIs section
- Status will change from "pending" to "available"
- This process can take several minutes

## Using Custom AMI

### 1. Launch Instance from Custom AMI
- Navigate to AMIs in EC2 Dashboard
- Select your custom AMI
- Click "Launch Instance from AMI"
- Follow standard EC2 launch process

### 2. Verify Custom Configuration
```bash
# SSH into new instance and verify
# Check if your software is installed
httpd -v  # For web server AMI
docker --version  # For Docker AMI
node --version  # For development AMI

# Verify services are running
systemctl status httpd
systemctl status docker
```

## AMI Cost Optimization

### 1. **Storage Costs**
- AMIs incur EBS snapshot storage costs
- Delete unused AMIs to save costs
- Use smaller root volumes when possible

### 2. **AMI Lifecycle Management**
```bash
# AWS CLI command to delete old AMIs
aws ec2 describe-images --owners self --query 'Images[?CreationDate<`2023-01-01`].[ImageId,Name,CreationDate]' --output table

# Delete old AMI (replace ami-12345678 with actual AMI ID)
aws ec2 deregister-image --image-id ami-12345678

# Delete associated snapshots
aws ec2 describe-snapshots --owner-ids self --query 'Snapshots[?Description.contains(@, `ami-12345678`)].[SnapshotId]' --output text | xargs -n 1 aws ec2 delete-snapshot --snapshot-id
```

### 3. **Automated AMI Management**
- Set up Lambda functions for automated AMI creation
- Implement retention policies
- Use AWS Data Lifecycle Manager

## Troubleshooting AMI Issues

### 1. **AMI Creation Fails**
- Ensure instance is stopped (for EBS-backed AMIs)
- Check available storage space
- Verify IAM permissions for AMI creation

### 2. **Instance Won't Boot from Custom AMI**
- Check if required drivers are installed
- Verify boot configuration
- Review CloudWatch logs for boot errors

### 3. **Missing Software/Configuration**
- Verify software was properly installed before AMI creation
- Check if services are enabled for auto-start
- Review installation logs

## Prerequisites

- AWS account with EC2 permissions
- Understanding of EC2 instance management
- Basic Linux/Windows administration skills
- Knowledge of software installation and configuration

## Security Notes

- Remove all sensitive data before creating AMIs
- Use encrypted EBS volumes for sensitive data
- Implement proper access controls for AMI sharing
- Regular security updates for base AMIs
- Never include credentials or keys in AMIs

## Next Steps

After successful AMI creation and usage:
1. Document AMI contents and configuration
2. Set up automated AMI creation pipeline
3. Implement AMI testing procedures
4. Create AMI sharing policies if needed
5. Monitor AMI usage and performance
6. Plan for regular AMI updates and maintenance 