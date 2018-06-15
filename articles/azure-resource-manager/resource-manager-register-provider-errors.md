---
title: Błędy rejestracji dostawcy zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób rozwiązania błędy rejestracji dostawcy zasobów platformy Azure.
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
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: b90009c1cd08a1004e58c4b9f25cd6350712fbcd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358612"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Rozwiązywanie błędów dla rejestracji dostawcy zasobów

W tym artykule opisano błędy, które można napotkać, korzystając z dostawcy zasobów, które nie były wcześniej używane w ramach subskrypcji.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu, może zostać wyświetlony następujący kod błędu i komunikat:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Lub może zostać wyświetlony komunikat podobny:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Komunikat o błędzie powinien zapewnić sugestie dotyczące obsługiwanych lokalizacji i wersje interfejsu API. Szablon można zmienić na jedną z sugerowane wartości. Większość dostawców są rejestrowane automatycznie w portalu Azure lub interfejsu wiersza polecenia, którego używasz, ale nie wszystkich. Nie używano dostawcę określonego zasobu przed, konieczne może zarejestrować tego dostawcy.

## <a name="cause"></a>Przyczyna

Te błędy dla jednego z trzech powodów:

1. Nie zarejestrowano dostawcę zasobów dla Twojej subskrypcji
1. Wersja interfejsu API nie jest obsługiwana dla typu zasobu
1. Lokalizacja nie jest obsługiwana dla typu zasobu

## <a name="solution-1---powershell"></a>Rozwiązanie 1 - programu PowerShell

Dla programu PowerShell, użyj **Get AzureRmResourceProvider** aby zobaczyć stan rejestracji.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Aby zarejestrować dostawcę, należy użyć **AzureRmResourceProvider rejestru** i podaj nazwę dostawcy zasobów, które chcesz zarejestrować.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Aby uzyskać obsługiwane lokalizacje dla określonego typu zasobu, należy użyć:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Aby uzyskać obsługiwanej wersji interfejsu API dla określonego typu zasobu, należy użyć:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 - Azure CLI

Aby sprawdzić, czy dostawca został zarejestrowany, użyj `az provider list` polecenia.

```azurecli-interactive
az provider list
```

Aby zarejestrować dostawcy zasobów, użyj `az provider register` polecenia, a następnie określ *przestrzeni nazw* do zarejestrowania.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Aby wyświetlić obsługiwane lokalizacje i wersje interfejsu API dla typu zasobu, należy użyć:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 - w portalu Azure

Możesz wyświetlić stan rejestracji i zarejestrować przestrzeń nazw dostawcy zasobów za pośrednictwem portalu.

1. Dla Twojej subskrypcji, wybierz **dostawców zasobów**.

   ![Wybierz dostawców zasobów](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Sprawdzić listę dostawców zasobów, a w razie potrzeby wybierz **zarejestrować** łącze, aby zarejestrować dostawcę zasobów typu, który chcesz wdrożyć.

   ![Lista dostawców zasobów](./media/resource-manager-register-provider-errors/list-resource-providers.png)
