---
title: Zapytania usługi Azure Resource Graph dla początkujących
description: Użyj usługi Azure Resource Graph do uruchamiania niektórych podstawowych zapytań.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bcc6c6e9d38948cb4be09c9f52049790ccd5ac7e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227031"
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

## <a name="language-support"></a>Obsługa języków

Interfejs wiersza polecenia platformy Azure (za pośrednictwem rozszerzenia) i program Azure PowerShell (za pośrednictwem modułu) obsługują usługę Azure Resource Graph. Przed wykonaniem dowolnego z następujących zapytań sprawdź, czy Twoje środowisko jest gotowe. Zobacz [Interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Program Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module), gdzie znajdziesz kroki instalacji i weryfikacji wybranego środowiska powłoki.

## <a name="count-resources"></a>Liczba zasobów platformy Azure

To zapytanie zwraca liczbę zasobów platformy Azure, które istnieją w subskrypcjach, do których masz dostęp. Jest to również dobre zapytanie do weryfikowania, czy wybrana powłoka ma zainstalowane odpowiednie składniki usługi Azure Resource Graph w kolejności pracy.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```powershell
Search-AzureRmGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Lista zasobów posortowana według nazwy

Bez ograniczania do typu zasobu lub konkretnej dopasowywanej właściwości, to zapytanie zwraca tylko **nazwę**, **typ**, i **lokalizację** zasobów platformy Azure, ale używa polecenia `order by` do sortowania ich według właściwości **nazwa** w kolejności rosnącej (`asc`).

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Pokaż wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej

Zamiast pobierania wszystkich zasobów platformy Azure, jeśli chcemy pobrać tylko listę maszyn wirtualnych (typu `Microsoft.Compute/virtualMachines`), możemy dopasować właściwość **typ** w wynikach.
Podobnie jak w przypadku poprzedniego zapytania, element `desc` zmienia `order by` na kolejność malejącą. `=~` w dopasowaniu typu nakazuje usłudze Resource Graph ignorowanie wielkości liter.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```powershell
Search-AzureRmGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
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

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
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

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
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

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
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

```powershell
Search-AzureRmGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Lista wszystkich publicznych adresów IP

Podobnie jak poprzednie zapytanie, znajduje wszystko, co było typem zawierającym słowo **publicIPAddresses**. To zapytanie rozszerza ten wzorzec w celu wykluczenia wyników, w których właściwość **properties.ipAddress** ma wartość null, aby zwrócić tylko właściwości **properties.ipAddress** i ograniczyć (`limit`) wyniki do pierwszych 100. W zależności od wybranej powłoki może być konieczne zastosowanie znaku ucieczki dla cudzysłowów.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

```powershell
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>Liczba zasobów ze skonfigurowanymi adresami IP według subskrypcji

Używając poprzedniego przykładowego zapytania i dodając elementy `summarize` i `count()`, możemy pobrać listę zasobów ze skonfigurowanymi adresami IP według subskrypcji.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

```powershell
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
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

```powershell
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name"
```

Gdyby było również konieczne podanie tagów zasobu i ich wartości, ten przykład można rozszerzyć, dodając właściwość **tagi** do słowa kluczowego `project`.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```powershell
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Lista wszystkich kont magazynu z konkretną wartością tagu

Łącząc możliwość filtrowania z poprzedniego przykładu z użyciem filtrowania według typu zasobów platformy Azure według właściwości **typ**, możemy ograniczyć nasze wyszukiwanie do określonych typów zasobów platformy Azure przy użyciu określonej nazwy i wartości tagu.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> W tym przykładzie użyto `==` do dopasowania zamiast warunkowego `=~`. `==` jest dopasowaniem uwzględniającym wielkość liter.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku zapytań](../concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](../concepts/explore-resources.md)
- Zobacz przykłady [zapytań zaawansowanych](advanced.md)