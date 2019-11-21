---
title: Przykłady zapytań podstawowych
description: Przy użyciu usługi Azure Resource Graph uruchom kilka zapytań dla początkujących, obejmujących zliczanie i porządkowanie zasobów lub grupowanie według określonego tagu.
ms.date: 11/18/2019
ms.topic: quickstart
ms.openlocfilehash: 87061b56371bc68bd1d71cc9e9278b8c46c168a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216418"
---
# <a name="starter-resource-graph-queries"></a>Zapytania usługi Resource Graph dla początkujących

Pierwszym krokiem do zrozumienia zapytań usługi Azure Resource Graph jest podstawowa wiedza na temat [języka zapytań](../concepts/query-language.md). If you aren't already familiar with [Kusto Query Language (KQL)](/azure/kusto/query/index), it's recommended to review the [tutorial for KQL](/azure/kusto/query/tutorial) to understand how to compose requests for the resources you're looking for.

Omówimy następujące podstawowe zapytania:

> [!div class="checklist"]
> - [Liczba zasobów platformy Azure](#count-resources)
> - [Count key vault resources](#count-keyvaults)
> - [Lista zasobów posortowana według nazwy](#list-resources)
> - [Pokaż wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej](#show-vms)
> - [Pokaż pierwsze pięć maszyn wirtualnych według nazwy i ich typu systemu operacyjnego](#show-sorted)
> - [Liczba maszyn wirtualnych według typu systemu operacyjnego](#count-os)
> - [Pokaż zasoby, które zawierają magazyn](#show-storage)
> - [Lista wszystkich publicznych adresów IP](#list-publicip)
> - [Liczba zasobów ze skonfigurowanymi adresami IP według subskrypcji](#count-resources-by-ip)
> - [Lista zasobów z konkretną wartością tagu](#list-tag)
> - [Lista wszystkich kont magazynu z konkretną wartością tagu](#list-specific-tag)
> - [Show aliases for a virtual machine resource](#show-aliases)
> - [Show distinct values for a specific alias](#distinct-alias-values)
> - [Show unassociated network security groups](#unassociated-nsgs)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="language-support"></a>Obsługa języków

Interfejs wiersza polecenia platformy Azure (za pośrednictwem rozszerzenia) i program Azure PowerShell (za pośrednictwem modułu) obsługują usługę Azure Resource Graph. Przed uruchomieniem dowolnego z poniższych zapytań sprawdź, czy Twoje środowisko jest gotowe. Zobacz [Interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Program Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module), gdzie znajdziesz kroki instalacji i weryfikacji wybranego środowiska powłoki.

## <a name="a-namecount-resources-count-azure-resources"></a><a name="count-resources" />Count Azure resources

To zapytanie zwraca liczbę zasobów platformy Azure, które istnieją w subskrypcjach, do których masz dostęp. Jest to również dobre zapytanie do weryfikowania, czy wybrana powłoka ma zainstalowane odpowiednie składniki usługi Azure Resource Graph w kolejności pracy.

```kusto
Resources
| summarize count()
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namecount-keyvaults-count-key-vault-resources"></a><a name="count-keyvaults" />Count key vault resources

This query uses `count` instead of `summarize` to count the number of records returned. Only key vaults are included in the count.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namelist-resources-list-resources-sorted-by-name"></a><a name="list-resources" />List resources sorted by name

To zapytanie zwraca dowolny typ zasobu, ale tylko właściwości **nazwa**, **typ** i **lokalizacja**. Używa ono polecenia `order by` do sortowania właściwości według właściwości **nazwa** w kolejności rosnącej (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-nameshow-vms-show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Show all virtual machines ordered by name in descending order

Aby wyświetlić listę zawierającą tylko maszyny wirtualne (które są typu `Microsoft.Compute/virtualMachines`), możemy dopasować w wynikach właściwość **typ**. Podobnie jak w przypadku poprzedniego zapytania, element `desc` zmienia `order by` na kolejność malejącą. `=~` w dopasowaniu typu nakazuje usłudze Resource Graph ignorowanie wielkości liter.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-nameshow-sorted-show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />Show first five virtual machines by name and their OS type

To zapytanie będzie używać elementu `top`, aby pobrać tylko pięć pasujących rekordów, uporządkowanych według nazwy. Typ zasobu platformy Azure to `Microsoft.Compute/virtualMachines`. `project` informuje usługę Azure Resource Graph, które właściwości mają być uwzględnione.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namecount-os-count-virtual-machines-by-os-type"></a><a name="count-os" />Count virtual machines by OS type

Opierając się na poprzednim zapytaniu, nadal ograniczamy wyniki według zasobów platformy Azure typu `Microsoft.Compute/virtualMachines`, ale nie ograniczamy już liczby zwracanych rekordów.
Zamiast tego użyliśmy elementów `summarize` i `count()`, aby określić sposób grupowania i agregacji wartości według właściwości, którą w tym przykładzie jest `properties.storageProfile.osDisk.osType`. Aby zobaczyć przykład przedstawiający wygląd tego ciągu w pełnym obiekcie, zobacz [badanie zasobów — odnajdywanie maszyn wirtualnych](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

Innym sposobem zapisania tego samego zapytania jest rozszerzenie (`extend`) właściwości i nadanie jej nazwy tymczasowej do użycia w ramach zapytania, w tym przypadku **os**. Właściwość **os** jest następnie używana przez funkcje `summarize` i `count()`, jak w poprzednim przykładzie.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

> [!NOTE]
> Pamiętaj, że o ile `=~` zezwala na dopasowanie bez uwzględniania wielkości liter, to użycie właściwości (takich jak **properties.storageProfile.osDisk.osType**) w zapytaniu wymaga prawidłowej wielkości liter. If the property is the incorrect case, a null or incorrect value is returned and the grouping or summarization would be incorrect.

## <a name="a-nameshow-storage-show-resources-that-contain-storage"></a><a name="show-storage" />Show resources that contain storage

Zamiast jawnie definiować typ do dopasowania, to przykładowe zapytanie znajdzie każdy zasób platformy Azure, który zawiera (`contains`) słowo **magazyn**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namelist-publicip-list-all-public-ip-addresses"></a><a name="list-publicip" />List all public IP addresses

Podobnie jak poprzednie zapytanie, znajduje wszystko, co jest typem zawierającym słowo **publicIPAddresses**.
This query expands on that pattern to only include results where **properties.ipAddress**
`isnotempty`, to only return the **properties.ipAddress**, and to `limit` the results by the top
100. W zależności od wybranej powłoki może być konieczne zastosowanie znaku ucieczki dla cudzysłowów.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namecount-resources-by-ip-count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />Count resources that have IP addresses configured by subscription

Używając poprzedniego przykładowego zapytania i dodając elementy `summarize` i `count()`, możemy pobrać listę zasobów ze skonfigurowanymi adresami IP według subskrypcji.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namelist-tag-list-resources-with-a-specific-tag-value"></a><a name="list-tag" />List resources with a specific tag value

Możemy ograniczyć wyniki za pomocą właściwości innych niż typ zasobu platformy Azure, takich jak tag. W tym przykładzie filtrujemy zasoby platformy Azure za pomocą nazwy tagu **Środowisko** o wartości **Wewnętrzne**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

Aby uzyskać także nazwy i wartości tagów w zasobie, dodaj właściwość **tagi** do słowa kluczowego `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namelist-specific-tag-list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />List all storage accounts with specific tag value

Połącz funkcję filtrowania z poprzedniego przykładu i przefiltruj typ zasobów platformy Azure według właściwości **typ**. To zapytanie ogranicza także nasze wyszukiwanie do określonych typów zasobów platformy Azure o określonej nazwie i wartości tagu.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

> [!NOTE]
> W tym przykładzie użyto `==` do dopasowania zamiast warunkowego `=~`. `==` jest dopasowaniem uwzględniającym wielkość liter.

## <a name="a-nameshow-aliases-show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />Show aliases for a virtual machine resource

[Azure Policy aliases](../../policy/concepts/definition-structure.md#aliases) are used by Azure Policy to manage resource compliance. Azure Resource Graph can return the _aliases_ of a resource type. These values are useful for comparing the current value of aliases when creating a custom policy definition. The _aliases_ array isn't provided by default in the results of a query. Use `project aliases` to explicitly add it to the results.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-namedistinct-alias-values-show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />Show distinct values for a specific alias

Seeing the value of aliases on a single resource is helpful, but it doesn't show the true value of using Azure Resource Graph to query across subscriptions. This example looks at all values of a specific alias and returns the distinct values.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="a-nameunassociated-nsgs-show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />Show unassociated network security groups

This query returns Network Security Groups (NSGs) that aren't associated to a network interface or subnet.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph explorer icon](../media/resource-graph-small.png) Try this query in Azure Resource Graph Explorer:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Open link in new window icon](../../media/new-window.png)
- Azure Government portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Open link in new window icon](../../media/new-window.png)

---

## <a name="next-steps"></a>Następne kroki

- Learn more about the [query language](../concepts/query-language.md).
- Learn more about how to [explore resources](../concepts/explore-resources.md).
- See samples of [Advanced queries](advanced.md).