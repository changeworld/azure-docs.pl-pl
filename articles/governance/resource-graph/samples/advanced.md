---
title: Przykłady zapytań zaawansowanych
description: Użyj grafu zasobów platformy Azure do uruchamiania niektórych zaawansowanych zapytań, w tym pojemności zestawu skalowania maszyn wirtualnych, wyświetlania wszystkich używanych tagów i zgodnych maszyn wirtualnych z wyrażeniami regularnymi.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 8b848eac338260340cb9160a72f33294e51101f2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387572"
---
# <a name="advanced-resource-graph-queries"></a>Zaawansowane zapytania usługi Resource Graph

Pierwszym krokiem do zrozumienia zapytań usługi Azure Resource Graph jest podstawowa wiedza na temat [języka zapytań](../concepts/query-language.md). Jeśli nie znasz jeszcze usługi [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), zalecane jest zapoznanie się z podstawami, aby zrozumieć sposób tworzenia żądań dotyczących zasobów, których szukasz.

Omówimy następujące zaawansowane zapytania:

> [!div class="checklist"]
> - [Pobieranie pojemności i rozmiaru zestawu skalowania maszyny wirtualnej](#vmss-capacity)
> - [Usuń kolumny z wyników](#remove-column)
> - [Wyświetlanie listy wszystkich nazw tagów](#list-all-tags)
> - [Wyświetlanie maszyn wirtualnych dopasowanych przez wyrażenie regularne](#vm-regex)
> - [Wyświetlanie listy Cosmos DB z określonymi lokalizacjami zapisu](#mvexpand-cosmosdb)
> - [Magazyn kluczy z nazwą subskrypcji](#join)
> - [Wyświetlanie listy baz danych SQL i ich pul elastycznych](#join-sql)
> - [Wyświetlanie listy maszyn wirtualnych z interfejsem sieciowym i publicznym adresem IP](#join-vmpip)
> - [Znajdowanie kont magazynu z określonym tagiem w grupie zasobów](#join-findstoragetag)
> - [Łączenie wyników z dwóch zapytań w jeden wynik](#unionresults)
> - [Uwzględnij nazwy dzierżawców i subskrypcji z nazwami wyświetlanymi](#displaynames)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="language-support"></a>Obsługa języków

Interfejs wiersza polecenia platformy Azure (za pośrednictwem rozszerzenia) i program Azure PowerShell (za pośrednictwem modułu) obsługują usługę Azure Resource Graph. Przed uruchomieniem dowolnego z poniższych zapytań sprawdź, czy Twoje środowisko jest gotowe. Zobacz [Interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Program Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module), gdzie znajdziesz kroki instalacji i weryfikacji wybranego środowiska powłoki.

## <a name="vmss-capacity"></a>Uzyskiwanie pojemności zestawu skalowania i rozmiaru maszyny wirtualnej

To zapytanie szuka zasobów zestawu skalowania maszyn wirtualnych i pobiera różne szczegółowe informacje, takie jak rozmiar maszyny wirtualnej i pojemność zestawu skalowania. To zapytanie używa funkcji `toint()`, aby rzutować pojemność na liczbę, co pozwala na sortowanie. Na koniec następuje zmiana nazw kolumn na właściwości o niestandardowej nazwie.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="remove-column"></a>Usuń kolumny z wyników

Następujące zapytanie używa `summarize` do zliczania zasobów według subskrypcji, `join`, aby połączyć je z informacjami o subskrypcji z tabeli _ResourceContainers_ , a następnie `project-away`, aby usunąć niektóre kolumny.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

## <a name="list-all-tags"></a>Wyświetlanie listy wszystkich nazw tagów

To zapytanie rozpoczyna się od tagu i tworzy obiekt JSON zawierający listę wszystkich unikatowych nazw tagów i odpowiadających im typów.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Wyświetlanie maszyn wirtualnych dopasowanych przez wyrażenie regularne

To zapytanie szuka maszyn wirtualnych, które odpowiadają [wyrażeniu regularnemu](/dotnet/standard/base-types/regular-expression-language-quick-reference) (nazywanemu _regex_). **Dopasowuje wyrażenie regularne \@** pozwala nam definiować wyrażenie regularne, które jest zgodne z `^Contoso(.*)[0-9]+$`.
Definicja tego wyrażenia regularnego jest wyjaśniona jako:

- `^` - Dopasowanie musi zaczynać się od początku ciągu.
- `Contoso` - Ciąg z uwzględnieniem wielkości liter.
- `(.*)` - Dopasowanie podwyrażenia:
  - `.` - Dopasowuje dowolny pojedynczy znak (z wyjątkiem nowego wiersza).
  - `*` - Dopasowuje poprzedni element zero lub więcej razy.
- `[0-9]` - Dopasowanie grupy znaków dla cyfry od 0 do 9.
- `+` - Dopasowuje poprzedni element co najmniej raz.
- `$` - Dopasowanie poprzedniego elementu musi wystąpić na końcu ciągu.

Po dopasowaniu według nazwy zapytanie rzutuje nazwę i porządkuje rosnąco według nazwy.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="mvexpand-cosmosdb"></a>Wyświetlanie listy Cosmos DB z określonymi lokalizacjami zapisu

Następujące limity zapytania dotyczące Cosmos DB zasobów, używają `mv-expand` do rozwijania zbioru właściwości dla **właściwości. writeLocations**, a następnie pól specyficznych dla projektu i bardziej ograniczone wyniki do **właściwości. writeLocations. LocationName** wartości pasuje do elementu "Wschodnie stany USA" lub "zachodnie stany USA".

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

## <a name="join"></a>Magazyn kluczy z nazwą subskrypcji

Następujące zapytanie pokazuje złożone użycie `join`. Zapytanie ogranicza przyłączoną tabelę do zasobów subskrypcji i z `project`, aby uwzględnić tylko oryginalny identyfikator _subskrypcji_ pola i _nazwę pola Nazwa_ zmieniona na _SubName_. Zmiana nazwy pola unika `join` dodanie go jako _Name1_ , ponieważ pole już istnieje w obszarze _zasoby_. Oryginalna tabela jest filtrowana z `where`, a poniższe `project` zawierają kolumny z obu tabel. Wynikiem zapytania jest pojedynczy magazyn kluczy zawierający typ, nazwę magazynu kluczy oraz nazwę subskrypcji, w której znajduje się.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

## <a name="join-sql"></a>Wyświetlanie listy baz danych SQL i ich pul elastycznych

Następujące zapytanie używa **leftouter** `join` do łączenia zasobów SQL Database i powiązanych z nimi pul elastycznych, jeśli są one dostępne.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

## <a name="join-vmpip"></a>Wyświetlanie listy maszyn wirtualnych z interfejsem sieciowym i publicznym adresem IP

To zapytanie używa dwóch **leftouter** `join` poleceń do łączenia z maszynami wirtualnymi, powiązanymi interfejsami sieciowymi oraz wszelkimi publicznymi adresami IP związanymi z tymi interfejsami sieciowymi.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

```azurecli-interactive
azure graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

## <a name="join-findstoragetag"></a>Znajdowanie kont magazynu z określonym tagiem w grupie zasobów

Następujące zapytanie używa **innerunique** `join` do łączenia kont magazynu z grupami zasobów, które mają określoną nazwę tagu i wartość tagu.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=innerunique (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

## <a name="unionresults"></a>Łączenie wyników z dwóch zapytań w jeden wynik

Poniższe zapytanie używa `union`, aby uzyskać wyniki z tabeli _ResourceContainers_ i dodać je do wyników z tabeli _resources_ .

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

## <a name="displaynames"></a>Uwzględnij nazwy dzierżawców i subskrypcji z nazwami wyświetlanymi

To zapytanie używa nowego parametru **include** z opcjami _DisplayName_ , aby dodać **subscriptionDisplayName** i **tenantDisplayName** do wyników. Ten parametr jest dostępny tylko dla interfejsu wiersza polecenia platformy Azure i Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Jeśli zapytanie nie używa **programu Project** do określenia zwracanych właściwości, **subscriptionDisplayName** i **tenantDisplayName** są automatycznie dołączane do wyników.
> Jeśli zapytanie używa **programu Project**, każde z pól _DisplayName_ musi być jawnie dołączone do **projektu** lub nie będzie zwracane w wynikach, nawet gdy zostanie użyty parametr **include** .

## <a name="next-steps"></a>Następne kroki

- Zobacz przykłady [zapytań dla początkujących](starter.md)
- Dowiedz się więcej o [języku zapytań](../concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](../concepts/explore-resources.md)