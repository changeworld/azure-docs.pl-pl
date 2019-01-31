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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 6e9ad6f74970b6c72b96ae142f02bee6b07fb558
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455477"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Użyj usługi Azure Key Vault, aby przekazać wartość parametru secure podczas wdrażania

Zamiast umieszczać wartość bezpieczną (na przykład hasło) bezpośrednio w pliku parametrów, można pobrać wartość z [usługi Azure Key Vault](../key-vault/key-vault-whatis.md) podczas wdrażania. Możesz pobrać wartość przez odwołanie się do magazynu kluczy i klucz tajny w pliku parametrów. Wartość nigdy nie jest uwidaczniana, ponieważ możesz odwoływać się tylko do jego identyfikator magazynu kluczy. Magazyn kluczy może znajdować się w innej subskrypcji niż grupa zasobów, które są wdrażane do.

Aby wykonać kroki samouczka, zobacz [samouczka: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md#prepare-a-key-vault).

## <a name="deploy-key-vaults-and-secrets"></a>Wdrażanie magazynów kluczy i wpisów tajnych

Aby utworzyć magazynów kluczy i dodać wpisy tajne, zobacz:

- [Ustawianie i pobieranie klucza tajnego przy użyciu interfejsu wiersza polecenia](../key-vault/quick-create-cli.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu programu Powershell](../key-vault/quick-create-powershell.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu portalu](../key-vault/quick-create-portal.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu platformy .NET](../key-vault/quick-create-net.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu środowiska Node.js](../key-vault/quick-create-node.md)

Istnieją pewne dodatkowe zagadnienia i wymagania dotyczące integrowania usługi Key Vault z wdrożenia szablonu usługi Resource Manager:

- `enabledForTemplateDeployment` jest to właściwość magazynu kluczy. Dostęp do kluczy tajnych wewnątrz tej usługi Key Vault z wdrożenia usługi Resource Manager `enabledForTemplateDeployment` musi być `true`. 
- Jeśli nie jesteś właścicielem magazynu kluczy, właściciel należy zaktualizować ustawienia zasad zabezpieczeń magazynu kluczy można dodać wpisów tajnych.

Poniższe przykłady interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell pokazują, jak to zrobić:

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>Udzielanie dostępu do kluczy tajnych

Użytkownik, który służy do wdrażania szablonu musi mieć `Microsoft.KeyVault/vaults/deploy/action` uprawnień dla zakresu, który zawiera usługi Key Vault, łącznie z grupy zasobów i usługi Key Vault. [Właściciela](../role-based-access-control/built-in-roles.md#owner) i [Współautor](../role-based-access-control/built-in-roles.md#contributor) zarówno rola udzielenia tego dostępu. Jeśli utworzysz usługę Key Vault, jesteś właścicielem, dzięki czemu masz uprawnienia. W przypadku usługi Key Vault w ramach różnych subskrypcji, właściciel usługi Key Vault musi grand dostępu.

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
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzRoleAssignment` Przykład umożliwia przypisanie roli niestandardowej dla użytkownika na poziomie grupy zasobów.  

Korzystając z usługi Key Vault z szablonem [zarządzanej aplikacji](../managed-applications/overview.md), musi udzielić dostępu do **dostawcy zasobów urządzenia** nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [wpisu tajnego usługi Key Vault dostęp, podczas wdrażania usługi Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Odwołanie do wpisów tajnych, identyfikatorem statyczne

W przypadku tej metody możesz odwoływać się magazynu kluczy w pliku parametrów, a nie szablon. Na poniższej ilustracji przedstawiono, jak plik parametrów odwołuje się do klucza tajnego i przekazuje tę wartość do szablonu.

![Diagram statyczne identyfikator integracji magazynu kluczy usługi Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Samouczek: Integrowanie usługi Azure Key Vault we wdrożeniu szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md) używa tej metody. Samouczek wdrożyć maszynę wirtualną, co zawiera hasła administratora. Parametr hasła jest ustawiony na bezpieczny ciąg:

![Menedżer zasobów usługi key vault integracji identyfikator szablonu pliku statycznego](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

Teraz Utwórz plik parametrów dla Powyższy szablon. W pliku parametrów należy określić parametr, który pasuje do nazwy parametrów w szablonie. Wartość parametru odwoływać się wpisu tajnego z magazynu kluczy. Odwołujesz się klucz tajny, przekazując identyfikator zasobu magazynu kluczy i nazwę klucza tajnego:

![Menedżer zasobów usługi key vault integracji statyczne identyfikator pliku parametrów](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

Jeśli musisz użyć wersji klucza tajnego innej niż bieżąca wersja, należy użyć `secretVersion` właściwości.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Wdróż szablon i przekazać plik parametrów:

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Odwołanie do wpisów tajnych, identyfikatorem dynamiczne

Poprzedniej sekcji pokazano, jak przekazać Identyfikatora zasobu statycznych dla wpisu tajnego usługi key vault z parametru. Jednak w niektórych przypadkach należy odwoływać się do usługi key vault klucz tajny, który różni się w oparciu o bieżące wdrożenie. Ewentualnie możesz podać wartości parametrów w szablonie, zamiast tworzyć parametr przekazany przez odwołanie w pliku parametrów. W obu przypadkach można dynamicznie generować identyfikator zasobu dla wpisu tajnego usługi key vault za pomocą dołączonego szablonu.

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

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólne informacje dotyczące magazynów kluczy, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](../key-vault/key-vault-get-started.md).
- Przykłady pełny odwołujące się do wpisy tajne klucza, zobacz [przykłady usługi Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
