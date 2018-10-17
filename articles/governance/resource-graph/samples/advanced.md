---
title: Zaawansowane zapytania usługi Azure Resource Graph
description: Usługa Azure Resource Graph umożliwia uruchamianie pewnych zaawansowanych zapytań.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a9281bc333b3edd12501a6813f12b9e7ad1bf59f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219271"
---
# <a name="advanced-resource-graph-queries"></a>Zaawansowane zapytania usługi Resource Graph

Pierwszym krokiem do zrozumienia zapytań usługi Azure Resource Graph jest podstawowa wiedza na temat [języka zapytań](../concepts/query-language.md). Jeśli nie znasz jeszcze usługi [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), zalecane jest zapoznanie się z podstawami, aby zrozumieć sposób tworzenia żądań dotyczących zasobów, których szukasz.

Omówimy następujące zaawansowane zapytania:

> [!div class="checklist"]
> - [Pobieranie pojemności i rozmiaru zestawu skalowania maszyn wirtualnych](#vmss-capacity)
> - [Wyświetlanie listy wszystkich nazw tagów](#list-all-tags)
> - [Wyświetlanie maszyn wirtualnych dopasowanych przez wyrażenie regularne](#vm-regex)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="language-support"></a>Obsługa języków

Interfejs wiersza polecenia platformy Azure (za pośrednictwem rozszerzenia) i program Azure PowerShell (za pośrednictwem modułu) obsługują usługę Azure Resource Graph. Przed wykonaniem dowolnego z następujących zapytań sprawdź, czy Twoje środowisko jest gotowe. Zobacz [Interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Program Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module), gdzie znajdziesz kroki instalacji i weryfikacji wybranego środowiska powłoki.

## <a name="vmss-capacity"></a>Pobieranie pojemności i rozmiaru zestawu skalowania maszyn wirtualnych

To zapytanie szuka zasobów zestawu skalowania maszyn wirtualnych (VMSS) i pobiera różne szczegółowe informacje, takie jak rozmiar maszyny wirtualnej i pojemność zestawu skalowania. Te informacje używają funkcji `toint()`, aby rzutować pojemność na liczbę, co pozwala na sortowanie. To polecenie również zmienia nazwy zwracanych wartości na niestandardowe nazwy właściwości.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```powershell
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Wyświetlanie listy wszystkich nazw tagów

To zapytanie rozpoczyna się od tagu i tworzy obiekt JSON zawierający listę wszystkich unikatowych nazw tagów i odpowiadających im typów.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```powershell
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Wyświetlanie maszyn wirtualnych dopasowanych przez wyrażenie regularne

To zapytanie szuka maszyn wirtualnych, które odpowiadają [wyrażeniu regularnemu](/dotnet/standard/base-types/regular-expression-language-quick-reference) (nazywanemu _regex_).
Polecenie **matches regex @** pozwala na określenie wyrażenia regularnego do dopasowania, czyli **^Contoso(.*)[0-9]+$**. Definicja tego wyrażenia regularnego jest wyjaśniona jako:

- `^` - Dopasowanie musi zaczynać się od początku ciągu.
- `Contoso` - Dopasowywany ciąg podstawowy (z uwzględnieniem wielkości liter).
- `(.*)` - Dopasowanie podwyrażenia:
  - `.` - Dopasowuje dowolny pojedynczy znak (z wyjątkiem nowego wiersza).
  - `*` - Dopasowuje poprzedni element zero lub więcej razy.
- `[0-9]` - Dopasowanie grupy znaków dla cyfry od 0 do 9.
- `+` - Dopasowuje poprzedni element co najmniej raz.
- `$` - Dopasowanie poprzedniego elementu musi wystąpić na końcu ciągu.

Po dopasowaniu według nazwy zapytanie rzutuje nazwę i porządkuje rosnąco według nazwy.

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Następne kroki

- Zobacz przykłady [zapytań dla początkujących](starter.md)
- Dowiedz się więcej o [języku zapytań](../concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](../concepts/explore-resources.md)