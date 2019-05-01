---
title: Dostawcy zasobów platformy Azure i ich typy zasobów | Dokumentacja firmy Microsoft
description: W tym artykule opisano dostawców zasobów, które obsługują usługi Resource Manager, ich schematów i dostępnych wersji interfejsu API i regiony, które mogą hostować zasoby.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: a3c6eca548eb61d6b7b239b4292b9c77ca2dec6f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702703"
---
# <a name="azure-resource-providers-and-types"></a>Dostawcy zasobów platformy Azure i ich typy

Podczas wdrażania zasobów, często jest potrzebne do pobierania informacji o dostawcy zasobów i typów. W tym artykule omówiono sposób wykonywania następujących zadań:

* Wyświetlanie wszystkich dostawców zasobów na platformie Azure
* Sprawdzanie stanu rejestracji dostawcy zasobów
* Zarejestruj dostawcę zasobów
* Widok typów zasobów dla dostawcy zasobów
* Wyświetl prawidłowe lokalizacje dla typu zasobu
* Wyświetl prawidłowych wersji interfejsu API dla typu zasobu

Można wykonać następujące kroki, za pośrednictwem witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure.

Aby uzyskać listę, która mapuje dostawcy zasobów usługi platformy Azure, zobacz [dostawcy zasobów usługi platformy Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Azure Portal

Aby wyświetlić wszystkich dostawców zasobów, a stan rejestracji subskrypcji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi**.

    ![Wybierz subskrypcje](./media/resource-manager-supported-services/select-subscriptions.png)
3. W **wszystkich usług** wprowadź **subskrypcji**, a następnie wybierz pozycję **subskrypcje**.
4. Wybierz subskrypcję z listy subskrypcję, aby wyświetlić.
5. Wybierz **dostawców zasobów** i wyświetlić listę dostępnych dostawców zasobów.

    ![Pokaż dostawców zasobów](./media/resource-manager-supported-services/show-resource-providers.png)

6. Rejestrowanie dostawcy zasobów służy do konfigurowania subskrypcji do pracy za pomocą dostawcy zasobów. Zakres do rejestracji jest zawsze subskrypcji. Domyślnie są automatycznie rejestrowane wielu dostawców zasobów. Jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Aby zarejestrować dostawcę zasobów, musi mieć uprawnienia do wykonania `/register/action` operacji dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Aby zarejestrować dostawcę zasobów, wybierz **zarejestrować**. Na poprzednim zrzucie ekranu **zarejestrować** łącza zostanie wyróżniona do **Microsoft.Blueprint**.

    Nie można wyrejestrować dostawcy zasobów, jeśli nadal masz typy zasobów pochodzące od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje o dostawcy określonego zasobu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi**.

    ![Wybierz wszystkie usługi](./media/resource-manager-supported-services/more-services.png)

3. W **wszystkich usług** wprowadź **Eksploratora zasobów**, a następnie wybierz pozycję **Eksploratora zasobów**.
4. Rozwiń **dostawców** , wybierając strzałkę w prawo.

    ![Wybierz dostawców](./media/resource-manager-supported-services/select-providers.png)

5. Rozwiń węzeł dostawcy zasobów i typ zasobu, który chcesz wyświetlić.

    ![Wybierz typ zasobu](./media/resource-manager-supported-services/select-resource-type.png)

6. Menedżer zasobów jest obsługiwane we wszystkich regionach, ale zasoby, które możesz wdrożyć mogą nie być obsługiwane we wszystkich regionach. Ponadto może być ograniczeń w ramach subskrypcji, które uniemożliwiają użycie w niektórych regionach, które obsługują zasobu. Eksplorator zasobów Wyświetla prawidłowe lokalizacje dla typu zasobu.

    ![Pokaż lokalizacje](./media/resource-manager-supported-services/show-locations.png)

7. Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są wydawane przez dostawcę zasobów. Jako dostawca zasobów zapewnia nowe funkcje, wydaje nową wersję interfejsu API REST. Eksplorator zasobów przedstawia prawidłowych wersji interfejsu API dla typu zasobu.

    ![Pokaż wersje interfejsu API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stanu rejestracji dla Twojej subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
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
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
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
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
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
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
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
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
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
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
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

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md). 
* Aby wyświetlić schematy szablonów dostawcy zasobów, zobacz [odwołanie do szablonu](/azure/templates/).
* Aby uzyskać listę, która mapuje dostawcy zasobów usługi platformy Azure, zobacz [dostawcy zasobów usługi platformy Azure](azure-services-resource-providers.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [interfejsu API REST usługi Azure](/rest/api/).
