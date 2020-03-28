---
title: Przykłady zapytań podstawowych
description: Przy użyciu usługi Azure Resource Graph uruchom kilka zapytań dla początkujących, obejmujących zliczanie i porządkowanie zasobów lub grupowanie według określonego tagu.
ms.date: 11/21/2019
ms.topic: sample
ms.openlocfilehash: b966d8c239cb6ff706c967174bcea23bf25de374
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238398"
---
# <a name="starter-resource-graph-query-samples"></a>Przykłady kwerend wykresu zasobów startowych

Pierwszym krokiem do zrozumienia zapytań usługi Azure Resource Graph jest podstawowa wiedza na temat [języka zapytań](../concepts/query-language.md). Jeśli nie jesteś jeszcze zaznajomiony z [Kusto Query Language (KQL),](/azure/kusto/query/index)zaleca się przejrzenie [samouczka dla KQL,](/azure/kusto/query/tutorial) aby zrozumieć, jak skomponować żądania zasobów, których szukasz.

Omówimy następujące podstawowe zapytania:

> [!div class="checklist"]
> - [Liczba zasobów platformy Azure](#count-resources)
> - [Zliczanie zasobów magazynu kluczy](#count-keyvaults)
> - [Lista zasobów posortowanych według nazwy](#list-resources)
> - [Pokaż wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej](#show-vms)
> - [Pokaż pięć pierwszych maszyn wirtualnych według nazwy i ich typu systemu operacyjnego](#show-sorted)
> - [Liczba maszyn wirtualnych według typu systemu operacyjnego](#count-os)
> - [Pokaż zasoby zawierające magazyn](#show-storage)
> - [Lista wszystkich publicznych adresów IP](#list-publicip)
> - [Zliczanie zasobów, które mają skonfigurowane adresy IP według subskrypcji](#count-resources-by-ip)
> - [Lista zasobów z określoną wartością znacznika](#list-tag)
> - [Wyświetlanie listy wszystkich kont magazynu z określoną wartością znacznika](#list-specific-tag)
> - [Pokazywale aliasów zasobu maszyny wirtualnej](#show-aliases)
> - [Pokaż różne wartości dla określonego aliasu](#distinct-alias-values)
> - [Pokaż nieskoczące się grupy zabezpieczeń sieci](#unassociated-nsgs)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free) przed rozpoczęciem.

## <a name="language-support"></a>Obsługa języków

Interfejs wiersza polecenia platformy Azure (za pośrednictwem rozszerzenia) i program Azure PowerShell (za pośrednictwem modułu) obsługują usługę Azure Resource Graph. Przed uruchomieniem dowolnego z poniższych zapytań sprawdź, czy Twoje środowisko jest gotowe. Zobacz [Interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Program Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module), gdzie znajdziesz kroki instalacji i weryfikacji wybranego środowiska powłoki.

## <a name="count-azure-resources"></a><a name="count-resources" />Liczba zasobów platformy Azure

To zapytanie zwraca liczbę zasobów platformy Azure, które istnieją w subskrypcjach, do których masz dostęp. Jest to również dobre zapytanie do weryfikowania, czy wybrana powłoka ma zainstalowane odpowiednie składniki usługi Azure Resource Graph w kolejności pracy.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults" />Zliczanie zasobów magazynu kluczy

Ta kwerenda `count` używa `summarize` zamiast zliczania liczby zwróconych rekordów. W liczbie uwzględniono tylko magazyny kluczy.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources" />Lista zasobów posortowana według nazwy

To zapytanie zwraca dowolny typ zasobu, ale tylko właściwości **nazwa**, **typ** i **lokalizacja**. Używa ono polecenia `order by` do sortowania właściwości według właściwości **nazwa** w kolejności rosnącej (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Pokaż wszystkie maszyny wirtualne uporządkowane według nazwy w kolejności malejącej

Aby wyświetlić listę zawierającą tylko maszyny wirtualne (które są typu `Microsoft.Compute/virtualMachines`), możemy dopasować w wynikach właściwość **typ**. Podobnie jak w przypadku poprzedniego zapytania, element `desc` zmienia `order by` na kolejność malejącą. `=~` w dopasowaniu typu nakazuje usłudze Resource Graph ignorowanie wielkości liter.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />Pokaż pierwsze pięć maszyn wirtualnych według nazwy i ich typu systemu operacyjnego

To zapytanie będzie używać elementu `top`, aby pobrać tylko pięć pasujących rekordów, uporządkowanych według nazwy. Typ zasobu platformy Azure to `Microsoft.Compute/virtualMachines`. `project` informuje usługę Azure Resource Graph, które właściwości mają być uwzględnione.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os" />Liczba maszyn wirtualnych według typu systemu operacyjnego

Opierając się na poprzednim zapytaniu, nadal ograniczamy wyniki według zasobów platformy Azure typu `Microsoft.Compute/virtualMachines`, ale nie ograniczamy już liczby zwracanych rekordów.
Zamiast tego użyliśmy elementów `summarize` i `count()`, aby określić sposób grupowania i agregacji wartości według właściwości, którą w tym przykładzie jest `properties.storageProfile.osDisk.osType`. Aby zobaczyć przykład przedstawiający wygląd tego ciągu w pełnym obiekcie, zobacz [badanie zasobów — odnajdywanie maszyn wirtualnych](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

Innym sposobem zapisania tego samego zapytania jest rozszerzenie (`extend`) właściwości i nadanie jej nazwy tymczasowej do użycia w ramach zapytania, w tym przypadku **os**. Właściwość **os** jest następnie używana przez funkcje `summarize` i `count()`, jak w poprzednim przykładzie.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

> [!NOTE]
> Pamiętaj, że o ile `=~` zezwala na dopasowanie bez uwzględniania wielkości liter, to użycie właściwości (takich jak **properties.storageProfile.osDisk.osType**) w zapytaniu wymaga prawidłowej wielkości liter. Jeśli właściwość jest niepoprawna wielkość liter, zwracana jest wartość null lub niepoprawna, a grupowanie lub podsumowanie będzie niepoprawne.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage" />Pokaż zasoby, które zawierają magazyn

Zamiast jawnie definiować typ do dopasowania, to przykładowe zapytanie znajdzie każdy zasób platformy Azure, który zawiera (`contains`) słowo **magazyn**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip" />Lista wszystkich publicznych adresów IP

Podobnie jak poprzednie zapytanie, znajduje wszystko, co jest typem zawierającym słowo **publicIPAddresses**.
Ta kwerenda rozszerza się na tym wzorcu, aby uwzględnić tylko wyniki, w których **properties.ipAddress**
`isnotempty`, aby zwrócić tylko **adres properties.ipAddress,** i wyniki `limit` przez górę
100. W zależności od wybranej powłoki może być konieczne zastosowanie znaku ucieczki dla cudzysłowów.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />Liczba zasobów ze skonfigurowanymi adresami IP według subskrypcji

Używając poprzedniego przykładowego zapytania i dodając elementy `summarize` i `count()`, możemy pobrać listę zasobów ze skonfigurowanymi adresami IP według subskrypcji.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag" />Lista zasobów z konkretną wartością tagu

Możemy ograniczyć wyniki za pomocą właściwości innych niż typ zasobu platformy Azure, takich jak tag. W tym przykładzie filtrujemy zasoby platformy Azure za pomocą nazwy tagu **Środowisko** o wartości **Wewnętrzne**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

Aby uzyskać także nazwy i wartości tagów w zasobie, dodaj właściwość **tagi** do słowa kluczowego `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />Lista wszystkich kont magazynu z konkretną wartością tagu

Połącz funkcję filtrowania z poprzedniego przykładu i przefiltruj typ zasobów platformy Azure według właściwości **typ**. To zapytanie ogranicza także nasze wyszukiwanie do określonych typów zasobów platformy Azure o określonej nazwie i wartości tagu.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

> [!NOTE]
> W tym przykładzie użyto `==` do dopasowania zamiast warunkowego `=~`. `==` jest dopasowaniem uwzględniającym wielkość liter.

## <a name="show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />Pokazywale aliasów zasobu maszyny wirtualnej

[Aliasy zasad platformy Azure](../../policy/concepts/definition-structure.md#aliases) są używane przez usługę Azure Policy do zarządzania zgodnością zasobów. Program Azure Resource Graph może zwracać _aliasy_ typu zasobu. Te wartości są przydatne do porównywania bieżącej wartości aliasów podczas tworzenia niestandardowej definicji zasad. Tablica _aliasów_ nie jest domyślnie podana w wynikach kwerendy. Użyj, `project aliases` aby jawnie dodać go do wyników.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />Pokaż różne wartości dla określonego aliasu

Wyświetlanie wartości aliasów w jednym zasobie jest przydatne, ale nie pokazuje prawdziwej wartości przy użyciu usługi Azure Resource Graph do wykonywania zapytań w ramach subskrypcji. W tym przykładzie przyjrzy się wszystkim wartościom określonego aliasu i zwróci różne wartości.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />Pokaż nieskoczące się grupy zabezpieczeń sieci

Ta kwerenda zwraca sieciowe grupy zabezpieczeń (NSG), które nie są skojarzone z interfejsem sieciowym lub podsiecią.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikona Eksploratora wykresu zasobów](../media/resource-graph-small.png) Wypróbuj tę kwerendę w Eksploratorze wykresu zasobów platformy Azure:

- Portal Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Otwórz łącze w nowej ikonie okna](../../media/new-window.png)
- Portal azure dla instytucji rządowych: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)
- Portal Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![łącze Otwórz w nowej ikonie okna](../../media/new-window.png)

---

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku zapytań](../concepts/query-language.md).
- Dowiedz się więcej o [eksplorowanie zasobów](../concepts/explore-resources.md).
- Zobacz przykłady [zapytań zaawansowanych](advanced.md).