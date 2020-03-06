---
title: Błędy rejestracji dostawcy zasobów
description: Opisuje sposób rozwiązywania problemów z rejestracją dostawcy zasobów platformy Azure podczas wdrażania zasobów przy użyciu Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390061"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Rozwiązywanie błędów dla rejestracji dostawcy zasobów

W tym artykule opisano błędy, które można napotkać podczas używania dostawcy zasobów, które wcześniej nie były używane w ramach subskrypcji.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

W przypadku wyłączenia automatycznego zamykania maszyn wirtualnych może zostać wyświetlony komunikat o błędzie podobny do:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Przyczyna

Te błędy są wyświetlane z jednego z następujących powodów:

* Wymagany dostawca zasobów nie został zarejestrowany dla Twojej subskrypcji
* Wersja interfejsu API, które nie są obsługiwane dla typu zasobu
* Lokalizacja nie jest obsługiwana dla typu zasobu
* W przypadku automatycznego zamykania maszyn wirtualnych dostawca zasobów Microsoft. wspólny musi być zarejestrowany.

## <a name="solution-1---powershell"></a>Rozwiązanie 1 — PowerShell

W przypadku programu PowerShell Użyj polecenia **Get-AzResourceProvider** , aby wyświetlić stan rejestracji.

```powershell
Get-AzResourceProvider -ListAvailable
```

Aby zarejestrować dostawcę, należy użyć **register-AzResourceProvider** i podać nazwę dostawcy zasobów, który ma zostać zarejestrowany.

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

Aby sprawdzić, czy dostawca jest zarejestrowany, użyj polecenia `az provider list`.

```azurecli-interactive
az provider list
```

Aby zarejestrować dostawcę zasobów, użyj polecenia `az provider register` i określ *obszar nazw* do zarejestrowania.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Aby wyświetlić obsługiwane lokalizacje i wersje interfejsów API dla typu zasobu, należy użyć:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 - w witrynie Azure portal

Można wyświetlić stan rejestracji i zarejestrować przestrzeni nazw dostawcy zasobów za pośrednictwem portalu.

1. W portalu wybierz pozycję **wszystkie usługi**.

   ![Wybierz wszystkie usługi](./media/error-register-resource-provider/select-all-services.png)

1. Wybierz pozycję **Subskrypcje**.

   ![Wybierz subskrypcje](./media/error-register-resource-provider/select-subscriptions.png)

1. Z listy subskrypcji Wybierz subskrypcję, której chcesz użyć do rejestrowania dostawcy zasobów.

   ![Wybierz subskrypcję, można zarejestrować dostawcy zasobów](./media/error-register-resource-provider/select-subscription-to-register.png)

1. W przypadku subskrypcji wybierz pozycję **dostawcy zasobów**.

   ![Wybierz dostawców zasobów](./media/error-register-resource-provider/select-resource-provider.png)

1. Zapoznaj się z listą dostawców zasobów i w razie potrzeby wybierz łącze **zarejestruj** , aby zarejestrować dostawcę zasobów typu, który próbujesz wdrożyć.

   ![Lista dostawców zasobów](./media/error-register-resource-provider/list-resource-providers.png)
