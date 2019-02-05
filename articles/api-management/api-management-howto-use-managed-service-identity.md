---
title: Użyj usługi Azure Managed tożsamości usługi w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać tożsamości usługi zarządzanej platformy Azure w usłudze API Management
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
ms.openlocfilehash: 54c4d58dc881ffc7c1f5ecc2242b64e5b61fa68f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730751"
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Użyj tożsamości usług zarządzanych platformy Azure w usłudze Azure API Management

W tym artykule przedstawiono sposób tworzenia tożsamości usługi zarządzanej dla wystąpienia usługi API Management oraz jak uzyskiwać dostęp do innych zasobów. Tożsamość usługi zarządzanej, generowane przez usługę Azure Active Directory (Azure AD) umożliwia wystąpienia usługi API Management w łatwy i bezpieczny dostęp z innych zasobów platformy Azure chronionych przez usługi AD, takich jak usługi Azure Key Vault. Tej tożsamości usługi zarządzanej jest zarządzana przez platformę Azure i nie wymaga obsługi administracyjnej ani Obróć jakichkolwiek kluczy tajnych. Aby uzyskać więcej informacji o tożsamości usługi zarządzanej platformy Azure, zobacz [tożsamości usługi zarządzanej dla zasobów platformy Azure](../active-directory/msi-overview.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>Tworzenie tożsamości usługi zarządzanej dla wystąpienia usługi API Management

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamości usługi zarządzanej w portalu, możesz najpierw utworzyć wystąpienia usługi API Management, jak zwykle i włączysz tę funkcję.

1. Tworzenie wystąpienia usługi API Management w witrynie portal, tak jak zwykle. Przejdź do niego w portalu.
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
> Tożsamość usługi zarządzanej można obecnie uzyskać certyfikaty z usługi Azure Key Vault dla usługi API Management niestandardowych nazw domen. Wkrótce będą obsługiwane dalszych scenariuszach.
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

1. Tworzenie wystąpienia usługi API Management przy użyciu tożsamości usługi zarządzanej.
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
                "description": "Reference to the KeyVault certificate."
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

Dowiedz się więcej na temat tożsamości usługi zarządzanej platformy Azure:

* [Tożsamość usługi zarządzanej dla zasobów platformy Azure](../active-directory/msi-overview.md)
* [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
