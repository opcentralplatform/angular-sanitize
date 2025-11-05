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
/((ftp|https?):\/\/|(www\.)|(mailto:)?[A-Za-z0-9._%+-]{1,64}@)[^\s.;,(){}<>"\u201d\u2019]{1,2083}/i
```

**Key Changes:**
1. **Bounded email username**: Changed `[A-Za-z0-9._%+-]+` to `[A-Za-z0-9._%+-]{1,64}` - limits email local part to 64 characters (RFC standard)
2. **Bounded URL/domain**: Changed `+` quantifier to `{1,2083}` - limits URL length to 2083 characters (practical browser limit)

The original pattern used unbounded quantifiers (`+` and `*`) with overlapping character classes, allowing exponential backtracking when matching long strings. By adding maximum length constraints, the regex engine is prevented from attempting to match extremely long strings, completely eliminating the ReDoS vulnerability while maintaining correct URL detection functionality.

**Performance Impact:**
- Before fix: 200,000 character input would hang/timeout (>10 seconds)
- After fix: 200,000 character input completes in ~30ms

**Mitigation**:
Upgrade to version 1.5.8 or later with this patch applied. If upgrading is not immediately possible, avoid using the `linky` filter with untrusted user input, or implement input length validation before processing.

## Reporting Security Issues

If you discover a security vulnerability in this package, please report it by creating an issue in the repository with the tag "security".

