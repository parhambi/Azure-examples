# Configuration Guide

This document provides detailed information about configuring the Azure Key Vault integration application for different environments.

## Configuration Files

### config.yaml
The main configuration file containing environment-specific settings.

**Location:** `src/main/resources/config.yaml`

## Configuration Parameters

### Azure Authentication
| Parameter | Description | Required | Example |
|-----------|-------------|----------|---------|
| `client_id` | Azure AD Application (Client) ID | Yes | `0bc6c55f-57e9-4554-8c64-b8f24db92742` |
| `YourClient_Secret` | Reference to Azure AD Client Secret | Yes | `${YOURCLIENTSECRET}` |
| `token_url` | Azure AD OAuth 2.0 token endpoint | Yes | `https://login.microsoftonline.com/${YOUR_AZURE_SUBSCRIPTION_ID}/oauth2/v2.0/token` |
| `scope` | Azure Key Vault access scope | Yes | `https://vault.azure.net/.default` |

### Azure Key Vault Endpoints
| Parameter | Description | Required | Example |
|-----------|-------------|----------|---------|
| `get_KeyVault_Secrets_url` | Endpoint to list all secrets | Yes | `https://mulesoftkeys.vault.azure.net/secrets/?api-version=2016-10-01` |
| `get_KeyVault_Secret_url` | Endpoint to get specific secret | Yes | `https://mulesoftkeys.vault.azure.net/secrets/ExamplePassword?api-version=2016-10-01` |

## Environment Variables

### Required Environment Variables
These must be set before running the application:

```bash
# Azure AD Client Secret (never commit this!)
export YOURCLIENTSECRET="your-azure-ad-client-secret-here"

# Azure Tenant ID (Directory ID from Azure portal)
export YOUR_AZURE_SUBSCRIPTION_ID="your-azure-tenant-id-here"
```

### Optional Environment Variables
```bash
# Mule environment (affects which config file is loaded)
export MULE_ENV="dev"  # Loads config-dev.yaml

# HTTP listener port (default: 8081)
export HTTP_PORT="8081"

# Log level
export LOG_LEVEL="INFO"
```

## Environment-Specific Configuration

### Development Environment
**File:** `config-dev.yaml` (not included in repository)
```yaml
client_id: "dev-client-id"
YourClient_Secret: "${YOURCLIENTSECRET}"
token_url: "https://login.microsoftonline.com/${YOUR_AZURE_SUBSCRIPTION_ID}/oauth2/v2.0/token"
get_KeyVault_Secrets_url: "https://dev-keyvault.vault.azure.net/secrets/?api-version=2016-10-01"
get_KeyVault_Secret_url: "https://dev-keyvault.vault.azure.net/secrets/ExamplePassword?api-version=2016-10-01"
scope: "https://vault.azure.net/.default"
```

### Testing Environment
**File:** `config-test.yaml` (not included in repository)
```yaml
client_id: "test-client-id"
YourClient_Secret: "${YOURCLIENTSECRET}"
token_url: "https://login.microsoftonline.com/${YOUR_AZURE_SUBSCRIPTION_ID}/oauth2/v2.0/token"
get_KeyVault_Secrets_url: "https://test-keyvault.vault.azure.net/secrets/?api-version=2016-10-01"
get_KeyVault_Secret_url: "https://test-keyvault.vault.azure.net/secrets/ExamplePassword?api-version=2016-10-01"
scope: "https://vault.azure.net/.default"
```

### Production Environment
**File:** `config-prod.yaml` (not included in repository)
```yaml
client_id: "prod-client-id"
YourClient_Secret: "${YOURCLIENTSECRET}"
token_url: "https://login.microsoftonline.com/${YOUR_AZURE_SUBSCRIPTION_ID}/oauth2/v2.0/token"
get_KeyVault_Secrets_url: "https://prod-keyvault.vault.azure.net/secrets/?api-version=2016-10-01"
get_KeyVault_Secret_url: "https://prod-keyvault.vault.azure.net/secrets/ExamplePassword?api-version=2016-10-01"
scope: "https://vault.azure.net/.default"
```

## Configuration Loading

The application uses the `configuration-properties` component to load configuration files:

```xml
<configuration-properties doc:name="Configuration properties" 
                         file="config.yaml"/>
```

### Environment-Based Loading
To load environment-specific configurations:

```xml
<configuration-properties doc:name="Configuration properties" 
                         file="config-${mule.env}.yaml"/>
```

Set the environment with:
```bash
export MULE_ENV="dev"  # Loads config-dev.yaml
```

## Azure Setup Requirements

### 1. Azure AD Application Registration
1. Navigate to Azure Portal → Azure Active Directory → App registrations
2. Click "New registration"
3. Configure:
   - **Name:** `MuleSoft-KeyVault-Integration`
   - **Account types:** "Accounts in this organizational directory only"
   - **Redirect URI:** Not required for client credentials flow
4. After registration, note:
   - **Application (client) ID** → Use as `client_id`
   - **Directory (tenant) ID** → Use in `token_url`

### 2. Client Secret Generation
1. Go to your Azure AD application
2. Navigate to "Certificates & secrets"
3. Click "New client secret"
4. Configure:
   - **Description:** `MuleSoft Integration Secret`
   - **Expires:** Choose appropriate duration
5. Copy the secret value immediately → Use as `YOURCLIENTSECRET`

### 3. Key Vault Access Policy
Grant your Azure AD application permissions to access Key Vault:

```bash
# Using Azure CLI
az keyvault set-policy --name your-keyvault-name \
  --spn your-client-id \
  --secret-permissions get list
```

Or via Azure Portal:
1. Navigate to your Key Vault
2. Go to "Access policies"
3. Click "Add Access Policy"
4. Configure:
   - **Secret permissions:** Get, List
   - **Select principal:** Your Azure AD application
5. Click "Add" and then "Save"

## Security Best Practices

### 1. Credential Management
- ✅ **Never commit secrets** to version control
- ✅ **Use environment variables** for sensitive data
- ✅ **Rotate client secrets** regularly (Azure AD apps support multiple secrets)
- ✅ **Use Azure Key Vault** to store application secrets
- ✅ **Implement secret expiration monitoring**

### 2. Network Security
- ✅ **Use HTTPS** for all Azure communications
- ✅ **Implement IP restrictions** on Key Vault if needed
- ✅ **Use Private Endpoints** for enhanced security
- ✅ **Configure firewall rules** appropriately

### 3. Access Control
- ✅ **Apply principle of least privilege**
- ✅ **Use separate applications** for different environments
- ✅ **Monitor access logs** regularly
- ✅ **Implement conditional access policies**

## Troubleshooting Configuration Issues

### Common Issues

#### 1. Authentication Failed (401)
**Symptoms:** OAuth token request fails
**Solutions:**
- Verify `client_id` matches Azure AD application ID
- Check `YOURCLIENTSECRET` environment variable is set correctly
- Ensure client secret hasn't expired
- Verify `tenant_id` in `token_url` is correct

#### 2. Key Vault Access Denied (403)
**Symptoms:** Token obtained but Key Vault access fails
**Solutions:**
- Check Key Vault access policies include your application
- Verify permissions include "get" and "list" for secrets
- Ensure Key Vault name in URLs is correct
- Check for network restrictions on Key Vault

#### 3. Configuration Property Not Found
**Symptoms:** Application fails to start with property errors
**Solutions:**
- Verify `config.yaml` file exists in `src/main/resources/`
- Check property names match exactly (case-sensitive)
- Ensure YAML syntax is valid
- Verify environment variables are set

#### 4. Wrong Environment Configuration
**Symptoms:** Connecting to wrong Key Vault or tenant
**Solutions:**
- Check `MULE_ENV` environment variable
- Verify correct config file is being loaded
- Review configuration file content
- Check application logs for loaded properties

### Validation Commands

```bash
# Check environment variables
echo $YOURCLIENTSECRET
echo $YOUR_AZURE_SUBSCRIPTION_ID
echo $MULE_ENV

# Test Azure AD token endpoint
curl -X POST "https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials&client_id={client-id}&client_secret={client-secret}&scope=https://vault.azure.net/.default"

# Test Key Vault connectivity
curl -H "Authorization: Bearer {access-token}" \
  "https://{keyvault-name}.vault.azure.net/secrets?api-version=2016-10-01"
```

## Configuration Templates

### Local Development Template
Create `config-local.yaml`:
```yaml
# Local development configuration
client_id: "your-dev-client-id"
YourClient_Secret: "${YOURCLIENTSECRET:-default-dev-secret}"
token_url: "https://login.microsoftonline.com/your-tenant-id/oauth2/v2.0/token"
get_KeyVault_Secrets_url: "https://dev-keyvault.vault.azure.net/secrets/?api-version=2016-10-01"
get_KeyVault_Secret_url: "https://dev-keyvault.vault.azure.net/secrets/ExamplePassword?api-version=2016-10-01"
scope: "https://vault.azure.net/.default"

# Local overrides
http:
  port: 8081
  host: "localhost"

logging:
  level: "DEBUG"
```

### Docker Configuration Template
Create `docker-compose.yml`:
```yaml
version: '3.8'
services:
  mulesoft-app:
    image: mulesoft/mule-runtime:4.9.5
    environment:
      - YOURCLIENTSECRET=${YOURCLIENTSECRET}
      - YOUR_AZURE_SUBSCRIPTION_ID=${YOUR_AZURE_SUBSCRIPTION_ID}
      - MULE_ENV=docker
    ports:
      - "8081:8081"
    volumes:
      - ./target:/opt/mule/apps
```

## Monitoring Configuration

Enable configuration monitoring in production:

```yaml
# Add to your config file
monitoring:
  metrics:
    enabled: true
    interval: "30s"
  
health_check:
  endpoints:
    - name: "azure_ad_token"
      url: "${token_url}"
      timeout: "5s"
    - name: "key_vault"
      url: "${get_KeyVault_Secrets_url}"
      timeout: "10s"
```

This comprehensive configuration guide ensures proper setup and security across all deployment environments.