# Jenkins: Running Processes That Survive After Build Completion

## The Problem

When Jenkins runs a build job, it expects all processes started during the build to finish before the build completes. This can be problematic when you want to:
- Start an application server
- Launch a background service
- Run a monitoring script that should continue after the build

By default, Jenkins will **kill all processes** spawned during a build once the build finishes. This is a safety feature to ensure no orphaned processes are left running.

## The Solution: BUILD_ID=dontKillMe

To tell Jenkins **not to kill** a process after the build completes, you need to set a special environment variable before starting your command.

### Basic Syntax
```bash
BUILD_ID=dontKillMe <your-command>
```

### Real-World Examples

#### Starting a Java Application
```bash
BUILD_ID=dontKillMe nohup java -jar app.jar > app.log 2>&1 &
```

#### Starting a Node.js Server
```bash
BUILD_ID=dontKillMe nohup node server.js > server.log 2>&1 &
```

#### Starting a Python Script
```bash
BUILD_ID=dontKillMe nohup python3 monitor.py > monitor.log 2>&1 &
```

## Understanding the Components

- `BUILD_ID=dontKillMe` - Tells Jenkins to leave this process running
- `nohup` - Prevents the process from stopping when the shell exits
- `> app.log 2>&1` - Redirects output to a log file
- `&` - Runs the process in the background

## Important Notes

### For Jenkins Pipeline
If you're using Jenkins Pipeline (Jenkinsfile), use `JENKINS_NODE_COOKIE` instead:
```groovy
sh 'JENKINS_NODE_COOKIE=dontKillMe nohup java -jar app.jar > app.log 2>&1 &'
```

### Best Practices
1. **Always redirect output** to a log file to avoid Jenkins hanging
2. **Use nohup** to ensure the process continues after Jenkins disconnects
3. **Monitor your processes** - Remember that these processes will keep running until manually stopped

### Stopping Long-Running Processes
Since these processes survive the build, you'll need to stop them manually:

#### Using ps and kill (traditional method)
```bash
# Find the process
ps aux | grep app.jar

# Kill the process
kill <process-id>
```

#### Using pgrep and pkill (easier method)
```bash
# Find process IDs by name
pgrep -f app.jar

# Kill process by name pattern
pkill -f app.jar

# Kill with specific signal (e.g., SIGKILL)
pkill -9 -f app.jar
```

**Tip:** `pgrep` and `pkill` are more convenient because you don't need to manually find and copy process IDs.

## Summary

When you need a process started by Jenkins to continue running after the build:
1. Add `BUILD_ID=dontKillMe` before your command
2. Use `nohup` and run in background with `&`
3. Redirect output to avoid build hanging

This allows you to deploy applications, start services, or run monitoring scripts that persist beyond the Jenkins build lifecycle. 