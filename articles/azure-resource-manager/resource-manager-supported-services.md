---
title: Dostawcy zasobów platformy Azure i typy zasobów | Dokumentacja firmy Microsoft
description: W tym artykule opisano dostawców zasobów obsługujących usługi Resource Manager, ich schematów i dostępne wersje interfejsu API i regionów, które można udostępniać zasoby.
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
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 811bb40816339dbe7097e429722625a3ae5c95c0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358204"
---
# <a name="resource-providers-and-types"></a>Dostawcy zasobów i typów

Podczas wdrażania zasobów, często konieczne pobranie informacji o dostawcy zasobów i typów. W tym artykule dowiesz się:

* Wyświetlanie wszystkich dostawców zasobów na platformie Azure
* Sprawdzaj stan rejestracji dostawcy zasobów
* Rejestrowanie dostawcy zasobów
* Wyświetlanie typów zasobów dla dostawcy zasobów
* Prawidłowe lokalizacje widoku dla typu zasobu
* Widok prawidłowych wersji interfejsu API dla typu zasobu

Można wykonywać następujące czynności, za pośrednictwem portalu, programu PowerShell lub wiersza polecenia platformy Azure.

## <a name="powershell"></a>PowerShell

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stan rejestracji subskrypcji, należy użyć:

```powershell
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

Rejestrowanie dostawcy zasobów służy do konfigurowania subskrypcji do pracy z dostawcy zasobów. Subskrypcja jest zawsze zakres rejestracji. Domyślnie automatycznie zarejestrowano wielu dostawców zasobów. Jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Aby zarejestrować dostawcy zasobów, musi mieć uprawnienia do wykonywania `/register/action` operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Która zwróci wyniki podobne do:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Nie można wyrejestrować dostawcy zasobów, gdy nadal masz typów zasobów z tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące dostawcy określonego zasobu, należy użyć:

```powershell
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

Aby wyświetlić typy zasobów dla dostawcy zasobów, należy użyć:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Polecenie to zwraca:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są wydawane przez dostawcę zasobów. Jak dostawca zasobów umożliwia korzystanie z nowych funkcji, udostępnia nową wersję interfejsu API REST. 

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, należy użyć:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Polecenie to zwraca:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwane we wszystkich regionach, ale zasoby, które można wdrożyć mogą nie być obsługiwane we wszystkich regionach. Ponadto może być mają zastosowanie ograniczenia dotyczące subskrypcji, które uniemożliwią używanie niektórych regionów, które obsługują zasobu. 

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, należy użyć.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Polecenie to zwraca:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stan rejestracji subskrypcji, należy użyć:

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

Rejestrowanie dostawcy zasobów służy do konfigurowania subskrypcji do pracy z dostawcy zasobów. Subskrypcja jest zawsze zakres rejestracji. Domyślnie automatycznie zarejestrowano wielu dostawców zasobów. Jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Aby zarejestrować dostawcy zasobów, musi mieć uprawnienia do wykonywania `/register/action` operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```azurecli
az provider register --namespace Microsoft.Batch
```

Która zwraca komunikat do tej rejestracji jest w toku.

Nie można wyrejestrować dostawcy zasobów, gdy nadal masz typów zasobów z tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące dostawcy określonego zasobu, należy użyć:

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

Aby wyświetlić typy zasobów dla dostawcy zasobów, należy użyć:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Polecenie to zwraca:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są wydawane przez dostawcę zasobów. Jak dostawca zasobów umożliwia korzystanie z nowych funkcji, udostępnia nową wersję interfejsu API REST. 

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, należy użyć:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Polecenie to zwraca:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwane we wszystkich regionach, ale zasoby, które można wdrożyć mogą nie być obsługiwane we wszystkich regionach. Ponadto może być mają zastosowanie ograniczenia dotyczące subskrypcji, które uniemożliwią używanie niektórych regionów, które obsługują zasobu. 

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, należy użyć.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Polecenie to zwraca:

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

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stan rejestracji subskrypcji, wybierz **subskrypcje**.

![Wybierz subskrypcje](./media/resource-manager-supported-services/select-subscriptions.png)

Wybierz subskrypcję, aby wyświetlić.

![Określ subskrypcję](./media/resource-manager-supported-services/subscription.png)

Wybierz **dostawców zasobów** i wyświetlić listę dostępnych dostawców zasobów.

![Pokaż dostawców zasobów](./media/resource-manager-supported-services/show-resource-providers.png)

Rejestrowanie dostawcy zasobów służy do konfigurowania subskrypcji do pracy z dostawcy zasobów. Subskrypcja jest zawsze zakres rejestracji. Domyślnie automatycznie zarejestrowano wielu dostawców zasobów. Jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Aby zarejestrować dostawcy zasobów, musi mieć uprawnienia do wykonywania `/register/action` operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Aby zarejestrować dostawcy zasobów, wybierz **zarejestrować**.

![Rejestrowanie dostawcy zasobów](./media/resource-manager-supported-services/register-provider.png)

Nie można wyrejestrować dostawcy zasobów, gdy nadal masz typów zasobów z tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące dostawcy określonego zasobu, wybierz **wszystkie usługi**.

![Wybierz wszystkie usługi](./media/resource-manager-supported-services/more-services.png)

Wyszukaj **Eksploratora zasobów** i wybierz ją z dostępnych opcji.

![Wybierz Eksploratora zasobów](./media/resource-manager-supported-services/select-resource-explorer.png)

Wybierz pozycję **Dostawcy**.

![Wybierz dostawców](./media/resource-manager-supported-services/select-providers.png)

Wybierz dostawcę zasobów i typ zasobu, który chcesz wyświetlić.

![Wybierz typ zasobu](./media/resource-manager-supported-services/select-resource-type.png)

Menedżer zasobów jest obsługiwane we wszystkich regionach, ale zasoby, które można wdrożyć mogą nie być obsługiwane we wszystkich regionach. Ponadto może być mają zastosowanie ograniczenia dotyczące subskrypcji, które uniemożliwią używanie niektórych regionów, które obsługują zasobu. Eksplorator zasobów Wyświetla prawidłowe lokalizacje dla typu zasobu.

![Pokaż lokalizacje](./media/resource-manager-supported-services/show-locations.png)

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są wydawane przez dostawcę zasobów. Jak dostawca zasobów umożliwia korzystanie z nowych funkcji, udostępnia nową wersję interfejsu API REST. Eksplorator zasobów wyświetla prawidłowych wersji interfejsu API dla typu zasobu.

![Pokaż wersje interfejsu API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacje dotyczące tworzenia szablonów usługi Resource Manager, zobacz [szablonów Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Aby dowiedzieć się więcej na temat wdrażania zasobów, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).
* Aby wyświetlić operacje dla dostawców zasobów, zobacz [interfejsu API REST Azure](/rest/api/).

