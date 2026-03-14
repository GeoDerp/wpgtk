# Security Policy

## Dependency Vulnerability Management

This project uses multiple automated tools to ensure dependencies are secure and up-to-date:

### Automated Security Tools

1. **Dependabot** (`.github/dependabot.yml`)
   - Automatically checks for vulnerable dependencies
   - Creates pull requests for security updates
   - Runs weekly on Mondays
   - Covers both Python dependencies and GitHub Actions

2. **Dependency Vulnerability Scanning** (`.github/workflows/security-scan.yml`)
   - **pip-audit**: Official PyPA tool that scans against the OSV (Open Source Vulnerabilities) database
   - **safety**: Checks Python packages against the Safety DB for known vulnerabilities
   - **bandit**: Security linter that analyzes Python code for common security issues
   - Runs on every push, pull request, and weekly via scheduled workflow
   - Generates detailed reports available as workflow artifacts

3. **CodeQL Analysis** (`.github/workflows/codeql-analysis.yml`)
   - GitHub's semantic code analysis engine
   - Finds security vulnerabilities and coding errors in code
   - Runs on push, pull request, and weekly schedule
   - Results visible in Security tab > Code scanning alerts

### How to Use These Tools

#### Viewing Security Alerts

1. **Dependabot Alerts**: Navigate to the "Security" tab in the GitHub repository, then select "Dependabot alerts"
2. **Workflow Scan Results**: Check the Actions tab for "Security - Dependency Vulnerability Scan" workflow runs
3. **CodeQL Alerts**: Navigate to "Security" tab > "Code scanning" for detailed code analysis results

#### Responding to Vulnerabilities

When a vulnerability is detected:

1. **Review the Alert**: Check the severity, affected versions, and available patches
2. **Update Dependencies**: 
   - For Dependabot alerts: Review and merge the automated PR
   - For manual updates: Update version in `pyproject.toml`
3. **Test Changes**: Ensure updated dependencies don't break functionality
4. **Verify Fix**: Re-run security scans to confirm vulnerability is resolved

#### Manual Security Scans

You can manually run security scans locally:

```bash
# Install security tools
pip install pip-audit safety bandit

# Run pip-audit
pip-audit

# Run safety check
safety check

# Run bandit on the codebase
bandit -r wpgtk

# Check for outdated packages
pip list --outdated
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

## Security Best Practices for Contributors

When contributing to this project:

1. **Never commit secrets**: API keys, tokens, passwords, etc.
2. **Keep dependencies updated**: Use the latest secure versions
3. **Review Dependabot PRs**: Help maintain security by reviewing and merging updates
4. **Run security scans**: Test your changes with `pip-audit` and `bandit` before submitting PRs
5. **Follow secure coding practices**: Validate inputs, handle errors properly, avoid unsafe operations

## Additional Resources

- [Python Security Best Practices](https://python.readthedocs.io/en/stable/library/security_warnings.html)
- [OWASP Python Security](https://owasp.org/www-project-python-security/)
- [pip-audit Documentation](https://pypi.org/project/pip-audit/)
- [Safety Documentation](https://pyup.io/safety/)
- [Bandit Documentation](https://bandit.readthedocs.io/)
- [GitHub Security Features](https://docs.github.com/en/code-security)
