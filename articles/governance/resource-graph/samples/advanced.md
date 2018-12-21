---
title: Przykłady zapytań zaawansowanych
description: Usługa Azure Resource Graph umożliwia uruchamianie pewnych zaawansowanych zapytań.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 8599d535a3a522d742207a655a8f4098d6f3f18f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309393"
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

Interfejs wiersza polecenia platformy Azure (za pośrednictwem rozszerzenia) i program Azure PowerShell (za pośrednictwem modułu) obsługują usługę Azure Resource Graph. Przed uruchomieniem dowolnego z poniższych zapytań sprawdź, czy Twoje środowisko jest gotowe. Zobacz [Interfejs wiersza polecenia platformy Azure](../first-query-azurecli.md#add-the-resource-graph-extension) i [Program Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module), gdzie znajdziesz kroki instalacji i weryfikacji wybranego środowiska powłoki.

## <a name="vmss-capacity"></a>Uzyskiwanie pojemności zestawu skalowania i rozmiaru maszyny wirtualnej

To zapytanie szuka zasobów zestawu skalowania maszyn wirtualnych i pobiera różne szczegółowe informacje, takie jak rozmiar maszyny wirtualnej i pojemność zestawu skalowania. To zapytanie używa funkcji `toint()`, aby rzutować pojemność na liczbę, co pozwala na sortowanie. Na koniec następuje zmiana nazw kolumn na właściwości o niestandardowej nazwie.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
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

```azurepowershell-interactive
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Wyświetlanie maszyn wirtualnych dopasowanych przez wyrażenie regularne

To zapytanie szuka maszyn wirtualnych, które odpowiadają [wyrażeniu regularnemu](/dotnet/standard/base-types/regular-expression-language-quick-reference) (nazywanemu _regex_).
Polecenie **matches regex @** pozwala na określenie wyrażenia regularnego do dopasowania, czyli `^Contoso(.*)[0-9]+$`. Definicja tego wyrażenia regularnego jest wyjaśniona jako:

- `^` - Dopasowanie musi zaczynać się od początku ciągu.
- `Contoso` - Ciąg z uwzględnieniem wielkości liter.
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

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Następne kroki

- Zobacz przykłady [zapytań dla początkujących](starter.md)
- Dowiedz się więcej o [języku zapytań](../concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](../concepts/explore-resources.md)
