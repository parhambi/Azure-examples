# Azure Key Vault Integration with MuleSoft

A MuleSoft application demonstrating secure integration with Azure Key Vault using OAuth 2.0 client credentials flow for retrieving secrets.

## Overview

This project showcases how to securely connect to Azure Key Vault from MuleSoft applications to retrieve secrets and sensitive configuration data. It implements industry best practices for authentication, error handling, and secure credential management.

## Architecture

```
┌─────────────────┐    OAuth 2.0     ┌─────────────────┐
│   MuleSoft App  │ ────────────────> │   Azure AD      │
│                 │ <──────────────── │                 │
└─────────────────┘    Access Token   └─────────────────┘
         │
         │ REST API Call
         │ (Bearer Token)
         ▼
┌─────────────────┐
│ Azure Key Vault │
│                 │
└─────────────────┘
```

## Features

- **Secure Authentication**: OAuth 2.0 client credentials flow
- **Multiple Operations**: List all secrets or retrieve specific secrets
- **Environment Configuration**: Externalized configuration for different environments
- **Error Handling**: Comprehensive error handling patterns
- **REST API Endpoints**: Clean HTTP endpoints for integration

## Project Structure

```
azure-keyvault/
├── src/main/
│   ├── mule/
│   │   └── azure-keyvault-example.xml    # Main flow definitions
│   └── resources/
│       ├── config.yaml                   # Configuration properties
│       └── log4j2.xml                   # Logging configuration
├── src/test/
│   ├── munit/                           # MUnit test files
│   └── resources/
├── exchange-docs/                       # API documentation
├── pom.xml                             # Maven configuration
└── mule-artifact.json                  # Mule artifact descriptor
```

## Prerequisites

- MuleSoft Anypoint Studio 7.x or later
- Mule Runtime 4.9.5+
- Java 17
- Azure subscription with Key Vault access
- Azure AD application registration

## Azure Setup

### 1. Create Azure Key Vault

```bash
# Create resource group
az group create --name mulesoft-rg --location eastus

# Create Key Vault
az keyvault create --name mulesoftkeys --resource-group mulesoft-rg --location eastus

# Add a sample secret
az keyvault secret set --vault-name mulesoftkeys --name ExamplePassword --value "MySecretValue"
```

### 2. Register Azure AD Application

1. Go to Azure Portal → Azure Active Directory → App registrations
2. Click "New registration"
3. Name: `MuleSoft-KeyVault-App`
4. Account types: "Accounts in this organizational directory only"
5. Register the application
6. Note the **Application (client) ID** and **Directory (tenant) ID**
7. Go to "Certificates & secrets" → Generate new client secret
8. Copy the secret value immediately (it won't be shown again)

### 3. Grant Key Vault Permissions

```bash
# Grant Key Vault access to your application
az keyvault set-policy --name mulesoftkeys \
  --spn <your-client-id> \
  --secret-permissions get list
```

## Configuration

### Environment Variables

Set these environment variables before running the application:

```bash
export YOURCLIENTSECRET="your-azure-ad-client-secret"
export YOUR_AZURE_SUBSCRIPTION_ID="your-azure-tenant-id"
```

### config.yaml Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `client_id` | Azure AD Application ID | `0bc6c55f-57e9-4554-8c64-b8f24db92742` |
| `YourClient_Secret` | Reference to client secret | `${YOURCLIENTSECRET}` |
| `token_url` | Azure AD OAuth token endpoint | `https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token` |
| `scope` | Key Vault access scope | `https://vault.azure.net/.default` |

## API Endpoints

### 1. List All Secrets

**GET** `/KeyVault_getSecrets`

Lists all secrets available in the Azure Key Vault.

**Response Example:**
```json
{
  "value": [
    {
      "id": "https://mulesoftkeys.vault.azure.net/secrets/ExamplePassword",
      "attributes": {
        "enabled": true,
        "created": 1640995200,
        "updated": 1640995200
      }
    }
  ]
}
```

### 2. Get Specific Secret

**GET** `/KeyVault_getSecret`

Retrieves the `ExamplePassword` secret from Azure Key Vault.

**Response Example:**
```json
{
  "value": "MySecretValue",
  "id": "https://mulesoftkeys.vault.azure.net/secrets/ExamplePassword/version",
  "attributes": {
    "enabled": true,
    "created": 1640995200,
    "updated": 1640995200
  }
}
```

## Running the Application

### Local Development

1. **Configure Environment Variables:**
   ```bash
   export YOURCLIENTSECRET="your-secret-here"
   export YOUR_AZURE_SUBSCRIPTION_ID="your-tenant-id"
   ```

2. **Run in Anypoint Studio:**
   - Import project into Anypoint Studio
   - Right-click project → Run As → Mule Application

3. **Test the Endpoints:**
   ```bash
   # List all secrets
   curl http://localhost:8081/KeyVault_getSecrets
   
   # Get specific secret
   curl http://localhost:8081/KeyVault_getSecret
   ```

### Maven Build

```bash
# Clean and package
mvn clean package

# Run with Maven
mvn mule:run
```

## Deployment

### CloudHub Deployment

```bash
# Deploy to CloudHub
mvn clean package mule:deploy -Dmule.artifact=target/azure-keyvault-1.0.0-SNAPSHOT-mule-application.jar
```

### Runtime Fabric Deployment

1. Build the application JAR
2. Upload to Runtime Fabric
3. Configure environment variables in deployment settings

## Security Best Practices

- ✅ **Never commit secrets** to version control
- ✅ **Use environment variables** for sensitive data
- ✅ **Implement proper error handling** to avoid information leakage
- ✅ **Use HTTPS** for all external communications
- ✅ **Rotate client secrets** regularly
- ✅ **Monitor access logs** for suspicious activity
- ✅ **Apply principle of least privilege** for Key Vault permissions

## Monitoring and Troubleshooting

### Common Issues

1. **Authentication Failed (401)**
   - Verify client ID and secret are correct
   - Check if client secret has expired
   - Ensure proper Key Vault permissions

2. **Key Vault Access Denied (403)**
   - Verify the application has `get` and `list` permissions
   - Check if Key Vault access policies are configured correctly

3. **Network Connectivity Issues**
   - Verify firewall rules allow HTTPS traffic
   - Check DNS resolution for Azure endpoints

### Monitoring

- Enable logging in `log4j2.xml` for debugging
- Use Anypoint Monitoring for production deployments
- Set up alerts for authentication failures

## Development Guidelines

### Code Standards

- Follow MuleSoft naming conventions
- Use descriptive names for flows and variables
- Implement comprehensive error handling
- Add meaningful log messages for troubleshooting

### Testing

- Create MUnit tests for each flow
- Test with valid and invalid credentials
- Verify error handling scenarios
- Test network failure scenarios

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-feature`)
3. Follow MuleSoft coding standards
4. Add appropriate tests
5. Submit a pull request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For issues and questions:
- Check the [troubleshooting section](#monitoring-and-troubleshooting)
- Review Azure Key Vault documentation
- Contact the development team

## References

- [Azure Key Vault REST API](https://docs.microsoft.com/en-us/rest/api/keyvault/)
- [Azure AD OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)
- [MuleSoft HTTP Connector](https://docs.mulesoft.com/connectors/http/http-connector)
- [DataWeave 2.0 Documentation](https://docs.mulesoft.com/dataweave/2.4/)