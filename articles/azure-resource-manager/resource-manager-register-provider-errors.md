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
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2f3db5e6260b065c83f0e337306d38dca6e5ff51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60389959"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Rozwiązywanie błędów dla rejestracji dostawcy zasobów

W tym artykule opisano błędy, które można napotkać podczas używania dostawcy zasobów, które wcześniej nie były używane w ramach subskrypcji.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

Lub, wyłączając automatyczne zamykanie dla maszyn wirtualnych może zostać wyświetlony komunikat o błędzie podobny do:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Przyczyna

Te błędy dla jednego z następujących powodów:

* Dostawca wymagany zasób nie został zarejestrowany dla Twojej subskrypcji
* Wersja interfejsu API, które nie są obsługiwane dla typu zasobu
* Lokalizacja nie jest obsługiwana dla typu zasobu
* Automatycznego zamykania maszyn wirtualnych należy zarejestrować dostawcę zasobów Microsoft.DevTestLab.

## <a name="solution-1---powershell"></a>Rozwiązanie 1 — PowerShell

W przypadku programu PowerShell, użyj **Get AzResourceProvider** wyświetlić Twój status rejestracji.

```powershell
Get-AzResourceProvider -ListAvailable
```

Aby zarejestrować dostawcę, należy użyć **AzResourceProvider rejestru** i podaj nazwę dostawcy zasobów, które chcesz zarejestrować.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Aby uzyskać obsługiwane lokalizacje dla określonego typu zasobów, należy użyć:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Aby uzyskać obsługiwanych wersji interfejsu API dla danego typu zasobu, należy użyć:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
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
