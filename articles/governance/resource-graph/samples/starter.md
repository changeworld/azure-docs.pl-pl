---
title: Przykłady zapytań początkowych
description: Użyj grafu zasobów platformy Azure, aby uruchomić kilka początkowych zapytań, w tym zliczanie zasobów, porządkowanie zasobów lub przez określony tag.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 14bac2299505214b8b087946222c5560a9d90efd
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976865"
---
# <a name="starter-resource-graph-queries"></a>Początkowe zapytania grafu zasobów

Pierwszym krokiem do interpretacji zapytań za pomocą usługi Azure Resource Graph jest podstawowa znajomość [języka zapytań](../concepts/query-language.md). Jeśli nie znasz już [usługi Azure Eksplorator danych](../../../data-explorer/data-explorer-overview.md), zalecamy zapoznanie się z podstawowymi informacjami na temat tworzenia żądań dla szukanych zasobów.

Przeprowadzimy przez następujące początkowe zapytania:

> [!div class="checklist"]
> - [Liczba zasobów platformy Azure](#count-resources)
> - [Wyświetl listę zasobów posortowanych według nazwy](#list-resources)
> - [Pokaż wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej](#show-vms)
> - [Pokaż pięć pierwszych maszyn wirtualnych według nazwy i ich typu systemu operacyjnego](#show-sorted)
> - [Liczba maszyn wirtualnych według typu systemu operacyjnego](#count-os)
> - [Pokaż zasoby zawierające magazyn](#show-storage)
> - [Wyświetl listę wszystkich publicznych adresów IP](#list-publicip)
> - [Liczba zasobów z adresami IP skonfigurowanymi przez subskrypcję](#count-resources-by-ip)
> - [Wyświetlanie listy zasobów o określonej wartości tagu](#list-tag)
> - [Wyświetl listę wszystkich kont magazynu z określoną wartością tagu](#list-specific-tag)
> - [Wyświetlanie aliasów zasobu maszyny wirtualnej](#show-aliases)
> - [Pokaż różne wartości dla określonego aliasu](#distinct-alias-values)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free) .

## <a name="language-support"></a>Obsługa języków

Interfejs wiersza polecenia platformy Azure (za pomocą rozszerzenia) i Azure PowerShell (za pomocą modułu) obsługuje Wykres zasobów platformy Azure. Przed uruchomieniem dowolnego z poniższych zapytań Sprawdź, czy środowisko jest gotowe. Zobacz [interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) , aby uzyskać instrukcje dotyczące instalowania i weryfikowania wybranego środowiska powłoki.

## <a name="a-namecount-resourcescount-azure-resources"></a>@no__t — zasoby platformy Azure 0Count

To zapytanie zwraca liczbę zasobów platformy Azure, które istnieją w subskrypcjach, do których masz dostęp. Jest to również dobre zapytanie, aby sprawdzić, czy zainstalowano odpowiednie składniki grafu zasobów platformy Azure i w kolejności roboczej.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a>zasoby @no__t 0List posortowane według nazwy

To zapytanie zwraca dowolny typ zasobu, ale tylko **nazwę**, **Typ**i właściwości **lokalizacji** . Używa `order by` do sortowania właściwości według właściwości **name** w kolejności rosnącej (`asc`).

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Show wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej

Aby wyświetlić tylko maszyny wirtualne (które są typu `Microsoft.Compute/virtualMachines`), możemy dopasować **Typ** właściwości w wynikach. Podobnie jak w przypadku poprzedniego zapytania, `desc` zmienia `order by` na malejąco. @No__t-0 w dopasowaniu typów instruuje Graf zasobów, aby nie uwzględniać wielkości liter.

```kusto
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

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Show pięć pierwszych maszyn wirtualnych według nazwy i ich typu systemu operacyjnego

To zapytanie będzie używać `top` do pobierania tylko pięciu pasujących rekordów, które są uporządkowane według nazwy. Typ zasobu platformy Azure to `Microsoft.Compute/virtualMachines`. `project` informuje Wykres zasobów platformy Azure o właściwościach do uwzględnienia.

```kusto
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

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a>@no__t 0Count maszyn wirtualnych według typu systemu operacyjnego

Na podstawie poprzedniego zapytania nadal ograniczamy zasoby platformy Azure typu `Microsoft.Compute/virtualMachines`, ale nie ograniczają już liczby zwracanych rekordów.
Zamiast tego użyto `summarize` i `count()`, aby zdefiniować sposób grupowania i agregowania wartości według właściwości, które w tym przykładzie są `properties.storageProfile.osDisk.osType`. Przykład sposobu, w jaki ten ciąg wygląda w pełnym obiekcie, znajduje się w temacie [Eksplorowanie zasobów — odnajdywanie maszyn wirtualnych](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Innym sposobem zapisu tego samego zapytania jest `extend` a właściwości i nadaj mu nazwę tymczasową do użycia w zapytaniu, w tym przypadku **system operacyjny**. **system operacyjny** jest następnie używany przez `summarize` i `count()`, jak w poprzednim przykładzie.

```kusto
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
> Należy pamiętać, że podczas gdy `=~` umożliwia dopasowywanie wielkości liter, użycie właściwości (takich jak **Properties. obszarze storageprofile. osDisk. osType**) w zapytaniu wymaga, aby wielkość liter nie była poprawna. Jeśli właściwość jest w nieprawidłowym przypadku, nadal może zwrócić wartość, ale grupowanie lub podsumowanie będzie nieprawidłowe.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a>zasoby @no__t 0Show zawierające magazyn

Zamiast jawnie zdefiniować typ do dopasowania, to przykładowe zapytanie spowoduje znalezienie dowolnego zasobu platformy Azure, który `contains` **Magazyn**wyrazów.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>List wszystkie publiczne adresy IP

Podobnie jak w przypadku poprzedniego zapytania, Znajdź wszystkie elementy, które są typu z wyrazem **adresów publicipaddress**.
To zapytanie jest rozszerzane na ten wzorzec, aby zawierało tylko wyniki, w których **właściwości. ipaddress**
 @ no__t-2, aby zwracać tylko **właściwości. ipaddress**i do `limit` wyników według początku
100. Może być konieczne wypróbowanie cudzysłowów w zależności od wybranej powłoki.

```kusto
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

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a>zasoby usługi @no__t 0Count z adresami IP skonfigurowanymi przez subskrypcję

Korzystając z poprzedniego przykładowego zapytania i dodawania `summarize` i `count()`, możemy uzyskać listę według subskrypcji zasobów ze skonfigurowanymi adresami IP.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a>@no__t — zasoby 0List z określoną wartością tagu

Możemy ograniczyć wyniki według właściwości innych niż typ zasobu platformy Azure, takich jak tag. W tym przykładzie filtrowanie zasobów platformy Azure jest zgodne z nazwą tagu **środowiska** , która ma wartość **wewnętrzną**.

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Aby określić, jakie Tagi zawiera zasób i ich wartości, Dodaj **Tagi** właściwości do słowa kluczowego `project`.

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>List wszystkie konta magazynu z określoną wartością tagu

Połącz funkcje filtru poprzedniego przykładu i odfiltruj typ zasobu platformy Azure według **typu** . To zapytanie ogranicza także wyszukiwanie określonych typów zasobów platformy Azure o określonej nazwie i wartości tagu.

```kusto
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
> W tym przykładzie używa `==` w celu dopasowania zamiast warunku `=~`. `==` to dopasowanie uwzględniające wielkość liter.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a>aliasy @no__t 0Show dla zasobu maszyny wirtualnej

[Aliasy Azure Policy](../../policy/concepts/definition-structure.md#aliases) są używane przez Azure Policy do zarządzania zgodnością zasobów. Wykres zasobów platformy Azure może zwracać _aliasy_ typu zasobu. Te wartości są przydatne do porównywania bieżącej wartości aliasów podczas tworzenia niestandardowej definicji zasad. Tablica _aliasów_ nie jest domyślnie określona w wynikach zapytania. Użyj `project aliases`, aby jawnie dodać go do wyników.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>Show różne wartości dla określonego aliasu

Wyświetlanie wartości aliasów w pojedynczym zasobie jest przydatne, ale nie pokazuje prawdziwej wartości przy użyciu grafu zasobów platformy Azure do wykonywania zapytań między subskrypcjami. Ten przykład sprawdza wszystkie wartości określonego aliasu i zwraca różne wartości.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku zapytań](../concepts/query-language.md)
- Dowiedz się więcej na temat [eksplorowania zasobów](../concepts/explore-resources.md)
- Zobacz przykłady [zapytań zaawansowanych](advanced.md)
