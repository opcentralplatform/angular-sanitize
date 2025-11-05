# Security

## Fixed Vulnerabilities

### CVE-2025-4690 - ReDoS in linky filter (Fixed in v1.5.8)

**Severity**: Medium

**Description**: 
A Regular Expression Denial of Service (ReDoS) vulnerability was identified in the `linky` filter of the ngSanitize module. The vulnerability existed in the URL detection regular expression which used a pattern susceptible to catastrophic backtracking. An attacker could exploit this by providing carefully crafted input, leading to excessive CPU consumption and potential denial of service.

**Affected Versions**: 
All versions prior to 1.5.8

**Fixed Version**: 
1.5.8

**Fix Details**:
The vulnerable regex pattern:
```javascript
/((ftp|https?):\/\/|(www\.)|(mailto:)?[A-Za-z0-9._%+-]+@)\S*[^\s.;,(){}<>"\u201d\u2019]/i
```

Was replaced with:
```javascript
/((ftp|https?):\/\/|(www\.)|(mailto:)?[A-Za-z0-9._%+-]+@)[\S]+?[^\s.;,(){}<>"\u201d\u2019]/i
```

The key change is replacing the greedy quantifier `\S*` with a non-greedy quantifier `[\S]+?`, which prevents excessive backtracking while maintaining the same URL matching functionality.

**Mitigation**:
Upgrade to version 1.5.9 or later. If upgrading is not immediately possible, avoid using the `linky` filter with untrusted user input.

## Reporting Security Issues

If you discover a security vulnerability in this package, please report it by creating an issue in the repository with the tag "security".

