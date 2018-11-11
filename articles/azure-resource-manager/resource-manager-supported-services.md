---
title: Dostawcy zasobów platformy Azure i ich typy zasobów | Dokumentacja firmy Microsoft
description: W tym artykule opisano dostawców zasobów, które obsługują usługi Resource Manager, ich schematów i dostępnych wersji interfejsu API i regiony, które mogą hostować zasoby.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: fafc16bdf00f947d4ba8ffe56d7cf2ae3e0bc489
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344947"
---
# <a name="resource-providers-and-types"></a>Dostawcy zasobów i ich typy

Podczas wdrażania zasobów, często jest potrzebne do pobierania informacji o dostawcy zasobów i typów. W tym artykule przedstawiono sposób:

* Wyświetlanie wszystkich dostawców zasobów na platformie Azure
* Sprawdzanie stanu rejestracji dostawcy zasobów
* Zarejestruj dostawcę zasobów
* Widok typów zasobów dla dostawcy zasobów
* Wyświetl prawidłowe lokalizacje dla typu zasobu
* Wyświetl prawidłowych wersji interfejsu API dla typu zasobu

Można wykonywać następujące czynności za pomocą witryny portal, programu PowerShell lub wiersza polecenia platformy Azure.

## <a name="powershell"></a>PowerShell

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stanu rejestracji dla Twojej subskrypcji, użyj:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Która zwróci wyniki podobne do:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Rejestrowanie dostawcy zasobów służy do konfigurowania subskrypcji do pracy za pomocą dostawcy zasobów. Zakres do rejestracji jest zawsze subskrypcji. Domyślnie są automatycznie rejestrowane wielu dostawców zasobów. Jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Aby zarejestrować dostawcę zasobów, musi mieć uprawnienia do wykonania `/register/action` operacji dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Która zwróci wyniki podobne do:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Nie można wyrejestrować dostawcy zasobów, jeśli nadal masz typy zasobów pochodzące od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje o dostawcy określonego zasobu, należy użyć:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Która zwróci wyniki podobne do:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Aby wyświetlić typy zasobów dla dostawcy zasobów, użyj:

```azurepowershell-interactive
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Zwraca:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są wydawane przez dostawcę zasobów. Jako dostawca zasobów zapewnia nowe funkcje, wydaje nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, należy użyć:

```azurepowershell-interactive
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Zwraca:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwane we wszystkich regionach, ale zasoby, które możesz wdrożyć mogą nie być obsługiwane we wszystkich regionach. Ponadto może być ograniczeń w ramach subskrypcji, które uniemożliwiają użycie w niektórych regionach, które obsługują zasobu.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, należy użyć.

```azurepowershell-interactive
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Zwraca:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stanu rejestracji dla Twojej subskrypcji, użyj:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Która zwróci wyniki podobne do:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Rejestrowanie dostawcy zasobów służy do konfigurowania subskrypcji do pracy za pomocą dostawcy zasobów. Zakres do rejestracji jest zawsze subskrypcji. Domyślnie są automatycznie rejestrowane wielu dostawców zasobów. Jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Aby zarejestrować dostawcę zasobów, musi mieć uprawnienia do wykonania `/register/action` operacji dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```azurecli
az provider register --namespace Microsoft.Batch
```

Zwraca komunikat tej rejestracji jest w toku.

Nie można wyrejestrować dostawcy zasobów, jeśli nadal masz typy zasobów pochodzące od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje o dostawcy określonego zasobu, należy użyć:

```azurecli
az provider show --namespace Microsoft.Batch
```

Która zwróci wyniki podobne do:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Aby wyświetlić typy zasobów dla dostawcy zasobów, użyj:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Zwraca:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są wydawane przez dostawcę zasobów. Jako dostawca zasobów zapewnia nowe funkcje, wydaje nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, należy użyć:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Zwraca:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwane we wszystkich regionach, ale zasoby, które możesz wdrożyć mogą nie być obsługiwane we wszystkich regionach. Ponadto może być ograniczeń w ramach subskrypcji, które uniemożliwiają użycie w niektórych regionach, które obsługują zasobu.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, należy użyć.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Zwraca:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>Portal

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stanu rejestracji dla Twojej subskrypcji, wybierz **subskrypcje**.

![Wybierz subskrypcje](./media/resource-manager-supported-services/select-subscriptions.png)

Wybierz subskrypcję, aby wyświetlić.

![Określ subskrypcję](./media/resource-manager-supported-services/subscription.png)

Wybierz **dostawców zasobów** i wyświetlić listę dostępnych dostawców zasobów.

![Pokaż dostawców zasobów](./media/resource-manager-supported-services/show-resource-providers.png)

Rejestrowanie dostawcy zasobów służy do konfigurowania subskrypcji do pracy za pomocą dostawcy zasobów. Zakres do rejestracji jest zawsze subskrypcji. Domyślnie są automatycznie rejestrowane wielu dostawców zasobów. Jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Aby zarejestrować dostawcę zasobów, musi mieć uprawnienia do wykonania `/register/action` operacji dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Aby zarejestrować dostawcę zasobów, wybierz **zarejestrować**.

![Procedura Rejestruj dostawcę zasobów](./media/resource-manager-supported-services/register-provider.png)

Nie można wyrejestrować dostawcy zasobów, jeśli nadal masz typy zasobów pochodzące od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje o dostawcy określonego zasobu, wybierz **wszystkich usług**.

![Wybierz wszystkie usługi](./media/resource-manager-supported-services/more-services.png)

Wyszukaj **Eksploratora zasobów** i wybierz ją z listy dostępnych opcji.

![Wybierz pozycję Eksplorator zasobów](./media/resource-manager-supported-services/select-resource-explorer.png)

Wybierz pozycję **Dostawcy**.

![Wybierz dostawców](./media/resource-manager-supported-services/select-providers.png)

Wybierz dostawcę zasobów i typu zasobu, który chcesz wyświetlić.

![Wybierz typ zasobu](./media/resource-manager-supported-services/select-resource-type.png)

Menedżer zasobów jest obsługiwane we wszystkich regionach, ale zasoby, które możesz wdrożyć mogą nie być obsługiwane we wszystkich regionach. Ponadto może być ograniczeń w ramach subskrypcji, które uniemożliwiają użycie w niektórych regionach, które obsługują zasobu. Eksplorator zasobów Wyświetla prawidłowe lokalizacje dla typu zasobu.

![Pokaż lokalizacje](./media/resource-manager-supported-services/show-locations.png)

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są wydawane przez dostawcę zasobów. Jako dostawca zasobów zapewnia nowe funkcje, wydaje nową wersję interfejsu API REST. Eksplorator zasobów przedstawia prawidłowych wersji interfejsu API dla typu zasobu.

![Pokaż wersje interfejsu API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby dowiedzieć się więcej na temat wdrażania zasobów, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [interfejsu API REST usługi Azure](/rest/api/).
