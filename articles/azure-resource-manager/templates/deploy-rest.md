---
title: Wdrażanie zasobów za pomocą interfejsu API i szablonu REST
description: Użyj usługi Azure Resource Manager i interfejsu API REST usługi Resource Manager, aby wdrożyć zasoby na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153237"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Menedżera zasobów

W tym artykule wyjaśniono, jak używać interfejsu API REST usługi Resource Manager za pomocą szablonów usługi Azure Resource Manager (ARM) w celu wdrożenia zasobów na platformie Azure.

Szablon można dołączyć w treści żądania lub łącze do pliku. Podczas korzystania z pliku może to być plik lokalny lub plik zewnętrzny, który jest dostępny za pośrednictwem identyfikatora URI. Gdy szablon znajduje się na koncie magazynu, można ograniczyć dostęp do szablonu i podać token podpisu dostępu udostępnionego (SAS) podczas wdrażania.

## <a name="deployment-scope"></a>Zakres wdrożenia

Można kierować swoje wdrożenie do grupy zarządzania, subskrypcji platformy Azure lub grupy zasobów. W większości przypadków będziesz kierować wdrożenia do grupy zasobów. Użyj wdrożeń grupy zarządzania lub subskrypcji, aby zastosować zasady i przypisania ról w określonym zakresie. Można również użyć wdrożeń subskrypcji, aby utworzyć grupę zasobów i wdrożyć zasoby do niego. W zależności od zakresu wdrożenia należy użyć różnych poleceń.

Aby wdrożyć w **grupie zasobów,** użyj [wdrożenia - Tworzenie](/rest/api/resources/deployments/createorupdate). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby wdrożyć w **ramach subskrypcji,** należy użyć [opcji Wdrożenia — Tworzenie w zakresie subskrypcji](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

Aby wdrożyć w **grupie zarządzania,** należy użyć [opcji Wdrożenia — Tworzenie w zakresie grupy zarządzania](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

Przykłady w tym artykule używają wdrożeń grup zasobów.

## <a name="deploy-with-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

1. Ustaw [typowe parametry i nagłówki](/rest/api/azure/), w tym tokeny uwierzytelniania.

1. Jeśli nie masz istniejącej grupy zasobów, utwórz grupę zasobów. Podaj identyfikator subskrypcji, nazwę nowej grupy zasobów i lokalizację potrzebną do rozwiązania. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Z treścią żądania, taką jak:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Sprawdź poprawność wdrożenia przed jego wykonaniem, uruchamiając operację [sprawdzania poprawności szablonu.](/rest/api/resources/deployments/validate) Podczas testowania wdrożenia należy podać parametry dokładnie tak, jak podczas wykonywania wdrożenia (pokazane w następnym kroku).

1. Aby wdrożyć szablon, podaj identyfikator subskrypcji, nazwę grupy zasobów, nazwę wdrożenia w identyfikatorze żądania URI.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   W treści żądania podaj łącze do pliku szablonu i parametru. Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżera zasobów](parameter-files.md).

   Zwróć uwagę, że **tryb** jest ustawiony na **Przyrostowy**. Aby uruchomić pełne wdrożenie, ustaw **tryb** na **Zakończ**. Należy zachować ostrożność podczas korzystania z trybu pełnego, ponieważ można przypadkowo usunąć zasoby, które nie są w szablonie.

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

    Jeśli chcesz zarejestrować zawartość odpowiedzi, zażądać zawartości lub obu, należy uwzględnić **debugSetting** w żądaniu.

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

    Konto magazynu można skonfigurować tak, aby używało tokenu sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji, zobacz [Delegowanie dostępu za pomocą sygnatury dostępu udostępnionego](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Jeśli musisz podać wartość poufnych dla parametru (na przykład hasło), dodaj tę wartość do magazynu kluczy. Pobierz magazyn kluczy podczas wdrażania, jak pokazano w poprzednim przykładzie. Aby uzyskać więcej informacji, zobacz [Przekazywanie bezpiecznych wartości podczas wdrażania](key-vault-parameter.md).

1. Zamiast łączyć się z plikami szablonu i parametrów, można je uwzględnić w treści żądania. W poniższym przykładzie przedstawiono treść żądania z szablonem i parametrem wbudowanym:

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
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
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

1. Aby uzyskać stan wdrożenia szablonu, użyj [wdrożenia - Pobierz](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Następne kroki

- Aby przywrócić pomyślne wdrożenie po wyświetleniu błędu, zobacz [Wycofywanie w przypadku błędu do pomyślnego wdrożenia](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
- Aby dowiedzieć się więcej na temat obsługi asynchronicznych operacji REST, zobacz [Śledzenie asynchronicznych operacji platformy Azure.](../management/async-operations.md)
- Aby dowiedzieć się więcej o szablonach, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).

