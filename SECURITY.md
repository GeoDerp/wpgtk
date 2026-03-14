# Security Policy

## Table of Contents
- [Dependency Vulnerability Management](#dependency-vulnerability-management)
- [Automated Security Tools](#automated-security-tools)
- [Running Scans Locally](#running-scans-locally)
- [Interpreting Results](#interpreting-results)
- [Updating Dependencies](#updating-dependencies)
- [Reporting a Vulnerability](#reporting-a-vulnerability)
- [Security Best Practices](#security-best-practices)
- [GitHub Security Features](#github-security-features)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

## Dependency Vulnerability Management

This project uses multiple automated tools to ensure dependencies are secure and up-to-date:

1. **Dependabot**: Automated dependency updates
2. **pip-audit**: PyPA's official vulnerability scanner
3. **safety**: Python package vulnerability checker
4. **bandit**: Python code security linter
5. **CodeQL**: GitHub's semantic code analysis

All these tools run automatically on every push, pull request, and weekly via scheduled workflows.

## Automated Security Tools

### Dependabot
- **Configuration**: `.github/dependabot.yml`
- **What it does**: 
  - Monitors Python packages and GitHub Actions for updates
  - Creates PRs for security vulnerabilities automatically
  - Groups minor updates together
- **When it runs**: Weekly on Mondays at 9:00 AM UTC
- **Where to see results**: 
  - Security tab → Dependabot alerts
  - Pull requests created by dependabot

### Security Scan Workflow
- **Configuration**: `.github/workflows/security-scan.yml`
- **What it does**: Runs pip-audit, safety, and bandit on the codebase
- **When it runs**: 
  - On every push to main/master/develop branches
  - On every pull request
  - Weekly on Mondays at 9:00 AM UTC
  - Can be triggered manually
- **Where to see results**: 
  - Actions tab → "Security - Dependency Vulnerability Scan"
  - Workflow summaries
  - Downloadable artifacts (JSON reports)

### CodeQL Analysis
- **Configuration**: `.github/workflows/codeql-analysis.yml`
- **What it does**: Analyzes Python code for security vulnerabilities
- **When it runs**: 
  - On every push to main/master/develop branches
  - On every pull request
  - Weekly on Mondays at 6:00 AM UTC
- **Where to see results**: Security tab → Code scanning alerts

## Running Scans Locally

### Prerequisites
```bash
# Install the package
pip install .

# Install security tools
pip install pip-audit safety bandit
```

### pip-audit
```bash
# Basic scan
pip-audit

# Detailed scan with descriptions
pip-audit --desc

# Output as JSON
pip-audit --format json -o audit-report.json

# Scan specific requirements file
pip-audit -r requirements.txt
```

### safety
```bash
# Basic scan
safety check

# Full report with more details
safety check --full-report

# Output as JSON
safety check --json

# Using newer 'scan' command (recommended)
safety scan
```

### bandit
```bash
# Scan the wpgtk directory
bandit -r wpgtk

# Generate detailed report
bandit -r wpgtk -f json -o bandit-report.json

# Scan with specific severity level
bandit -r wpgtk -ll  # Low level and above
bandit -r wpgtk -lll # High confidence only
```

### Checking for outdated packages
```bash
# List outdated packages
pip list --outdated

# Using pip-audit to check versions
pip-audit --desc --fix-dry-run
```

## Interpreting Results

### pip-audit Results

**Output format:**
```
Name         Version    ID                  Fix Versions  Description
------------ ---------- ------------------- ------------- -----------
package-name 1.0.0      PYSEC-2024-123     1.0.1, 1.1.0  Description of vulnerability
```

**What to do:**
1. Check the Fix Versions column
2. Update to a fixed version in `pyproject.toml`
3. Test the changes
4. Commit and push

### safety Results

**Common vulnerability levels:**
- 🔴 **Critical/High**: Update immediately
- 🟡 **Medium**: Update when possible
- 🟢 **Low**: Consider updating

**What to do:**
1. Review the CVE/vulnerability details
2. Check if a patch is available
3. Update dependencies
4. Re-run safety to verify fix

### bandit Results

**Severity levels:**
- **High**: Security issue that should be fixed
- **Medium**: Potential security issue
- **Low**: Minor security concern

**Common findings:**
- B311: Weak random number generation (use `secrets` module for crypto)
- B404: Import of subprocess (review for command injection risks)
- B603: Subprocess without shell=True (generally safe, just informational)

**What to do:**
1. Review the code location
2. Determine if it's a false positive or real issue
3. Fix security issues or document why they're safe
4. Re-run bandit to verify

### CodeQL Results

**Where to find:** Security tab → Code scanning alerts

**Severity:**
- **Critical**: Fix immediately
- **High**: Fix soon
- **Medium**: Review and fix
- **Low**: Consider fixing

**What to do:**
1. Click on the alert for detailed information
2. Review the code path and data flow
3. Apply suggested fixes or implement your own
4. Mark as false positive if applicable (with justification)

## Updating Dependencies

### Method 1: Manual Update in pyproject.toml

```toml
[project]
dependencies = [
    "Pillow>=10.0.0",  # Updated version
    "pywal>=3.3.0",
]
```

### Method 2: Using pip-compile (recommended for lock files)

```bash
# Install pip-tools
pip install pip-tools

# Generate requirements.txt from pyproject.toml
pip-compile pyproject.toml

# Update all dependencies
pip-compile --upgrade pyproject.toml
```

### Method 3: Accept Dependabot PR

1. Go to Pull Requests
2. Find Dependabot PR
3. Review changes
4. Click "Merge pull request"

### After Updating

1. **Test the application:**
   ```bash
   # Install updated dependencies
   pip install -e .
   
   # Run any tests
   python -m pytest  # if tests exist
   
   # Test the CLI
   wpg --help
   ```

2. **Re-run security scans:**
   ```bash
   pip-audit
   safety check
   bandit -r wpgtk
   ```

3. **Commit changes:**
   ```bash
   git add pyproject.toml
   git commit -m "deps: update dependencies to fix vulnerabilities"
   git push
   ```

### Current Dependencies

This project's dependencies are defined in `pyproject.toml`:
- `Pillow>=4.2.1` - Python Imaging Library
- `pywal>=3.3.0` - Color scheme generator

### Supported Versions

We recommend always using the latest version of wpgtk. Security updates are applied to the main branch.

| Version | Supported          |
| ------- | ------------------ |
| Latest  | :white_check_mark: |
| < 6.0   | :x:                |

## Reporting a Vulnerability

If you discover a security vulnerability in wpgtk, please report it by:

1. **DO NOT** open a public issue
2. Email the maintainers at: fmorataya.04@gmail.com
3. Include:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

We will respond within 48 hours and work on a fix. Once the vulnerability is patched, we'll credit you in the release notes (unless you prefer to remain anonymous).

## GitHub Security Features

### Enabling Security Features

1. **Dependabot alerts**: Should be enabled by default
2. **Code scanning**: Enabled via CodeQL workflow
3. **Secret scanning**: Enable in Settings → Security → Secret scanning

### Configuring Alert Notifications

1. Go to Settings → Notifications
2. Under "Dependabot alerts", choose your notification preference
3. Under "Security alerts", enable notifications

### Security Overview

Navigate to the Security tab to see:
- **Overview**: Summary of all security issues
- **Dependabot**: Dependency vulnerability alerts
- **Code scanning**: CodeQL analysis results
- **Secret scanning**: Leaked secrets (if enabled)

## Security Best Practices

### For Contributors

When contributing to this project:

1. **Never commit secrets**: API keys, tokens, passwords, etc.
2. **Keep dependencies updated**: Use the latest secure versions
3. **Review Dependabot PRs**: Help maintain security by reviewing and merging updates
4. **Run security scans**: Test your changes with `pip-audit` and `bandit` before submitting PRs
5. **Follow secure coding practices**: Validate inputs, handle errors properly, avoid unsafe operations

### General Best Practices

1. **Review Dependabot PRs promptly**: Security updates should be merged quickly
2. **Don't ignore alerts**: Even low-severity issues can become critical
3. **Test updates**: Always test dependency updates before deploying
4. **Keep dependencies minimal**: Fewer dependencies = smaller attack surface
5. **Pin versions appropriately**: Use `>=` for flexibility, but pin specific versions in production
6. **Regular audits**: Review dependencies quarterly even without alerts
7. **Subscribe to security advisories**: Watch for CVEs in major dependencies

## Troubleshooting

### pip-audit fails with "No module found"
**Solution**: Make sure you've installed the package first: `pip install .`

### safety check fails with network error
**Solution**: safety requires internet access. Use pip-audit instead for offline scanning.

### bandit reports false positives
**Solution**: Add inline comments to suppress:
```python
# nosec B404
from subprocess import Popen
```

### Dependabot PRs fail tests
**Solution**: Review the dependency changes, fix any breaking changes, then merge.

## Additional Resources

- [pip-audit Documentation](https://github.com/pypa/pip-audit)
- [safety Documentation](https://docs.safetycli.com/)
- [bandit Documentation](https://bandit.readthedocs.io/)
- [CodeQL Documentation](https://codeql.github.com/docs/)
- [Dependabot Documentation](https://docs.github.com/en/code-security/dependabot)
- [GitHub Security Best Practices](https://docs.github.com/en/code-security/getting-started/securing-your-repository)
- [Python Security Best Practices](https://python.readthedocs.io/en/stable/library/security_warnings.html)
- [OWASP Python Security](https://owasp.org/www-project-python-security/)

## Questions?

If you have questions about security scanning or need help interpreting results:
1. Check this guide first
2. Open an issue with the "security" label
3. Contact the maintainers at: fmorataya.04@gmail.com
