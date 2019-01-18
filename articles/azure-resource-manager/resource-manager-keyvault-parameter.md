---
title: Klucz tajny usługi Key Vault, przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Pokazuje, jak przekazać wpisu tajnego z magazynu kluczy, jako parametr podczas wdrażania.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: e04ee6b74be0bb7ec81166f43295a8a9377f8cf8
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381522"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Użyj usługi Azure Key Vault, aby przekazać wartość parametru secure podczas wdrażania

Gdy potrzebujesz przekazać wartość bezpieczną (na przykład hasło) jako parametr podczas wdrażania można pobrać wartości z [usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Możesz pobrać wartość przez odwołanie się do magazynu kluczy i klucz tajny w pliku parametrów. Wartość nigdy nie jest uwidaczniana, ponieważ możesz odwoływać się tylko do jego identyfikator magazynu kluczy. Magazyn kluczy może znajdować się w innej subskrypcji niż grupa zasobów, które są wdrażane do.

## <a name="deploy-a-key-vault-and-secret"></a>Wdrażanie usługi key vault i klucz tajny

Aby utworzyć magazyn kluczy i klucz tajny, należy użyć wiersza polecenia platformy Azure lub programu PowerShell. `enabledForTemplateDeployment` jest to właściwość magazynu kluczy. Dostęp do kluczy tajnych wewnątrz tej usługi Key Vault z wdrożenia usługi Resource Manager `enabledForTemplateDeployment` musi być `true`. 

Poniższy skrypt programu Azure PowerShell i wiersza polecenia platformy Azure przykład przedstawia sposób tworzenia magazynu kluczy i klucz tajny.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

Jeśli uruchomienie skryptu programu PowerShell, poza usłudze Cloud Shell, użyj następującego polecenia, można zamiast niego wygenerować hasła:

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

Aby uzyskać przy użyciu szablonu usługi Resource Manager: Zobacz [samouczka: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> Każda usługa platformy Azure ma określone wymagania dotyczące hasła. Na przykład wymagania dotyczące maszyny wirtualnej platformy Azure znajduje się w temacie [jakie są wymagania dotyczące hasła, podczas tworzenia maszyny Wirtualnej?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="enable-access-to-the-secret"></a>Zapewnianie dostępu do klucza tajnego

Inne niż ustawienie `enabledForTemplateDeployment` do `true`, użytkownik wdrożenie szablonu musi mieć `Microsoft.KeyVault/vaults/deploy/action` uprawnień dla zakresu, który zawiera usługi Key Vault, łącznie z grupy zasobów i usługi Key Vault. [Właściciela](../role-based-access-control/built-in-roles.md#owner) i [Współautor](../role-based-access-control/built-in-roles.md#contributor) zarówno rola udzielenia tego dostępu. Jeśli utworzysz usługę Key Vault, jesteś właścicielem, dzięki czemu masz uprawnienia. W przypadku usługi Key Vault w ramach różnych subskrypcji, właściciel usługi Key Vault musi udzielić dostępu.

Poniższa procedura pokazuje, jak utworzyć rolę za pomocą minimalne uprawnienia i przypisywania użytkownika
1. Utwórz plik JSON definicji roli niestandardowej:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Zastąp identyfikator subskrypcji użytkownika, który wymaga, aby wdrożyć szablony "00000000-0000-0000-0000-000000000000".

2. Utwórz nową rolę za pomocą pliku JSON:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzureRmRoleAssignment` Przykładowe przypisać rolę niestandardową dla użytkownika na poziomie grupy zasobów.  

Korzystając z usługi Key Vault z szablonem [zarządzanej aplikacji](../managed-applications/overview.md), musi udzielić dostępu do **dostawcy zasobów urządzenia** nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [wpisu tajnego usługi Key Vault dostęp, podczas wdrażania usługi Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-a-secret-with-static-id"></a>Odwołanie wpisu tajnego z Identyfikatorem statyczne

Szablon, który odbiera wpisu tajnego usługi key vault jest jak każdego innego szablonu. To dlatego, że **możesz odwoływać się do magazynu kluczy w pliku parametrów, a nie szablon.** Na poniższej ilustracji przedstawiono, jak plik parametrów odwołuje się do klucza tajnego i przekazuje tę wartość do szablonu.

![Identyfikator statyczne](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Na przykład [następujący szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) wdraża bazę danych SQL, który zawiera hasła administratora. Parametr hasła jest ustawiony na bezpieczny ciąg. Jednak szablonu nie określa, z której pochodzi ta wartość.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Teraz Utwórz plik parametrów dla Powyższy szablon. W pliku parametrów należy określić parametr, który pasuje do nazwy parametrów w szablonie. Wartość parametru odwoływać się wpisu tajnego z magazynu kluczy. Możesz odwoływać się klucz tajny, przekazując identyfikator zasobu magazynu kluczy i nazwę klucza tajnego. W [następującego pliku parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json)wpisu tajnego usługi key vault musi już istnieć i podaj wartość statyczną dla jego identyfikator zasobu. Skopiuj ten plik lokalnie, a następnie ustaw identyfikator subskrypcji, nazwę magazynu i nazwa serwera SQL.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Jeśli musisz użyć wersji klucza tajnego innej niż bieżąca wersja, należy użyć `secretVersion` właściwości.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Teraz Wdróż szablon i przekazać plik parametrów. Można użyć szablonu przykładu z serwisu GitHub, ale musisz użyć pliku parametrów lokalnych przy użyciu wartości ustawione dla danego środowiska.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

W przypadku programu PowerShell użyj polecenia:

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Odwołanie wpisu tajnego z Identyfikatorem dynamiczne

Poprzedniej sekcji pokazano, jak przekazać Identyfikatora zasobu statycznych dla wpisu tajnego usługi key vault z parametru. Jednak w niektórych przypadkach należy odwoływać się do usługi key vault klucz tajny, który różni się w oparciu o bieżące wdrożenie. Lub możesz po prostu podać wartości parametrów do szablonu zamiast tworzyć parametr przekazany przez odwołanie w pliku parametrów. W obu przypadkach można dynamicznie generować identyfikator zasobu dla wpisu tajnego usługi key vault za pomocą dołączonego szablonu.

Nie można dynamicznie wygenerować identyfikator zasobu w pliku parametrów, ponieważ szablon wyrażenia nie są dozwolone w pliku parametrów. 

Do szablonu nadrzędnego Dodaj połączony szablon i przekazać parametr, który zawiera identyfikator zasobu generowanych dynamicznie. Na poniższej ilustracji przedstawiono, jak parametr w połączony szablon odwołuje się do klucza tajnego.

![Dynamiczny identyfikator](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[Następujący szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dynamicznie tworzy identyfikator magazynu kluczy i przekazuje je jako parametr.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Wdróż Powyższy szablon i podaj wartości parametrów. Można użyć szablonu przykładu z serwisu GitHub, ale należy podać wartości parametrów dla danego środowiska.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać ogólne informacje dotyczące magazynów kluczy, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](../key-vault/key-vault-get-started.md).
* Przykłady pełny odwołujące się do wpisy tajne klucza, zobacz [przykłady usługi Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).