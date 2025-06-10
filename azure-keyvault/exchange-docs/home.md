# Azure Key Vault Integration API

This API provides secure access to Azure Key Vault secrets through MuleSoft integration endpoints.

## Base URL
```
http://localhost:8081
```

## Authentication
The API handles Azure OAuth 2.0 authentication internally using client credentials flow.

## Endpoints

### GET /KeyVault_getSecrets
Lists all secrets available in the configured Azure Key Vault.

**Response:** 200 OK
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
  ],
  "nextLink": null
}
```

### GET /KeyVault_getSecret
Retrieves the specific secret 'ExamplePassword' from Azure Key Vault.

**Response:** 200 OK
```json
{
  "value": "MySecretValue",
  "id": "https://mulesoftkeys.vault.azure.net/secrets/ExamplePassword/current-version",
  "attributes": {
    "enabled": true,
    "created": 1640995200,
    "updated": 1640995200,
    "recoveryLevel": "Purgeable"
  }
}
```

## Error Responses

### 401 Unauthorized
```json
{
  "error": {
    "code": "Unauthorized",
    "message": "Authentication failed"
  }
}
```

### 403 Forbidden
```json
{
  "error": {
    "code": "Forbidden", 
    "message": "Access denied to Key Vault"
  }
}
```

### 500 Internal Server Error
```json
{
  "error": {
    "code": "InternalServerError",
    "message": "An unexpected error occurred"
  }
}
```

## Usage Examples

### cURL Examples

**List all secrets:**
```bash
curl -X GET http://localhost:8081/KeyVault_getSecrets
```

**Get specific secret:**
```bash
curl -X GET http://localhost:8081/KeyVault_getSecret
```

### Postman Collection
Import the provided Postman collection for easy testing of all endpoints.

## Security Notes
- All communication with Azure services uses HTTPS
- OAuth tokens are automatically managed and refreshed
- Secrets are never logged or exposed in error messages