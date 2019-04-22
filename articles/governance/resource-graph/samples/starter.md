---
title: Przykłady zapytań podstawowych
description: Przy użyciu usługi Azure Resource Graph uruchom kilka zapytań dla początkujących, obejmujących zliczanie i porządkowanie zasobów lub grupowanie według określonego tagu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ba48e2a21bdee0c5698bdfa314dd3bf462c1c7e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267773"
---
# <a name="starter-resource-graph-queries"></a>Zapytania usługi Resource Graph dla początkujących

Pierwszym krokiem do zrozumienia zapytań usługi Azure Resource Graph jest podstawowa wiedza na temat [języka zapytań](../concepts/query-language.md). Jeśli nie znasz jeszcze usługi [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), zalecane jest zapoznanie się z podstawami, aby zrozumieć sposób tworzenia żądań dotyczących zasobów, których szukasz.

Omówimy następujące podstawowe zapytania:

> [!div class="checklist"]
> - [Liczba zasobów platformy Azure](#count-resources)
> - [Lista zasobów posortowana według nazwy](#list-resources)
> - [Pokaż wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej](#show-vms)
> - [Pokaż pierwsze pięć maszyn wirtualnych według nazwy i ich typu systemu operacyjnego](#show-sorted)
> - [Liczba maszyn wirtualnych według typu systemu operacyjnego](#count-os)
> - [Pokaż zasoby, które zawierają magazyn](#show-storage)
> - [Lista wszystkich publicznych adresów IP](#list-publicip)
> - [Liczba zasobów ze skonfigurowanymi adresami IP według subskrypcji](#count-resources-by-ip)
> - [Lista zasobów z konkretną wartością tagu](#list-tag)
> - [Lista wszystkich kont magazynu z konkretną wartością tagu](#list-specific-tag)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Obsługa języków

Interfejs wiersza polecenia platformy Azure (za pośrednictwem rozszerzenia) i program Azure PowerShell (za pośrednictwem modułu) obsługują usługę Azure Resource Graph. Przed uruchomieniem dowolnego z poniższych zapytań sprawdź, czy Twoje środowisko jest gotowe. Zobacz [Interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Program Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module), gdzie znajdziesz kroki instalacji i weryfikacji wybranego środowiska powłoki.

## <a name="count-resources"></a>Liczba zasobów platformy Azure

To zapytanie zwraca liczbę zasobów platformy Azure, które istnieją w subskrypcjach, do których masz dostęp. Jest to również dobre zapytanie do weryfikowania, czy wybrana powłoka ma zainstalowane odpowiednie składniki usługi Azure Resource Graph w kolejności pracy.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Lista zasobów posortowana według nazwy

To zapytanie zwraca dowolny typ zasobu, ale tylko właściwości **nazwa**, **typ** i **lokalizacja**. Używa ono polecenia `order by` do sortowania właściwości według właściwości **nazwa** w kolejności rosnącej (`asc`).

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Pokaż wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej

Aby wyświetlić listę zawierającą tylko maszyny wirtualne (które są typu `Microsoft.Compute/virtualMachines`), możemy dopasować w wynikach właściwość **typ**. Podobnie jak w przypadku poprzedniego zapytania, element `desc` zmienia `order by` na kolejność malejącą. `=~` w dopasowaniu typu nakazuje usłudze Resource Graph ignorowanie wielkości liter.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>Pokaż pierwsze pięć maszyn wirtualnych według nazwy i ich typu systemu operacyjnego

To zapytanie będzie używać elementu `limit`, aby pobrać tylko pięć pasujących rekordów, uporządkowanych według nazwy. Typ zasobu platformy Azure to `Microsoft.Compute/virtualMachines`. `project` informuje usługę Azure Resource Graph, które właściwości mają być uwzględnione.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>Liczba maszyn wirtualnych według typu systemu operacyjnego

Opierając się na poprzednim zapytaniu, nadal ograniczamy wyniki według zasobów platformy Azure typu `Microsoft.Compute/virtualMachines`, ale nie ograniczamy już liczby zwracanych rekordów.
Zamiast tego użyliśmy elementów `summarize` i `count()`, aby określić sposób grupowania i agregacji wartości według właściwości, którą w tym przykładzie jest `properties.storageProfile.osDisk.osType`. Aby zobaczyć przykład przedstawiający wygląd tego ciągu w pełnym obiekcie, zobacz [badanie zasobów — odnajdywanie maszyn wirtualnych](../concepts/explore-resources.md#virtual-machine-discovery).

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Innym sposobem zapisania tego samego zapytania jest rozszerzenie (`extend`) właściwości i nadanie jej nazwy tymczasowej do użycia w ramach zapytania, w tym przypadku **os**. Właściwość **os** jest następnie używana przez funkcje `summarize` i `count()`, jak w poprzednim przykładzie.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Pamiętaj, że o ile `=~` zezwala na dopasowanie bez uwzględniania wielkości liter, to użycie właściwości (takich jak **properties.storageProfile.osDisk.osType**) w zapytaniu wymaga prawidłowej wielkości liter. Jeśli właściwość ma nieprawidłową wielkość liter, nadal może zwracać wartość, ale grupowanie lub podsumowanie będą nieprawidłowe.

## <a name="show-storage"></a>Pokaż zasoby, które zawierają magazyn

Zamiast jawnie definiować typ do dopasowania, to przykładowe zapytanie znajdzie każdy zasób platformy Azure, który zawiera (`contains`) słowo **magazyn**.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Lista wszystkich publicznych adresów IP

Podobnie jak poprzednie zapytanie, znajduje wszystko, co jest typem zawierającym słowo **publicIPAddresses**.
To zapytanie rozszerza ten wzorzec obejmujący tylko wyniki gdzie **properties.ipAddress**
`isnotempty`, aby zwrócić tylko **properties.ipAddress**, a `limit` wyniki według u góry
100. W zależności od wybranej powłoki może być konieczne zastosowanie znaku ucieczki dla cudzysłowów.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>Liczba zasobów ze skonfigurowanymi adresami IP według subskrypcji

Używając poprzedniego przykładowego zapytania i dodając elementy `summarize` i `count()`, możemy pobrać listę zasobów ze skonfigurowanymi adresami IP według subskrypcji.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>Lista zasobów z konkretną wartością tagu

Możemy ograniczyć wyniki za pomocą właściwości innych niż typ zasobu platformy Azure, takich jak tag. W tym przykładzie filtrujemy zasoby platformy Azure za pomocą nazwy tagu **Środowisko** o wartości **Wewnętrzne**.

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Aby uzyskać także nazwy i wartości tagów w zasobie, dodaj właściwość **tagi** do słowa kluczowego `project`.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Lista wszystkich kont magazynu z konkretną wartością tagu

Połącz funkcję filtrowania z poprzedniego przykładu i przefiltruj typ zasobów platformy Azure według właściwości **typ**. To zapytanie ogranicza także nasze wyszukiwanie do określonych typów zasobów platformy Azure o określonej nazwie i wartości tagu.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> W tym przykładzie użyto `==` do dopasowania zamiast warunkowego `=~`. `==` jest dopasowaniem uwzględniającym wielkość liter.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [języku zapytań](../concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](../concepts/explore-resources.md)
- Zobacz przykłady [zapytań zaawansowanych](advanced.md)