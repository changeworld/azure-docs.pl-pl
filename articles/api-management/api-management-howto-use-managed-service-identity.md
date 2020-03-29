---
title: Używanie tożsamości zarządzanych w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać tożsamości zarządzanych w usłudze API Management
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
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249635"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Używanie tożsamości zarządzanych w usłudze Azure API Management

W tym artykule pokazano, jak utworzyć tożsamość zarządzaną dla wystąpienia usługi zarządzania interfejsem API i jak uzyskać dostęp do innych zasobów. Tożsamość zarządzana generowana przez usługę Azure Active Directory (Azure AD) umożliwia wystąpieniu usługi API Management łatwy i bezpieczny dostęp do innych zasobów chronionych usługą Azure AD, takich jak usługa Azure Key Vault. Ta tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowidizacji ani obracania żadnych wpisów tajnych. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Tworzenie tożsamości zarządzanej dla wystąpienia usługi API Management

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, najpierw utworzysz wystąpienie usługi API Management w zwykły sposób, a następnie włączysz tę funkcję.

1. Utwórz wystąpienie zarządzania interfejsami API w portalu, tak jak zwykle. Przejdź do niej w portalu.
2. Wybierz **pozycję Tożsamości usługi zarządzanej**.
3. Przełącz rejestrację w usłudze Azure Active Directory na włącz. Kliknij pozycję Zapisz.

![Włączanie tożsamości usługi zarządzanej](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager

Można utworzyć wystąpienie usługi API Management z tożsamością, dołączając następującą właściwość do definicji zasobu:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

To nakazuje platformie Azure tworzenie tożsamości dla wystąpienia usługi API Management i zarządzanie nią.

Na przykład kompletny szablon usługi Azure Resource Manager może wyglądać następująco:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
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
    }]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Uzyskiwanie dostępu do innych zasobów za pomocą tożsamości usługi zarządzanej

> [!NOTE]
> Obecnie tożsamości zarządzane mogą służyć do uzyskiwania certyfikatów z usługi Azure Key Vault dla niestandardowych nazw domen usługi API Management. Wkrótce będzie obsługiwanych więcej scenariuszy.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Uzyskiwanie certyfikatu z usługi Azure Key Vault

#### <a name="prerequisites"></a>Wymagania wstępne
1. Magazyn kluczy zawierający certyfikat pfx musi znajdować się w tej samej subskrypcji platformy Azure i tej samej grupie zasobów co usługa zarządzania interfejsami API. Jest to wymaganie szablonu usługi Azure Resource Manager.
2. Typ zawartości klucza tajnego musi być *application/x-pkcs12*. Do przekazania certyfikatu można użyć następującego skryptu:

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
> Jeśli wersja obiektu certyfikatu nie zostanie dostarczona, usługa API Management automatycznie uzyska nowszą wersję certyfikatu po jego przekazaniu do usługi Key Vault.

W poniższym przykładzie pokazano szablon usługi Azure Resource Manager, który zawiera następujące kroki:

1. Utwórz wystąpienie zarządzania interfejsami API z tożsamością zarządzaną.
2. Zaktualizuj zasady dostępu wystąpienia usługi Azure Key Vault i pozwól wystąpieniu usługi API Management uzyskać od niego wpisy tajne.
3. Zaktualizuj wystąpienie usługi API Management, ustawiając niestandardową nazwę domeny za pomocą certyfikatu z wystąpienia Usługi Usługi Key Vault.

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure:

* [Co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
* [Uwierzytelnij się przy użyciu tożsamości zarządzanej w zasadach](./api-management-authentication-policies.md#ManagedIdentity)
