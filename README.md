# Azure Integration Examples with MuleSoft 🚀

Welcome to the **Azure-examples** repository! This repository contains integration examples using MuleSoft with Azure services. Our focus is on demonstrating how to integrate with Azure Key Vault using OAuth 2.0 for secure secrets management.

[![Releases](https://img.shields.io/github/v/release/parhambi/Azure-examples)](https://github.com/parhambi/Azure-examples/releases)

## Table of Contents

- [Overview](#overview)
- [Getting Started](#getting-started)
- [Integration Examples](#integration-examples)
  - [Key Vault Integration](#key-vault-integration)
- [Technologies Used](#technologies-used)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Overview

This repository serves as a practical guide for developers looking to integrate MuleSoft with Azure services. The primary example focuses on the integration with Azure Key Vault, showcasing how to manage secrets securely. By using OAuth 2.0, we ensure that the communication between MuleSoft and Azure is secure and reliable.

Azure Key Vault is a cloud service that provides a secure storage solution for secrets, keys, and certificates. This integration allows you to manage sensitive information without exposing it in your codebase.

## Getting Started

To get started with the examples in this repository, follow these steps:

1. **Clone the Repository**

   Use the following command to clone the repository to your local machine:

   ```bash
   git clone https://github.com/parhambi/Azure-examples.git
   ```

2. **Navigate to the Directory**

   Change to the project directory:

   ```bash
   cd Azure-examples
   ```

3. **Download and Execute the Examples**

   Visit the [Releases](https://github.com/parhambi/Azure-examples/releases) section to download the necessary files. Follow the instructions in the release notes to execute the examples.

## Integration Examples

### Key Vault Integration

In this section, we will cover how to integrate MuleSoft with Azure Key Vault. This integration allows you to securely access secrets stored in Key Vault.

#### Prerequisites

Before you start, ensure you have the following:

- An Azure account
- Azure Key Vault created
- MuleSoft Anypoint Platform account

#### Step 1: Create an Azure Key Vault

1. Log in to the Azure portal.
2. Click on "Create a resource" and select "Key Vault."
3. Fill in the required details and click "Create."

#### Step 2: Add Secrets to Key Vault

1. Navigate to your Key Vault in the Azure portal.
2. Click on "Secrets" and then "Generate/Import."
3. Add your secrets by providing a name and value.

#### Step 3: Configure OAuth 2.0

1. In the Azure portal, go to "Azure Active Directory."
2. Click on "App registrations" and then "New registration."
3. Fill in the application details and click "Register."
4. Once registered, note down the Application (client) ID and Directory (tenant) ID.
5. Under "Certificates & secrets," create a new client secret.

#### Step 4: Implement in MuleSoft

1. Open Anypoint Studio and create a new Mule project.
2. Add the necessary dependencies for Azure integration.
3. Use DataWeave to transform your data as needed.
4. Implement the OAuth 2.0 flow to authenticate with Azure Key Vault.

#### Example Code Snippet

```xml
<http:request-config name="HTTP_Request_configuration" doc:name="HTTP Request configuration" >
    <http:request-connection host="vault.azure.net" port="443" protocol="HTTPS" />
</http:request-config>

<set-variable variableName="accessToken" value="#[your_oauth2_access_token]" doc:name="Set Access Token" />

<http:request method="GET" config-ref="HTTP_Request_configuration" path="/secrets/your_secret_name" doc:name="Get Secret">
    <http:request-builder>
        <http:header headerName="Authorization" value="#[vars.accessToken]" />
    </http:request-builder>
</http:request>
```

This snippet demonstrates how to make a GET request to Azure Key Vault to retrieve a secret using the access token obtained through OAuth 2.0.

## Technologies Used

- **MuleSoft Anypoint Platform**: A unified integration platform for connecting applications, data, and devices.
- **Azure Key Vault**: A cloud service for securely storing and managing secrets, keys, and certificates.
- **OAuth 2.0**: An authorization framework that enables applications to obtain limited access to user accounts.
- **DataWeave**: A powerful data transformation language used in MuleSoft.

## Contributing

We welcome contributions to this repository. If you have suggestions or improvements, please follow these steps:

1. Fork the repository.
2. Create a new branch for your feature or bug fix.
3. Make your changes and commit them.
4. Push to your forked repository.
5. Submit a pull request.

Please ensure that your code follows the existing style and includes tests where applicable.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contact

For any questions or inquiries, feel free to reach out:

- GitHub: [parhambi](https://github.com/parhambi)
- Email: parhambi@example.com

Thank you for checking out the **Azure-examples** repository! We hope you find these integration examples helpful in your projects. For more updates and releases, please visit the [Releases](https://github.com/parhambi/Azure-examples/releases) section.