---
title: Praca z dużymi zestawami danych
description: Dowiedz się, jak uzyskać, sformatować, pomiń rekordy w dużych zestawach danych podczas pracy z programem Azure Resource Graph.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064740"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Praca z dużymi zestawami danych zasobów platformy Azure

Usługa Azure Resource Graph jest przeznaczona do pracy z zasobami w środowisku platformy Azure i uzyskiwania informacji o zasobach. Wykres zasobów sprawia, że uzyskanie tych danych szybko, nawet podczas wykonywania zapytań o tysiące rekordów. Wykres zasobów ma kilka opcji pracy z tymi dużymi zestawami danych.

Aby uzyskać wskazówki dotyczące pracy z zapytaniami z wysoką częstotliwością, zobacz [Wskazówki dotyczące ograniczonych żądań](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Rozmiar wyniku zestawu danych

Domyślnie wykres zasobów ogranicza wszystkie zapytania do zwracania tylko **100** rekordów. Ten formant chroni zarówno użytkownika, jak i usługę przed niezamierzonymi zapytaniami, które mogłyby spowodować duże zestawy danych. To zdarzenie najczęściej dzieje się jako klient eksperymentuje z zapytaniami, aby znaleźć i filtrować zasoby w sposób, który odpowiada ich określonym potrzebom. Ten formant różni się od używania [operatorów](/azure/kusto/query/topoperator) języka [Usługi](/azure/kusto/query/limitoperator) Azure Data Explorer w celu ograniczenia wyników.

> [!NOTE]
> W przypadku korzystania z **first**zaleca się kolejność wyników `asc` przez `desc`co najmniej jedną kolumnę z lub . Bez sortowania zwracane wyniki są losowe i nie powtarzalne.

Domyślny limit można zastąpić za pomocą wszystkich metod interakcji z wykresem zasobów. Poniższe przykłady pokazują, jak zmienić limit rozmiaru zestawu danych na _200:_

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

W [interfejsie API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)formant jest **$top** i jest częścią **QueryRequestOptions**.

Kontrola, która jest _najbardziej restrykcyjna,_ wygra. Na przykład, jeśli kwerenda używa operatorów **górnych** lub **limit** i spowoduje więcej rekordów niż **Pierwszy,** maksymalna zwracana wartość rekordów będzie równa **First**. Podobnie, jeśli **górna** lub **limit** jest mniejsza niż **Pierwszy,** zwrócony zestaw rekordów będzie mniejszą wartością skonfigurowaną przez **górę** lub **limit**.

**Pierwszy** obecnie ma maksymalną dozwoloną wartość _5000_.

## <a name="skipping-records"></a>Pomijanie rekordów

Następną opcją do pracy z dużymi zestawami danych jest **Skip** kontroli. Ten formant umożliwia kwerendy przeskoczyć lub pominąć zdefiniowaną liczbę rekordów przed zwróceniem wyników. **Skip** jest przydatne w przypadku kwerend, które sortują wyniki w znaczący sposób, gdzie intencją jest uzyskanie rekordów gdzieś w środku zestawu wyników. Jeśli potrzebne wyniki znajdują się na końcu zwracanego zestawu danych, bardziej efektywne jest użycie innej konfiguracji sortowania i pobranie wyników z górnej części zestawu danych.

> [!NOTE]
> W przypadku korzystania z **funkcji Pomiń**zaleca się `asc` uporządkowanie wyników o co najmniej jedną kolumnę z lub `desc`. Bez sortowania zwracane wyniki są losowe i nie powtarzalne.

Poniższe przykłady pokazują, jak pominąć pierwsze _10_ rekordów, które spowoduje kwerenda, zamiast tego rozpoczęcie zwracanego zestawu wyników z 11 rekordem:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

W [interfejsie API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)formant jest **$skip** i jest częścią **QueryRequestOptions**.

## <a name="paging-results"></a>Stronicowanie wyników

Gdy konieczne jest przerwanie wyniku ustawionego na mniejsze zestawy rekordów do przetworzenia lub ponieważ zestaw wyników przekroczy maksymalną dozwoloną wartość _1000_ zwróconych rekordów, użyj stronicowania. [Interfejs API REST](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** zawiera wartości wskazujące, że zestaw wyników został podzielony: **resultTruncated** i **$skipToken**.
**resultTruncated** jest wartością logiczną, która informuje konsumenta, jeśli istnieją dodatkowe rekordy nie zwrócone w odpowiedzi. Ten warunek można również zidentyfikować, gdy właściwość **count** jest mniejsza niż **właściwość totalRecords.** **totalRecords** definiuje liczbę rekordów, które pasują do kwerendy.

 **wynikTruncated** jest **true,** gdy albo stronicowania jest `id` wyłączona lub nie jest możliwe ze względu na brak kolumny lub gdy istnieje mniej zasobów dostępnych niż kwerenda żąda. Gdy **wynikTruncated** jest **true**, **$skipToken** właściwość nie jest ustawiona.

Poniższe przykłady pokazują, jak **pominąć** pierwsze rekordy 3000 i zwrócić **pierwsze** rekordy 1000 po tych rekordach pominiętych za pomocą interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Kwerenda musi **rzutować** pole **identyfikatora,** aby podział na strony działał. Jeśli brakuje jej w kwerendzie, odpowiedź nie będzie zawierać **$skipToken**.

Na przykład zobacz [Kwerenda strony następnej](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) w dokumentów interfejsu API REST.

## <a name="formatting-results"></a>Formatowanie wyników

Wyniki kwerendy wykres zasobów są dostarczane w dwóch formatach, _Tabela_ i _ObjectArray_. Format jest skonfigurowany z **wynikiemFormat** parametru jako część opcji żądania. Format _tabeli_ jest wartością domyślną **dla resultFormat**.

Wyniki z interfejsu wiersza polecenia platformy Azure są domyślnie dostarczane w json. Wyniki w programie Azure PowerShell są **pscustomObject** domyślnie, ale można `ConvertTo-Json` szybko przekonwertować na JSON przy użyciu polecenia cmdlet. W przypadku innych sdk wyniki kwerendy można skonfigurować do wyprowadzania formatu _ObjectArray._

### <a name="format---table"></a>Format — tabela

Domyślny _format, Tabela,_ zwraca wyniki w formacie JSON zaprojektowanym w celu podświetlenia wartości projektu kolumny i wiersza właściwości zwracanych przez kwerendę. Format ten bardzo przypomina dane zdefiniowane w tabeli strukturalnej lub arkuszu kalkulacyjnym z kolumnami zidentyfikowanymi najpierw, a następnie każdym wierszem reprezentującym dane wyrównane do tych kolumn.

Oto przykład wyniku kwerendy z formatowaniem _tabeli:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Format - ObjectArray

Format _ObjectArray_ zwraca również wyniki w formacie JSON. Jednak ten projekt jest wyrównany do relacji pary klucz/wartość wspólnej w JSON, gdzie kolumna i dane wiersza są dopasowywać się w grupach tablic.

Oto przykład wyniku kwerendy z formatowaniem _ObjectArray:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Oto kilka przykładów ustawiania **resultFormat** do korzystania z formatu _ObjectArray:_

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [kwerendach startowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [kwerendach zaawansowanych](../samples/advanced.md).
- Dowiedz się więcej o [eksplorowanie zasobów](explore-resources.md).