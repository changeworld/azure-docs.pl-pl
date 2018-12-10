---
title: Błędy rejestracji dostawcy zasobów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozwiązać błędy rejestracji dostawcy zasobów platformy Azure.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/07/2018
ms.author: tomfitz
ms.openlocfilehash: 787d6549eb8413c9dcfc0c906167cc36d4cff6b0
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135716"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Rozwiązywanie błędów dla rejestracji dostawcy zasobów

W tym artykule opisano błędy, które można napotkać podczas używania dostawcy zasobów, które wcześniej nie były używane w ramach subskrypcji.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobów, może pojawić się następujący kod błędu i komunikatem:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Lub może zostać wyświetlony podobny komunikat, stwierdzający, że:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Komunikat o błędzie powinien zapewnić sugestie dotyczące obsługiwane lokalizacje i wersje interfejsów API. Możesz zmienić szablon do jednego z sugerowanych wartości. Większość dostawców są zarejestrowane automatycznie w witrynie Azure portal lub interfejsu wiersza polecenia, którego używasz, ale nie wszystkich. Jeśli nie znasz dostawcy określonego zasobu, zanim może być konieczne zarejestrować tego dostawcę.

## <a name="cause"></a>Przyczyna

Te błędy są jednego z trzech powodów:

1. Dostawca zasobów nie został zarejestrowany dla Twojej subskrypcji
1. Wersja interfejsu API, które nie są obsługiwane dla typu zasobu
1. Lokalizacja nie jest obsługiwana dla typu zasobu

## <a name="solution-1---powershell"></a>Rozwiązanie 1 — PowerShell

W przypadku programu PowerShell, użyj **Get-AzureRmResourceProvider** wyświetlić Twój status rejestracji.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Aby zarejestrować dostawcę, należy użyć **Register-AzureRmResourceProvider** i podaj nazwę dostawcy zasobów, które chcesz zarejestrować.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Aby uzyskać obsługiwane lokalizacje dla określonego typu zasobów, należy użyć:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Aby uzyskać obsługiwanych wersji interfejsu API dla danego typu zasobu, należy użyć:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 — interfejs wiersza polecenia Azure

Aby zobaczyć, czy dostawca jest zarejestrowany, użyj `az provider list` polecenia.

```azurecli-interactive
az provider list
```

Aby zarejestrować dostawcę zasobów, użyj `az provider register` polecenia, a następnie określ *przestrzeni nazw* do zarejestrowania.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Aby wyświetlić obsługiwane lokalizacje i wersje interfejsów API dla typu zasobu, należy użyć:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 - w witrynie Azure portal

Można wyświetlić stan rejestracji i zarejestrować przestrzeni nazw dostawcy zasobów za pośrednictwem portalu.

1. W portalu, wybierz **wszystkich usług**.

   ![Wybierz wszystkie usługi](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Wybierz pozycję **Subskrypcje**.

   ![Wybierz subskrypcje](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Z listy subskrypcji Wybierz subskrypcję, której chcesz użyć do rejestrowania dostawcy zasobów.

   ![Wybierz subskrypcję, można zarejestrować dostawcy zasobów](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Dla Twojej subskrypcji, wybierz **dostawców zasobów**.

   ![Wybierz dostawców zasobów](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Przyjrzyj się liście dostawców zasobów, a w razie potrzeby zaznacz **zarejestrować** link, aby zarejestrować dostawcę zasobów typu, który próbujesz wdrożyć.

   ![Lista dostawców zasobów](./media/resource-manager-register-provider-errors/list-resource-providers.png)
