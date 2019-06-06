---
title: Wdrażanie zasobów przy użyciu interfejsu API REST i szablonu | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i interfejsu REST API usługi Resource Manager do wdrażania zasobów platformy Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 0490cf6837cb413bc2e869424cd430fd4a824dc9
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688873"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)

W tym artykule wyjaśniono, jak używać interfejsu REST API usługi Resource Manager przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure.  

Można dołączyć do szablonu w treści żądania lub link do pliku. Podczas korzystania z pliku, można go w lokalnym pliku lub pliku zewnętrznego, który jest dostępny za pośrednictwem identyfikatora URI. Gdy szablon będzie na koncie magazynu, można ograniczyć dostęp do szablonu, a także dostarczają token sygnatury (SAS) dostępu współdzielonego podczas wdrażania.

## <a name="deployment-scope"></a>Zakres wdrożenia

Można wskazać wdrożenia do grupy zarządzania, subskrypcji platformy Azure lub grupy zasobów. W większości przypadków można będzie zarządzać wdrożeniami docelowymi w grupie zasobów. Użyj wdrożenia zarządzania grupy lub subskrypcji, do stosowania zasad i przypisań ról dla określonego zakresu. Subskrypcja wdrożeń możesz także użyć do tworzenia grupy zasobów i wdrażania zasobów. W zależności od zakresu wdrożenia możesz używać różnych poleceń.

Aby wdrożyć **grupy zasobów**, użyj [tworzenie wdrożenia -](/rest/api/resources/deployments/createorupdate). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby wdrożyć **subskrypcji**, użyj [wdrożenia — Utwórz w zakres subskrypcji](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby wdrożyć **grupy zarządzania**, użyj [wdrożenia — tworzenie w zakresie grupy zarządzania](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

W przykładach w tym artykule używany wdrożenia grupy zasobów. Aby uzyskać więcej informacji o wdrożeniach subskrypcji, zobacz [tworzenia grupy zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

1. Ustaw [wspólnych parametrów i nagłówki](/rest/api/azure/), łącznie z tokenami uwierzytelniania.

1. Jeśli nie masz istniejącej grupy zasobów, Utwórz grupę zasobów. Prześlij identyfikator swojej subskrypcji, nazwę nowej grupy zasobów i lokalizacji, która należy do rozwiązania. Aby uzyskać więcej informacji, zobacz [Utwórz grupę zasobów](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Z treści żądania, takie jak:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Sprawdź poprawność wdrożenia przed wykonaniem ją, uruchamiając [zweryfikować wdrożenie szablonu](/rest/api/resources/deployments/validate) operacji. Podczas testowania wdrożenia, należy podać parametry dokładnie tak jak podczas wykonywania wdrożenia (pokazano w następnym kroku).

1. Aby wdrożyć szablon, Prześlij identyfikator swojej subskrypcji, nazwę grupy zasobów, nazwę wdrożenia w identyfikatorze URI żądania. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   W treści żądania Podaj link do pliku szablonu oraz parametrów. Zwróć uwagę **tryb** ustawiono **przyrostowe**. Aby uruchomić całego procesu wdrażania, należy ustawić **tryb** do **Complete**. Należy zachować ostrożność podczas korzystania z trybu pełne, jak można przypadkowo usunąć zasoby, które są w szablonie.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Do logowania się zawartość odpowiedzi i/lub zawartości żądania, należy dołączyć **debugSetting** w żądaniu.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Możesz skonfigurować swoje konto magazynu do użycia token dostępu współdzielonego (SAS) podpisu. Aby uzyskać więcej informacji, zobacz [Delegowanie dostępu za pomocą podpisu dostępu współdzielonego](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Zamiast łączenia z plików szablonu oraz parametrów, należy je dołączyć w treści żądania. Treść żądania za pomocą wbudowanego szablonu oraz parametrów można znaleźć w poniższym przykładzie:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Aby wyświetlić stan wdrożenia szablonu, należy użyć [wdrożenia — Pobierz](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie, gdy wdrożenie zakończy się niepowodzeniem

Ta funkcja jest nazywana *wycofywania w przypadku błędu*. Jeśli wdrożenie zakończy się niepowodzeniem, można automatycznie wdrożyć ponownie wcześniej pomyślnego wdrażania z historii wdrożenia. Aby określić ponowne wdrożenie, użyj `onErrorDeployment` właściwość w treści żądania. Ta funkcja jest przydatna, jeśli coś znanego, dobrego stanu dla danego wdrożenia infrastruktury, a chcesz przywrócić ten stan. Kilka ograniczeń i ostrzeżenia:

- Ponownego wdrożenia komputera jest uruchamiany, dokładnie tak, jak została poprzednio uruchomiona z tymi samymi parametrami. Nie można zmienić parametry.
- Poprzedniego wdrożenia zostanie uruchomiony z użyciem [w trybie](./deployment-modes.md#complete-mode). Zostaną usunięte wszystkie zasoby, które nie są uwzględnione w poprzednim wdrożeniu, a wszystkie konfiguracje zasobów są ustawione do poprzedniego stanu. Upewnij się, w pełni rozumiesz [tryby wdrażania](./deployment-modes.md).
- Ponownego wdrożenia komputera ma wpływ tylko na zasoby, nie ma wpływu na wszelkie zmiany danych.
- Ta funkcja jest obsługiwana tylko we wdrożeniach grupy zasobów, nie wdrożeń poziomu subskrypcji. Aby uzyskać więcej informacji na temat wdrażania poziomu subskrypcji zobacz [tworzenia grupy zasobów i zasobów na poziomie subskrypcji](./deploy-to-subscription.md).

Aby użyć tej opcji, wdrożeń muszą mieć unikatowe nazwy, dzięki czemu można je zidentyfikować w historii. Jeśli nie masz unikatowe nazwy bieżącego wdrożenia nie powiodło się może spowodować zastąpienie wcześniej pomyślnego wdrożenia w historii. Tej opcji można używać tylko w przypadku wdrożeń poziomu głównego. Wdrożenia z szablonów zagnieżdżonych nie są dostępne dla ponownego wdrażania.

Aby przeprowadzić ponowne wdrożenie ostatniego pomyślnego wdrożenia, jeśli bieżące wdrożenie zakończy się niepowodzeniem, należy użyć:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Aby przeprowadzić ponowne wdrożenie określonego wdrożenia, jeśli bieżące wdrożenie zakończy się niepowodzeniem, należy użyć:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Zakończyły się powodzeniem określonego wdrożenia.

## <a name="parameter-file"></a>Plik parametrów

Jeśli używasz pliku parametrów do przekazywania wartości parametrów podczas wdrażania należy utworzyć plik w formacie JSON za pomocą formatu podobny do poniższego przykładu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               },
               "secretName": "sqlAdminPassword"
            }
        }
   }
}
```

Rozmiar pliku parametrów nie może być więcej niż 64 KB.

Jeśli trzeba podać poufne wartość parametru (na przykład hasło), należy dodać tę wartość do magazynu kluczy. Pobierz magazyn kluczy podczas wdrażania, jak pokazano w poprzednim przykładzie. Aby uzyskać więcej informacji, zobacz [przekazywanie bezpiecznych wartości podczas wdrażania](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Kolejne kroki

- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
- Aby dowiedzieć się więcej informacji na temat obsługi operacji REST asynchronicznych, zobacz [śledzenie operacji asynchronicznych Azure](resource-manager-async-operations.md).
- Aby dowiedzieć się więcej na temat szablonów, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).

