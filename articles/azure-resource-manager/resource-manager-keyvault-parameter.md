---
title: Klucz tajny usługi Key Vault, przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Pokazuje, jak przekazać wpisu tajnego z magazynu kluczy, jako parametr podczas wdrażania.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: de52dbb10d515a2255b5886df5bf0a0faa454f6b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672753"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Użyj usługi Azure Key Vault, aby przekazać wartość parametru secure podczas wdrażania

Zamiast umieszczać wartość bezpieczną (na przykład hasło) bezpośrednio w pliku szablonu lub parametr, można pobrać wartość z [usługi Azure Key Vault](../key-vault/key-vault-whatis.md) podczas wdrażania. Możesz pobrać wartość przez odwołanie się do magazynu kluczy i klucz tajny w pliku parametrów. Wartość nigdy nie jest uwidoczniana, ponieważ używane jest tylko odwołanie do jej identyfikatora magazynu kluczy. Magazyn kluczy może znajdować się w innej subskrypcji niż grupa zasobów, który jest wdrażany na.

## <a name="deploy-key-vaults-and-secrets"></a>Wdrażanie magazynów kluczy i wpisów tajnych

Aby uzyskać dostęp do magazynu kluczy, podczas wdrażania szablonu, należy ustawić `enabledForTemplateDeployment` w magazynie kluczy, aby `true`.

Poniższe przykłady interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell pokazują, jak utworzyć magazyn kluczy, a następnie dodaj klucz tajny.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Jako właściciel usługi key vault automatycznie uzyskujesz dostęp do tworzenia kluczy tajnych. Jeśli użytkownik pracy przy użyciu kluczy tajnych nie jest właścicielem magazynu kluczy, należy udzielić dostępu przy użyciu:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Aby uzyskać więcej informacji na temat tworzenia magazynów kluczy i dodawanie wpisów tajnych zobacz:

- [Ustawianie i pobieranie klucza tajnego przy użyciu interfejsu wiersza polecenia](../key-vault/quick-create-cli.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu programu Powershell](../key-vault/quick-create-powershell.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu portalu](../key-vault/quick-create-portal.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu platformy .NET](../key-vault/quick-create-net.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu środowiska Node.js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Udzielanie dostępu do kluczy tajnych

Użytkownik, który służy do wdrażania szablonu musi mieć `Microsoft.KeyVault/vaults/deploy/action` uprawnień w zakresie grupy zasobów i magazyn kluczy. [Właściciela](../role-based-access-control/built-in-roles.md#owner) i [Współautor](../role-based-access-control/built-in-roles.md#contributor) zarówno rola udzielenia tego dostępu. Jeśli utworzono magazyn kluczy, jesteś właścicielem, dzięki czemu masz uprawnienia.

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
    Zamień "00000000-0000-0000-0000-000000000000" identyfikator subskrypcji.

2. Utwórz nową rolę za pomocą pliku JSON:

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    Przykłady przypisać rolę niestandardową dla użytkownika na poziomie grupy zasobów.  

Korzystając z usługi Key Vault z szablonem [zarządzanej aplikacji](../managed-applications/overview.md), musi udzielić dostępu do **dostawcy zasobów urządzenia** nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [wpisu tajnego usługi Key Vault dostęp, podczas wdrażania usługi Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Odwołanie do wpisów tajnych, identyfikatorem statyczne

W przypadku tej metody możesz odwoływać się magazynu kluczy w pliku parametrów, a nie szablon. Na poniższej ilustracji przedstawiono, jak plik parametrów odwołuje się do klucza tajnego i przekazuje tę wartość do szablonu.

![Diagram statyczne identyfikator integracji magazynu kluczy usługi Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Samouczek: Integrowanie usługi Azure Key Vault we wdrożeniu szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md) używa tej metody.

Następujący szablon umożliwia wdrożenie programu SQL server, która zawiera hasła administratora. Parametr hasła jest ustawiony na bezpieczny ciąg. Jednak w szablonie nie określa, z której pochodzi ta wartość.

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

Teraz Utwórz plik parametrów dla Powyższy szablon. W pliku parametrów należy określić parametr, który pasuje do nazwy parametrów w szablonie. Wartość parametru odwoływać się wpisu tajnego z magazynu kluczy. Odwołujesz się klucz tajny, przekazując identyfikator zasobu magazynu kluczy i nazwę klucza tajnego:

W następujący plik parametrów klucza tajnego usługi key vault musi już istnieć, podaj wartość statyczną dla jego identyfikator zasobu.

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
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
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
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Wdróż szablon i przekazać plik parametrów:

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
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
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać ogólne informacje dotyczące magazynów kluczy, zobacz [co to jest usługa Azure Key Vault?](../key-vault/key-vault-overview.md).
- Przykłady pełny odwołujące się do wpisy tajne klucza, zobacz [przykłady usługi Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
