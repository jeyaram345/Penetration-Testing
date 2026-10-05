# WordPress Penetration Testing Checklist

> Concise checklist based on the provided WordPress Penetration Testing PDF. Use only on systems you own or are explicitly authorized to assess.

## 1. Reconnaissance

- [ ] Identify WordPress version
- [ ] Identify web server / PHP version
- [ ] Identify installation path
- [ ] Enumerate themes
- [ ] Enumerate plugins
- [ ] Enumerate usernames
- [ ] Check directory listing
- [ ] Check exposed upload directories
- [ ] Check exposed configuration/backup files

## 2. WPScan

Use WPScan for WordPress-specific reconnaissance:

```bash
wpscan --url <TARGET>
```

Review:

- WordPress core version
- Installed themes and versions
- Installed plugins and versions
- User enumeration
- Known vulnerable components

If authorized, configure WPScan for authenticated or proxy-protected targets as required.

## 3. Authentication

Check:

- [ ] Username enumeration
- [ ] Weak/default credentials
- [ ] Brute-force protection
- [ ] Rate limiting
- [ ] CAPTCHA
- [ ] Account lockout
- [ ] Password policy
- [ ] Session management
- [ ] Secure cookies
- [ ] HTTPS

## 4. Component Security

Check for:

- [ ] Outdated WordPress core
- [ ] Vulnerable plugins
- [ ] Vulnerable themes
- [ ] File-upload vulnerabilities
- [ ] Insecure permissions
- [ ] Exposed configuration files
- [ ] Directory listing
- [ ] Known CVEs

## 5. Administrative Security

If authorized admin access is available:

- [ ] Review administrator accounts
- [ ] Review user roles
- [ ] Remove unused plugins/themes
- [ ] Check plugin installation permissions
- [ ] Check file-editing capabilities
- [ ] Check WordPress update status
- [ ] Review security configuration

## 6. Proxy / HTTP Authentication

For applications behind a proxy or HTTP authentication layer:

- [ ] Verify protected resources require authentication
- [ ] Check authentication boundaries
- [ ] Verify proxy configuration
- [ ] Check for unintended direct access
- [ ] Test the application through the authorized proxy

## 7. Vulnerability Validation

Validate confirmed findings using the least invasive proof of concept necessary.

Prioritize:

1. Authentication bypass
2. Privilege escalation
3. Arbitrary file upload
4. Sensitive file exposure
5. Remote code execution
6. Database exposure

Avoid persistence, destructive actions, or reverse shells unless explicitly authorized.

## 8. Reporting

For every finding, record:

- Finding
- Affected component
- Version
- Evidence
- Severity
- Impact
- Reproduction summary
- Remediation
- Retest result

### Common Remediation

- Update WordPress core
- Update/remove vulnerable plugins
- Update/remove vulnerable themes
- Disable unused components
- Enable MFA
- Implement login rate limiting
- Disable directory listing
- Restrict file permissions
- Protect configuration files
- Enforce HTTPS
- Maintain regular backups

## 9. Quick Checklist

### Recon
- [ ] Core version
- [ ] Server/PHP version
- [ ] Themes
- [ ] Plugins
- [ ] Users
- [ ] Files/directories

### Authentication
- [ ] Login security
- [ ] Enumeration
- [ ] Weak credentials
- [ ] Brute-force protection
- [ ] CAPTCHA
- [ ] Sessions
- [ ] MFA

### Vulnerabilities
- [ ] Core CVEs
- [ ] Plugin CVEs
- [ ] Theme CVEs
- [ ] File upload
- [ ] Configuration exposure
- [ ] Permissions

### Final
- [ ] Validate safely
- [ ] Capture evidence
- [ ] Assign severity
- [ ] Recommend remediation
- [ ] Retest

## Disclaimer

For authorized security assessments, labs, CTFs, and systems where you have explicit permission to test.
