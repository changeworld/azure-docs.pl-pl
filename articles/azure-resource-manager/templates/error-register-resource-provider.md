---
title: Błędy rejestracji dostawcy zasobów
description: W tym artykule opisano sposób rozwiązywania błędów rejestracji dostawcy zasobów platformy Azure podczas wdrażania zasobów w usłudze Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273776"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Usuwanie błędów związanych z rejestracją dostawcy zasobów

W tym artykule opisano błędy, które mogą wystąpić podczas korzystania z dostawcy zasobów, który nie był wcześniej używany w ramach subskrypcji.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu może pojawić się następujący kod błędu i komunikat:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Możesz też otrzymać podobny komunikat, który stwierdza:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Komunikat o błędzie powinien dać sugestie dotyczące obsługiwanych lokalizacji i wersji interfejsu API. Szablon można zmienić na jedną z sugerowanych wartości. Większość dostawców są rejestrowane automatycznie przez portal Azure lub interfejs wiersza polecenia, którego używasz, ale nie wszystkie. Jeśli nie używano określonego dostawcy zasobów przed, może być konieczne zarejestrowanie tego dostawcy.

Lub podczas wyłączania automatycznego zamykania maszyn wirtualnych może pojawić się komunikat o błędzie podobny do:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Przyczyna

Te błędy są następujące z jednego z następujących powodów:

* Dostawca wymaganych zasobów nie został zarejestrowany dla twojej subskrypcji
* Wersja interfejsu API nie jest obsługiwana dla typu zasobu
* Lokalizacja nie jest obsługiwana dla typu zasobu
* W przypadku automatycznego zamykania maszyn wirtualnych dostawca zasobów Microsoft.DevTestLab musi być zarejestrowany.

## <a name="solution-1---powershell"></a>Rozwiązanie 1 - PowerShell

W programie PowerShell użyj **programu Get-AzResourceProvider,** aby wyświetlić stan rejestracji.

```powershell
Get-AzResourceProvider -ListAvailable
```

Aby zarejestrować dostawcę, należy użyć **register-AzResourceProvider** i podać nazwę dostawcy zasobów, który chcesz zarejestrować.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Aby uzyskać obsługiwane lokalizacje dla określonego typu zasobu, należy użyć:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Aby uzyskać obsługiwane wersje interfejsu API dla określonego typu zasobu, należy użyć:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 — narzędzie interfejsu wiersza polecenia platformy Azure

Aby sprawdzić, czy dostawca jest `az provider list` zarejestrowany, użyj polecenia.

```azurecli-interactive
az provider list
```

Aby zarejestrować dostawcę `az provider register` zasobów, użyj polecenia i określ *obszar nazw* do zarejestrowania.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Aby wyświetlić obsługiwane lokalizacje i wersje interfejsu API dla typu zasobu, użyj:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 - Portal Platformy Azure

Możesz zobaczyć stan rejestracji i zarejestrować obszar nazw dostawcy zasobów za pośrednictwem portalu.

1. W portalu wybierz pozycję **Wszystkie usługi**.

   ![Wybierz wszystkie usługi](./media/error-register-resource-provider/select-all-services.png)

1. Wybierz pozycję **Subskrypcje**.

   ![Wybierz subskrypcje](./media/error-register-resource-provider/select-subscriptions.png)

1. Z listy subskrypcji wybierz subskrypcję, której chcesz użyć do zarejestrowania dostawcy zasobów.

   ![Wybierz subskrypcję, aby zarejestrować dostawcę zasobów](./media/error-register-resource-provider/select-subscription-to-register.png)

1. W przypadku subskrypcji wybierz pozycję **Dostawcy zasobów**.

   ![Wybieranie dostawców zasobów](./media/error-register-resource-provider/select-resource-provider.png)

1. Spójrz na listę dostawców zasobów i w razie potrzeby wybierz **łącze Zarejestruj,** aby zarejestrować dostawcę zasobów typu, który próbujesz wdrożyć.

   ![Lista dostawców zasobów](./media/error-register-resource-provider/list-resource-providers.png)
