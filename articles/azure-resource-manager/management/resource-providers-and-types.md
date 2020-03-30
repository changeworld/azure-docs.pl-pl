---
title: Dostawcy zasobów i typy zasobów
description: W tym artykule opisano dostawców zasobów, które obsługują Menedżera zasobów, ich schematy i dostępne wersje interfejsu API oraz regiony, które mogą obsługiwać zasoby.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273984"
---
# <a name="azure-resource-providers-and-types"></a>Dostawcy i typy zasobów platformy Azure

Podczas wdrażania zasobów często należy pobrać informacje o dostawców zasobów i typów. Na przykład w celu przechowywania kluczy i kluczy tajnych należy użyć dostawcy zasobów Microsoft.KeyVault. Ten dostawca zasobów udostępnia typ zasobu o nazwie magazyny umożliwiający utworzenie magazynu kluczy.

Nazwa typu zasobu ma następujący format: **{dostawca_zasobów}/{typ_zasobu}**. Typ zasobu dla magazynu kluczy to **Microsoft.KeyVault/magazyny**.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Wyświetlanie wszystkich dostawców zasobów na platformie Azure
* Sprawdzanie stanu rejestracji dostawcy zasobów
* Rejestrowanie dostawcy zasobów
* Wyświetlanie typów zasobów dla dostawcy zasobów
* Wyświetlanie prawidłowych lokalizacji dla typu zasobu
* Wyświetlanie prawidłowych wersji interfejsu API dla typu zasobu

Możesz wykonać te kroki za pomocą witryny Azure portal, usługi Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać listę map dostawców zasobów do usług platformy Azure, zobacz [Dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Portal Azure

Aby wyświetlić wszystkich dostawców zasobów i stan rejestracji subskrypcji:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.

    ![wybierz subskrypcje](./media/resource-providers-and-types/select-all-services.png)

3. W polu **Wszystkie usługi** wprowadź **pozycję Subskrypcja**, a następnie wybierz pozycję **Subskrypcje**.
4. Wybierz subskrypcję z listy subskrypcji do wyświetlenia.
5. Wybierz **dostawców zasobów** i wyświetl listę dostępnych dostawców zasobów.

    ![pokaż dostawców zasobów](./media/resource-providers-and-types/show-resource-providers.png)

6. Rejestrowanie dostawcy zasobów konfiguruje subskrypcję do pracy z dostawcą zasobów. Zakres rejestracji jest zawsze subskrypcja. Domyślnie wielu dostawców zasobów jest automatycznie zarejestrowanych. Jednak może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Aby zarejestrować dostawcę zasobów, musisz `/register/action` mieć uprawnienia do wykonania operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Aby zarejestrować dostawcę zasobów, wybierz pozycję **Zarejestruj**. Na poprzednim zrzucie ekranu wyróżnione łącze **Zarejestruj** dla programu **Microsoft.Blueprint**.

    Nie można wyrejestrować dostawcy zasobów, jeśli nadal masz typy zasobów od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące określonego dostawcy zasobów:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.
3. W polu **Wszystkie usługi** wprowadź **pozycję Eksplorator zasobów**, a następnie wybierz pozycję **Eksplorator zasobów**.

    ![wybierz wszystkie usługi](./media/resource-providers-and-types/select-resource-explorer.png)

4. Rozwiń **pozycję Dostawcy,** zaznaczając strzałkę w prawo.

    ![Wybierz dostawców](./media/resource-providers-and-types/select-providers.png)

5. Rozwiń dostawcę zasobów i typ zasobu, który chcesz wyświetlić.

    ![Wybierz typ zasobu](./media/resource-providers-and-types/select-resource-type.png)

6. Menedżer zasobów jest obsługiwany we wszystkich regionach, ale zasoby, które można wdrożyć, mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą istnieć ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób. Eksplorator zasobów wyświetla prawidłowe lokalizacje dla typu zasobu.

    ![Pokaż lokalizacje](./media/resource-providers-and-types/show-locations.png)

7. Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są zwalniane przez dostawcę zasobów. Jako dostawca zasobów włącza nowe funkcje, zwalnia nową wersję interfejsu API REST. Eksplorator zasobów wyświetla prawidłowe wersje interfejsu API dla typu zasobu.

    ![Pokaż wersje interfejsu API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stan rejestracji subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Który zwraca wyniki podobne do:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Rejestrowanie dostawcy zasobów konfiguruje subskrypcję do pracy z dostawcą zasobów. Zakres rejestracji jest zawsze subskrypcja. Domyślnie wielu dostawców zasobów jest automatycznie zarejestrowanych. Jednak może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Aby zarejestrować dostawcę zasobów, musisz `/register/action` mieć uprawnienia do wykonania operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Który zwraca wyniki podobne do:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Nie można wyrejestrować dostawcy zasobów, jeśli nadal masz typy zasobów od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące określonego dostawcy zasobów, należy użyć:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Który zwraca wyniki podobne do:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Aby wyświetlić typy zasobów dostawcy zasobów, należy użyć:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Który zwraca:

```output
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są zwalniane przez dostawcę zasobów. Jako dostawca zasobów włącza nowe funkcje, zwalnia nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, należy użyć:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Który zwraca:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwany we wszystkich regionach, ale zasoby, które można wdrożyć, mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą istnieć ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, użyj.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Który zwraca:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i stan rejestracji subskrypcji, użyj:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Który zwraca wyniki podobne do:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Rejestrowanie dostawcy zasobów konfiguruje subskrypcję do pracy z dostawcą zasobów. Zakres rejestracji jest zawsze subskrypcja. Domyślnie wielu dostawców zasobów jest automatycznie zarejestrowanych. Jednak może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Aby zarejestrować dostawcę zasobów, musisz `/register/action` mieć uprawnienia do wykonania operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```azurecli
az provider register --namespace Microsoft.Batch
```

Który zwraca komunikat, że rejestracja jest w toku.

Nie można wyrejestrować dostawcy zasobów, jeśli nadal masz typy zasobów od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące określonego dostawcy zasobów, należy użyć:

```azurecli
az provider show --namespace Microsoft.Batch
```

Który zwraca wyniki podobne do:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Aby wyświetlić typy zasobów dostawcy zasobów, należy użyć:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Który zwraca:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST, które są zwalniane przez dostawcę zasobów. Jako dostawca zasobów włącza nowe funkcje, zwalnia nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, należy użyć:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Który zwraca:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwany we wszystkich regionach, ale zasoby, które można wdrożyć, mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą istnieć ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, użyj.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Który zwraca:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu szablonów Usługi Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../templates/template-syntax.md). 
* Aby wyświetlić schematy szablonów dostawcy zasobów, zobacz [Odwołanie do szablonu](/azure/templates/).
* Aby uzyskać listę map dostawców zasobów do usług platformy Azure, zobacz [Dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [Interfejs API usługi Azure REST](/rest/api/).
