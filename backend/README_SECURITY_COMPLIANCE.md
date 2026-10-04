# Security and Compliance

Complete guide to Security and Compliance features

## Overview

The Security and Compliance system includes:
- **Audit Logging**: Recording all security activities
- **Encryption**: Encryption of sensitive data
- **Security Policies**: Managing security policies
- **Compliance Reporting**: Compliance reports
- **Access Control**: Advanced access control

## Audit Logging

### Event Types

The system records all of the following events:

#### Authentication Events:
- `login`: successful login
- `logout`: logout
- `login_failed`: failed login
- `password_change`: password change
- `token_refresh`: token renewal

#### Data Access Events:
- `data_view`: data view
- `data_create`: data creation
- `data_update`: data update
- `data_delete`: data deletion
- `data_export`: data export

#### User Management Events:
- `user_create`: user creation
- `user_update`: user update
- `user_delete`: user deletion
- `role_change`: role change

#### Security Events:
- `permission_denied`: permission denied
- `unauthorized_access`: unauthorized access
- `suspicious_activity`: suspicious activity

### Audit Log Schema

```sql
CREATE TABLE audit_logs (
    audit_id UUID PRIMARY KEY,
    event_type VARCHAR(50) NOT NULL,
    user_id VARCHAR(50),
    username VARCHAR(100),
    resource_type VARCHAR(50),
    resource_id VARCHAR(100),
    action VARCHAR(100),
    details JSONB,
    ip_address VARCHAR(45),
    user_agent TEXT,
    success BOOLEAN DEFAULT TRUE,
    timestamp TIMESTAMPTZ NOT NULL
);
```

## Security Policies

### Password Policy

- **Min Length**: At least 8 characters
- **Require Uppercase**: Requires uppercase letters
- **Require Lowercase**: Requires lowercase letters
- **Require Numbers**: Requires numbers
- **Require Special Chars**: Requires special characters
- **Max Age**: 90 days
- **History Count**: 5 previous passwords

### Session Policy

- **Timeout**: 30 minutes
- **Max Concurrent Sessions**: 5
- **Require HTTPS**: Yes
- **Same Site**: Strict

### Data Retention Policy

- **Sensor Data**: 365 days
- **Audit Logs**: 2555 days (7 years)
- **Alerts**: 180 days
- **ML Predictions**: 365 days

### Access Control Policy

- **Require MFA**: No (configurable)
- **IP Whitelist**: List of allowed IPs
- **IP Blacklist**: List of disallowed IPs
- **Rate Limit**: 100 request per minute

## Encryption

### Encryption Service

- **Algorithm**: Fernet (symmetric encryption)
- **Key Management**: From an environment variable or generated
- **Sensitive Fields**: password, token, secret, api_key, credential

### Usage

```python
from app.services.encryption_service import EncryptionService

encryption = EncryptionService()

# Encrypt
encrypted = encryption.encrypt("sensitive_data")

# Decrypt
decrypted = encryption.decrypt(encrypted)

# Encrypt dictionary
encrypted_dict = encryption.encrypt_dict({
    "password": "secret123",
    "api_key": "key123"
})
```

## Compliance Reporting

### Report Types

1. **Full Report**: Complete report with all sections
2. **Summary Report**: Report summary
3. **Detailed Report**: Detailed report

### Report Sections

1. **Audit Logs Summary**
   - Total events
   - Successful/Failed events
   - Event types distribution
   - Top users

2. **User Activity**
   - Active users
   - User actions
   - Last activity

3. **Security Events**
   - Failed logins
   - Permission denials
   - Unauthorized access
   - Suspicious activity

4. **Data Access**
   - Data operations
   - Data exports
   - Export users

5. **Alerts Summary**
   - Total alerts
   - Resolved/Unresolved
   - Severity distribution

6. **Compliance Status**
   - Password policy compliance
   - Session management compliance
   - Audit logging compliance
   - Encryption compliance

## API Endpoints

### Compliance

#### GET `/api/v1/compliance/report`
Generate compliance report (admin only)

**Query Parameters:**
- `start_date` (optional): Start date
- `end_date` (optional): End date
- `days` (default: 30): Number of days
- `report_type` (default: "full"): full, summary, detailed
- `format` (default: "json"): json, csv

#### GET `/api/v1/compliance/audit-logs`
Get audit logs (admin only)

**Query Parameters:**
- `user_id` (optional): Filter by user
- `event_type` (optional): Filter by event type
- `resource_type` (optional): Filter by resource
- `start_time` (optional): Start time
- `end_time` (optional): End time
- `limit` (default: 100)
- `offset` (default: 0)

#### GET `/api/v1/compliance/user-activity/{user_id}`
Get user activity summary (admin only)

**Query Parameters:**
- `days` (default: 30): Number of days

#### GET `/api/v1/compliance/suspicious-activity`
Detect suspicious activity (admin only)

**Query Parameters:**
- `user_id` (optional): Filter by user
- `hours` (default: 24): Time window

#### GET `/api/v1/compliance/security-policies`
Get security policies (admin only)

**Query Parameters:**
- `policy_type` (optional): Filter by policy type

#### PUT `/api/v1/compliance/security-policies/{policy_type}`
Update security policy (admin only)

## Security Headers

All responses include the following security headers:

- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `X-XSS-Protection: 1; mode=block`
- `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- `Content-Security-Policy: default-src 'self'`

## Rate Limiting

- **Default**: 100 requests per minute per IP
- **Configurable**: Through the security policy
- **Implementation**: RateLimitMiddleware

## Best Practices

1. **Audit Logging**: Log all important actions
2. **Password Policy**: Use a strong password policy
3. **Encryption**: Encrypt sensitive data
4. **Session Management**: Set the session timeout
5. **Compliance Reports**: Review reports regularly
6. **Suspicious Activity**: Monitor suspicious activity

## Compliance Standards

The system is designed for compliance with the following standards:

- **GDPR**: Data protection and privacy
- **SOC 2**: Security controls
- **ISO 27001**: Information security
- **NIST**: Cybersecurity framework

## Data Retention

Retention policies for data types:

- **Sensor Data**: 1 year
- **Audit Logs**: 7 years (compliance requirement)
- **Alerts**: 6 months
- **ML Predictions**: 1 year

## Security Monitoring

### Suspicious Activity Detection

The system automatically detects the following:

1. **Multiple Failed Logins**: More than 5 times in 24 hours
2. **Excessive Permission Denials**: More than 10 times in 24 hours
3. **Unusual Activity Volume**: More than 1000 events in 24 hours

### Alerts

An alert is created for each suspicious activity.

## Integration

### With Authentication:
- Login/logout events logged
- Failed login attempts tracked
- Password changes audited

### With Data Access:
- All data operations logged
- Export operations tracked
- Permission denials recorded

### With User Management:
- User creation/deletion logged
- Role changes audited
- User updates tracked

