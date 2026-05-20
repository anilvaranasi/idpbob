# Fluent CLI Setup Guide for ServiceNow (Windows)

## Issue Identified
You have Node.js v24.15.0 installed, but PowerShell execution policy is blocking npm. Let's fix this!

## Step 1: Fix PowerShell Execution Policy

### Option A: Temporary Fix (Recommended for Testing)
Open PowerShell **as Administrator** and run:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### Option B: Use Command Prompt Instead
Alternatively, use **Command Prompt (cmd.exe)** instead of PowerShell for npm commands. Command Prompt doesn't have execution policy restrictions.

### Verify npm Works
After fixing the execution policy, test npm:

```powershell
npm --version
```

You should see a version number (e.g., 10.x.x).

## Step 2: Install Fluent CLI

Once npm is working, install the ServiceNow CLI:

```powershell
npm install -g @servicenow/cli
```

**Note**: If you get permission errors, try:
```powershell
npm install -g @servicenow/cli --force
```

Verify installation:

```powershell
snc --version
```

## Step 3: Configure Your ServiceNow Instance

### Interactive Setup (Easiest)

```powershell
snc configure profile set
```

You'll be prompted for:
- **Profile name**: e.g., "dev" or "myinstance"
- **Instance URL**: e.g., `https://dev12345.service-now.com`
- **Username**: Your ServiceNow username
- **Password**: Your ServiceNow password

### Command Line Setup

```powershell
snc configure profile set --profile dev --instance-url https://your-instance.service-now.com --username your.username --password your.password
```

**Replace**:
- `dev` with your profile name
- `https://your-instance.service-now.com` with your actual instance URL
- `your.username` with your ServiceNow username
- `your.password` with your ServiceNow password

## Step 4: Test Connection

```powershell
snc instance info
```

This should display your ServiceNow instance information.

## Common Fluent Commands

### Profile Management

```powershell
# List all configured profiles
snc configure profile list

# Switch between profiles
snc configure profile set --profile dev

# View current profile
snc configure profile show
```

### Application Development

```powershell
# Create new application
snc app create

# Pull application from instance
snc app pull --scope x_your_app_scope

# Push changes to instance
snc app push

# Deploy application
snc app deploy
```

### Table Operations

```powershell
# List tables
snc table list

# Get records from a table
snc table get --table incident --limit 10

# Create a record
snc table create --table incident --data "{\"short_description\":\"Test\"}"
```

### Instance Operations

```powershell
# Open instance in browser
snc instance open

# Get instance information
snc instance info
```

## Troubleshooting

### 1. PowerShell Execution Policy Error

**Error**: "running scripts is disabled on this system"

**Solutions**:
- Run PowerShell as Administrator and execute:
  ```powershell
  Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
  ```
- OR use Command Prompt (cmd.exe) instead of PowerShell

### 2. npm Command Not Found

**Solution**: Add Node.js to your PATH:
1. Search for "Environment Variables" in Windows
2. Edit "Path" under System Variables
3. Add: `C:\Program Files\nodejs\`
4. Restart your terminal

### 3. Permission Errors During Installation

**Solution**: Run as Administrator or use:
```powershell
npm install -g @servicenow/cli --force
```

### 4. SSL Certificate Errors

For development instances with self-signed certificates:
```powershell
snc configure profile set --profile dev --instance-url https://your-instance.service-now.com --username your.username --password your.password --insecure
```

**Warning**: Only use `--insecure` for development instances, never for production!

### 5. Corporate Proxy Issues

If behind a corporate proxy:
```powershell
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080
```

## Quick Start Workflow

Here's a complete workflow to get you started:

```powershell
# 1. Fix PowerShell (if needed) - Run as Administrator
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# 2. Verify npm works
npm --version

# 3. Install Fluent CLI
npm install -g @servicenow/cli

# 4. Verify Fluent installation
snc --version

# 5. Configure your instance
snc configure profile set

# 6. Test connection
snc instance info

# 7. Start developing!
snc app create
```

## Alternative: Use Command Prompt

If you prefer to avoid PowerShell execution policy issues:

1. Press `Win + R`
2. Type `cmd` and press Enter
3. Run all npm and snc commands in Command Prompt

## VS Code Integration

For better development experience:

1. Open VS Code
2. Press `Ctrl+Shift+X` (Extensions)
3. Search for "ServiceNow Extension for VS Code"
4. Install the official extension
5. Configure it to use your Fluent profile

## Best Practices

1. **Use Multiple Profiles**: Create separate profiles for dev, test, and prod
2. **Secure Credentials**: Consider OAuth instead of passwords for production
3. **Version Control**: Use Git with your ServiceNow applications
4. **Regular Updates**: Keep Fluent updated:
   ```powershell
   npm update -g @servicenow/cli
   ```

## Need More Help?

If you encounter issues, please provide:
- The exact error message
- Which step you're on
- Whether you're using PowerShell or Command Prompt
- Your Node.js version: `node --version`
- Your npm version: `npm --version`

## Additional Resources

- [ServiceNow CLI Documentation](https://developer.servicenow.com/dev.do#!/reference/cli/latest)
- [ServiceNow Developer Portal](https://developer.servicenow.com)
- [PowerShell Execution Policies](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies)