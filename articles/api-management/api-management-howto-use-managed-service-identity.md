---
title: Używanie zarządzanych tożsamości w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zarządzanych tożsamości w usłudze API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 75a02abb6cce332daad12e1feb25fb425f89f7f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393386"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Używanie zarządzanych tożsamości w usłudze Azure API Management

W tym artykule dowiesz się, jak utworzyć tożsamość zarządzana w przypadku wystąpienia usługi API Management i jak uzyskać dostęp do innych zasobów. Tożsamość zarządzana generowanych przez usługi Azure Active Directory (Azure AD) umożliwia wystąpienia usługi API Management w łatwy i bezpieczny dostęp z innych zasobów platformy Azure chronionych przez usługi AD, takich jak usługi Azure Key Vault. Ta tożsamość jest zarządzana przez platformę Azure i nie wymaga obsługi administracyjnej ani Obróć jakichkolwiek kluczy tajnych. Aby uzyskać więcej informacji o zarządzanych tożsamości, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Utwórz tożsamość zarządzaną dla wystąpienia usługi API Management

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, możesz najpierw utworzyć wystąpienia usługi API Management, jak zwykle i włączysz tę funkcję.

1. Tworzenie wystąpienia usługi API Management w witrynie portal, tak jak zwykle. Przejdź do niej w portalu.
2. Wybierz **tożsamości usługi zarządzanej**.
3. Zarejestruj w usłudze Azure Active Directory należy przełączyć się na. Kliknij pozycję Zapisz.

![Włączanie tożsamości usługi Zarządzanej](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Azure Resource Manager

Można utworzyć wystąpienia usługi API Management przy użyciu tożsamości, umieszczając następujące właściwości w definicji zasobu:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Oznacza to, Azure do tworzenia i zarządzania tożsamościami dla swojego wystąpienia usługi API Management.

Na przykład kompletny szablon usługi Azure Resource Manager może wyglądać następująco:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "contoso",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "Developer",
                "capacity": "1"
            },
            "properties": {
                "publisherEmail": "admin@contoso.com",
                "publisherName": "Contoso"
            },
            "identity": {
                "type": "systemAssigned"
            }
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Używanie tożsamości usługi zarządzanej dostępu do innych zasobów

> [!NOTE]
> Obecnie zarządzanych tożsamości mogą być używane do uzyskania certyfikatów z usługi Azure Key Vault dla usługi API Management niestandardowych nazw domen. Wkrótce będą obsługiwane dalszych scenariuszach.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Uzyskaj certyfikat z usługi Azure Key Vault

#### <a name="prerequisites"></a>Wymagania wstępne
1. Key Vault zawierające certyfikat pfx musi być w tej samej subskrypcji platformy Azure i tej samej grupie zasobów co usługa API Management. Jest to wymagane szablonu usługi Azure Resource Manager.
2. Typ zawartości klucz tajny musi być *application/x-pkcs12*. Poniższy skrypt służy do przekazywania certyfikatu:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Jeśli wersja obiektu certyfikatu nie zostanie podany, usługa API Management automatycznie uzyskać nowszą wersję certyfikatu przekazane do usługi Key Vault.

Poniższy przykład przedstawia szablon usługi Azure Resource Manager, który zawiera następujące czynności:

1. Tworzenie wystąpienia usługi API Management za pomocą tożsamości zarządzanej.
2. Aktualizowanie zasad dostępu do wystąpienia usługi Azure Key Vault i umożliwiają wystąpienia usługi API Management można uzyskać wpisy tajne z niego.
3. Przez ustawienie niestandardowej nazwy domeny za pomocą certyfikatu z wystąpienia usługi Key Vault, należy zaktualizować wystąpienia usługi API Management.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat zarządzanych tożsamości dla zasobów platformy Azure:

* [Co to jest zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
* [Uwierzytelnianie za pomocą tożsamości zarządzanej w zasadach](./api-management-authentication-policies.md#ManagedIdentity)
