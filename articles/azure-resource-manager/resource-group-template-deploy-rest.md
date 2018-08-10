---
title: Wdrażanie zasobów przy użyciu interfejsu API REST i szablonu | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i interfejsu REST API usługi Resource Manager do wdrażania zasobów platformy Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: ae2393d16d2c9c1000b00f5514e63c988303a83c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628515"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)

W tym artykule wyjaśniono, jak używać interfejsu REST API usługi Resource Manager przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure.  

> [!TIP]
> Aby uzyskać pomoc w debugowaniu wystąpił błąd podczas wdrażania zobacz:
> 
> * [Wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md) Aby dowiedzieć się więcej o pobieraniu informacje ułatwiające rozwiązywanie problemów z błędu
> * [Rozwiązywanie typowych problemów podczas wdrażania zasobów na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md) dowiesz się, jak rozwiązywać typowe błędy związane z wdrażaniem
> 
> 

Szablon może być w lokalnym pliku lub pliku zewnętrznego, który jest dostępny za pośrednictwem identyfikatora URI. Gdy szablon znajduje się na koncie magazynu, można ograniczyć dostęp do szablonu, a także dostarczają token sygnatury (SAS) dostępu współdzielonego podczas wdrażania.

## <a name="deploy-with-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST
1. Ustaw [wspólnych parametrów i nagłówki](/rest/api/azure/), łącznie z tokenami uwierzytelniania.

2. Jeśli nie masz istniejącej grupy zasobów, Utwórz grupę zasobów. Prześlij identyfikator swojej subskrypcji, nazwę nowej grupy zasobów i lokalizacji, która należy do rozwiązania. Aby uzyskać więcej informacji, zobacz [Utwórz grupę zasobów](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. Sprawdź poprawność wdrożenia przed wykonaniem ją, uruchamiając [zweryfikować wdrożenie szablonu](/rest/api/resources/deployments/validate) operacji. Podczas testowania wdrożenia, należy podać parametry dokładnie tak jak podczas wykonywania wdrożenia (pokazano w następnym kroku).

4. Tworzenie wdrożenia. Podaj identyfikator subskrypcji, nazwę grupy zasobów, nazwę wdrożenia i łącza do szablonu. Aby uzyskać informacje o pliku szablonu, zobacz [plik parametrów](#parameter-file). Aby uzyskać więcej informacji na temat interfejsu API REST, aby utworzyć grupę zasobów, zobacz [Utwórz wdrożenie szablonu](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Zwróć uwagę **tryb** ustawiono **przyrostowe**. Aby uruchomić całego procesu wdrażania, należy ustawić **tryb** do **Complete**. Należy zachować ostrożność podczas korzystania z trybu pełne, jak można przypadkowo usunąć zasoby, które nie są w szablonie.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
      }
    }
  }
  ```

    Do logowania się zawartość odpowiedzi i/lub zawartości żądania, należy dołączyć **debugSetting** w żądaniu.

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    Możesz skonfigurować swoje konto magazynu do użycia token dostępu współdzielonego (SAS) podpisu. Aby uzyskać więcej informacji, zobacz [Delegowanie dostępu za pomocą podpisu dostępu współdzielonego](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

5. Pobierz stan wdrożenia szablonu. Aby uzyskać więcej informacji, zobacz [uzyskać informacje na temat wdrożenia szablonu](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie, gdy wdrożenie zakończy się niepowodzeniem

W przypadku wdrożeń, które się nie powieść można określić, że wcześniejsze wdrożenie z historii wdrożenia jest automatycznie ponownie wdrożyć. Aby użyć tej opcji, wdrożeń muszą mieć unikatowe nazwy, dzięki czemu można je zidentyfikować w historii. Jeśli nie masz unikatowe nazwy bieżącego wdrożenia nie powiodło się może spowodować zastąpienie wcześniej pomyślnego wdrożenia w historii. Tej opcji można używać tylko w przypadku wdrożeń poziomu głównego. Wdrożenia z szablonów zagnieżdżonych nie są dostępne dla ponownego wdrażania.

Aby przeprowadzić ponowne wdrożenie ostatniego pomyślnego wdrożenia, jeśli bieżące wdrożenie zakończy się niepowodzeniem, należy użyć:

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

Aby przeprowadzić ponowne wdrożenie określonego wdrożenia, jeśli bieżące wdrożenie zakończy się niepowodzeniem, należy użyć:

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
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
* Aby określić sposób obsługi zasób, który istnieje w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
* Aby dowiedzieć się więcej informacji na temat obsługi operacji REST asynchronicznych, zobacz [śledzenie operacji asynchronicznych Azure](resource-manager-async-operations.md).
* Aby uzyskać przykład wdrażanie zasobów za pomocą biblioteki klienckiej platformy .NET, zobacz [wdrażanie zasobów przy użyciu szablonu i bibliotek .NET](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby zdefiniować parametry w szablonie, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md#parameters).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

