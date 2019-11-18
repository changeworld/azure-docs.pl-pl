---
title: Key Vault klucz tajny z szablonem
description: Pokazuje, jak przekazać klucz tajny z magazynu kluczy jako parametr podczas wdrażania.
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 37d21e295eca2b40e91f92d65d6e927ee6857d0e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149494"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Użyj Azure Key Vault, aby przekazać bezpieczną wartość parametru podczas wdrażania

Zamiast umieszczać bezpieczną wartość (na przykład hasło) bezpośrednio w szablonie lub pliku parametrów, można pobrać wartość z [Azure Key Vault](../key-vault/key-vault-overview.md) podczas wdrażania. Możesz pobrać wartość, odwołując się do magazynu kluczy i wpisu tajnego w pliku parametrów. Wartość nigdy nie jest uwidoczniana, ponieważ używane jest tylko odwołanie do jej identyfikatora magazynu kluczy. Magazyn kluczy może istnieć w innej subskrypcji niż grupa zasobów, w której jest wdrażana.

## <a name="deploy-key-vaults-and-secrets"></a>Wdrażanie magazynów kluczy i wpisów tajnych

Aby uzyskać dostęp do magazynu kluczy podczas wdrażania szablonu, należy ustawić `enabledForTemplateDeployment` w magazynie kluczy, aby `true`.

Następujące przykłady interfejsu wiersza polecenia platformy Azure i Azure PowerShell pokazują, jak utworzyć magazyn kluczy i dodać wpis tajny.

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

Jako właściciel magazynu kluczy automatycznie masz dostęp do tworzenia wpisów tajnych. Jeśli użytkownik pracujący z wpisami tajnymi nie jest właścicielem magazynu kluczy, Udziel dostępu:

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

Aby uzyskać więcej informacji na temat tworzenia magazynów kluczy i dodawania wpisów tajnych, zobacz:

- [Ustawianie i pobieranie wpisu tajnego za pomocą interfejsu wiersza polecenia](../key-vault/quick-create-cli.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu programu PowerShell](../key-vault/quick-create-powershell.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu portalu](../key-vault/quick-create-portal.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu platformy .NET](../key-vault/quick-create-net.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu środowiska Node. js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Przyznaj dostęp do wpisów tajnych

Użytkownik, który wdraża szablon, musi mieć uprawnienie `Microsoft.KeyVault/vaults/deploy/action` dla zakresu grupy zasobów i magazynu kluczy. Role [właściciela](../role-based-access-control/built-in-roles.md#owner) i [współautora](../role-based-access-control/built-in-roles.md#contributor) przyznają ten dostęp. Jeśli magazyn kluczy został utworzony, jesteś właścicielem i masz uprawnienia.

Poniższa procedura pokazuje, jak utworzyć rolę z minimalnym uprawnieniem i jak przypisać użytkownika

1. Utwórz niestandardowy plik JSON definicji roli:

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
    Zastąp ciąg "00000000-0000-0000-0000-000000000000" IDENTYFIKATORem subskrypcji.

2. Utwórz nową rolę przy użyciu pliku JSON:

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

    Przykłady umożliwiają przypisanie roli niestandardowej do użytkownika na poziomie grupy zasobów.  

Korzystając z Key Vault z szablonem [aplikacji zarządzanej](../managed-applications/overview.md), należy przyznać dostęp do jednostki usługi **dostawcy zasobów urządzenia** . Aby uzyskać więcej informacji, zobacz [Access Key Vault Secret podczas wdrażania Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Wpisy tajne z IDENTYFIKATORem statycznym

To podejście polega na odwoływaniu się do magazynu kluczy w pliku parametrów, a nie w szablonie. Na poniższej ilustracji przedstawiono sposób, w jaki plik parametrów odwołuje się do klucza tajnego i przekazuje tę wartość do szablonu.

![Diagram identyfikatorów statycznych Menedżer zasobów integracji magazynu kluczy](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Samouczek: integracja Azure Key Vault w Menedżer zasobów Template Deployment](./resource-manager-tutorial-use-key-vault.md) używa tej metody.

Poniższy szablon wdraża program SQL Server zawierający hasło administratora. Parametr password jest ustawiony na bezpieczny ciąg. Jednak szablon nie określa, skąd pochodzi ta wartość.

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

Teraz Utwórz plik parametrów dla poprzedniego szablonu. W pliku parametrów określ parametr, który jest zgodny z nazwą parametru w szablonie. Dla wartości parametru należy odwołać się do wpisu tajnego z magazynu kluczy. Aby odwołać się do wpisu tajnego, należy przekazać identyfikator zasobu magazynu kluczy i nazwę klucza tajnego:

W poniższym pliku parametrów wpis tajny magazynu kluczy musi już istnieć i podano wartość statyczną dla tego identyfikatora zasobu.

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

Jeśli musisz użyć wersji klucza tajnego innego niż bieżąca wersja, użyj właściwości `secretVersion`.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Wdróż szablon i przekaż go do pliku parametrów:

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

## <a name="reference-secrets-with-dynamic-id"></a>Wpisy tajne z IDENTYFIKATORem dynamicznym

W poprzedniej sekcji pokazano, jak przekazać statyczny identyfikator zasobu dla wpisu tajnego magazynu kluczy z parametru. Jednak w niektórych scenariuszach należy odwołać się do wpisu tajnego magazynu kluczy, który zależy od bieżącego wdrożenia. Można również przekazać wartości parametrów do szablonu zamiast tworzyć parametr odwołania w pliku parametrów. W obu przypadkach można dynamicznie generować identyfikator zasobu dla wpisu tajnego magazynu kluczy przy użyciu połączonego szablonu.

Nie można dynamicznie wygenerować identyfikatora zasobu w pliku parametrów, ponieważ wyrażenia szablonów nie są dozwolone w pliku parametrów. 

W szablonie nadrzędnym Dodaj połączony szablon i Przekaż parametr zawierający dynamicznie wygenerowany identyfikator zasobu. Na poniższej ilustracji przedstawiono sposób, w jaki parametr w połączonym szablonie odwołuje się do klucza tajnego.

![Identyfikator dynamiczny](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[Następujący szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dynamicznie tworzy identyfikator magazynu kluczy i przekazuje go jako parametr.

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

Wdróż poprzedni szablon i podaj wartości parametrów. Możesz użyć przykładowego szablonu z usługi GitHub, ale musisz podać wartości parametrów dla danego środowiska.

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

- Aby uzyskać ogólne informacje na temat magazynów kluczy, zobacz [co to jest Azure Key Vault?](../key-vault/key-vault-overview.md).
- Aby uzyskać pełne Przykłady odwoływania się do kluczy tajnych, zobacz [Key Vault przykładów](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
