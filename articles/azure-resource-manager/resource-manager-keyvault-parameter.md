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
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: 3a29319a0d478537dfc4905ee77865b8fea64587
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598411"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Użyj usługi Azure Key Vault, aby przekazać wartość parametru secure podczas wdrażania

Gdy potrzebujesz przekazać wartość bezpieczną (na przykład hasło) jako parametr podczas wdrażania można pobrać wartości z [usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Możesz pobrać wartość przez odwołanie się do magazynu kluczy i klucz tajny w pliku parametrów. Wartość nigdy nie jest uwidaczniana, ponieważ możesz odwoływać się tylko do jego identyfikator magazynu kluczy. Magazyn kluczy może znajdować się w innej subskrypcji niż grupa zasobów, które są wdrażane do.

## <a name="enable-access-to-the-secret"></a>Zapewnianie dostępu do klucza tajnego

Istnieją dwie ważne warunki, które muszą istnieć w celu uzyskania dostępu do magazynu kluczy podczas wdrażania szablonu:

1. Właściwości magazynu kluczy `enabledForTemplateDeployment` musi być `true`.
2. Użytkownik, o których wdrożenie szablonu musi mieć dostęp do klucza tajnego. Użytkownik musi mieć `Microsoft.KeyVault/vaults/deploy/action` uprawnień dla usługi key vault. [Właściciela](../role-based-access-control/built-in-roles.md#owner) i [Współautor](../role-based-access-control/built-in-roles.md#contributor) zarówno rola udzielenia tego dostępu.

Korzystając z usługi Key Vault z szablonem [zarządzanej aplikacji](../managed-applications/overview.md), musi udzielić dostępu do **dostawcy zasobów urządzenia** nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [wpisu tajnego usługi Key Vault dostęp, podczas wdrażania usługi Azure Managed Applications](../managed-applications/key-vault-access.md).


## <a name="deploy-a-key-vault-and-secret"></a>Wdrażanie usługi key vault i klucz tajny

Aby utworzyć magazyn kluczy i klucz tajny, należy użyć wiersza polecenia platformy Azure lub programu PowerShell. Należy zauważyć, że magazyn kluczy jest włączone dla wdrożenia szablonu. 

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

W przypadku programu PowerShell użyj polecenia:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

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
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Odwołanie wpisu tajnego z Identyfikatorem dynamiczne

Poprzedniej sekcji pokazano, jak przekazać Identyfikatora zasobu statycznych dla wpisu tajnego usługi key vault. Jednak w niektórych przypadkach należy odwoływać się do usługi key vault klucz tajny, który różni się w oparciu o bieżące wdrożenie. W takim przypadku nie można trwale kodować identyfikator zasobu w pliku parametrów. Niestety, nie można dynamicznie wygenerować identyfikator zasobu w pliku parametrów, ponieważ szablon wyrażenia nie są dozwolone w pliku parametrów.

Można dynamicznie wygenerować identyfikator zasobu dla wpisu tajnego usługi key vault, możesz przenieść zasób, który wymaga klucza tajnego do dołączonego szablonu. Do szablonu nadrzędnego Dodaj połączony szablon i przekazać parametr, który zawiera identyfikator zasobu generowanych dynamicznie. Na poniższej ilustracji przedstawiono, jak parametr w połączony szablon odwołuje się do klucza tajnego.

![Dynamiczny identyfikator](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Połączone szablonu musi być dostępny za pośrednictwem zewnętrznego identyfikatora URI. Zwykle, Dodaj szablon do konta magazynu i uzyskać do niego dostęp za pomocą identyfikatora URI, takich jak `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

[Następujący szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) dynamicznie tworzy identyfikator magazynu kluczy i przekazuje je jako parametr. Łączy on [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) w usłudze GitHub.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Wdróż Powyższy szablon i podaj wartości parametrów. Można użyć szablonu przykładu z serwisu GitHub, ale należy podać wartości parametrów dla danego środowiska.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać ogólne informacje dotyczące magazynów kluczy, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](../key-vault/key-vault-get-started.md).
* Przykłady pełny odwołujące się do wpisy tajne klucza, zobacz [przykłady usługi Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
