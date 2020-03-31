---
title: Opis języka zapytań
description: W tym artykule tabele wykres zasobów i dostępnych typów danych Kusto, operatorów i funkcji, które można uzyskać z usługą Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927495"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Opis języka kwerendy programu Azure Resource Graph

Język zapytań dla usługi Azure Resource Graph obsługuje szereg operatorów i funkcji. Każda praca i działają w oparciu o [Kusto Query Language (KQL)](/azure/kusto/query/index). Aby dowiedzieć się więcej o języku zapytań używanym przez program Resource Graph, zacznij od [samouczka dla języka KQL](/azure/kusto/query/tutorial).

Ten artykuł obejmuje składniki języka obsługiwane przez wykres zasobów:

- [Tabele wykresów zasobów](#resource-graph-tables)
- [Obsługiwane elementy języka KQL](#supported-kql-language-elements)
- [Znaki ucieczki](#escape-characters)

## <a name="resource-graph-tables"></a>Tabele wykresów zasobów

Wykres zasobów zawiera kilka tabel dla danych, które przechowuje o typach zasobów Menedżera zasobów i ich właściwości. Te tabele mogą `join` `union` być używane z lub operatorów, aby uzyskać właściwości z powiązanych typów zasobów. Oto lista tabel dostępnych na wykresie zasobów:

|Tabele wykresów zasobów |Opis |
|---|---|
|Resources |Tabela domyślna, jeśli żaden nie został zdefiniowany w kwerendzie. Większość typów zasobów Menedżera zasobów i właściwości są tutaj. |
|Zasoby |Obejmuje typy i `Microsoft.Resources/subscriptions`dane subskrypcji (w wersji zapoznawczej -- ) i grupy zasobów (`Microsoft.Resources/subscriptions/resourcegroups`) . |
|Źródło usług doradczych |Zawiera zasoby `Microsoft.Advisor`związane _z_ . |
|AlertyManagementResources |Zawiera zasoby `Microsoft.AlertsManagement`związane _z_ . |
|Zasoby konserwacyjne |Zawiera zasoby `Microsoft.Maintenance`związane _z_ . |
|Zasoby zabezpieczeń |Zawiera zasoby `Microsoft.Security`związane _z_ . |

Aby uzyskać pełną listę obejmującą typy zasobów, zobacz [Odwołanie: Obsługiwane tabele i typy zasobów](../reference/supported-tables-resources.md).

> [!NOTE]
> _Zasoby_ są tabelą domyślną. Podczas wykonywania zapytań _resources_ tabeli, nie jest wymagane, `join` `union` aby podać nazwę tabeli, chyba że lub są używane. Jednak zalecaną praktyką jest zawsze zawierać tabelę początkową w kwerendzie.

Użyj Eksploratora wykresu zasobów w portalu, aby dowiedzieć się, jakie typy zasobów są dostępne w każdej tabeli. Alternatywnie należy użyć kwerendy, takiej jak `<tableName> | distinct type` uzyskanie listy typów zasobów, które obsługuje dana tabela wykresu zasobów, które istnieją w danym środowisku.

Poniższa kwerenda `join`przedstawia prosty plik . Wynik kwerendy łączy kolumny ze sobą, a wszystkie zduplikowane nazwy kolumn z tabeli połączonej, _ResourceContainers_ w tym przykładzie, są dołączane z **1**. Ponieważ _tabela ResourceContainers_ zawiera typy zarówno dla subskrypcji, jak i grup zasobów, każdy typ może być używany do łączenia się z zasobem z tabeli _zasobów._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Poniższa kwerenda przedstawia bardziej `join`złożone użycie programu . Kwerenda ogranicza tabelę sprzężona `project` do zasobów subskrypcji i zawiera tylko oryginalny _identyfikator subskrypcji_ pola i pole _nazwy_ o zmienionej nazwie _na SubName_. Zmiana nazwy pola `join` pozwala uniknąć dodania go jako _nazwy1,_ ponieważ pole już istnieje w _zasobach_. Oryginalna tabela jest `where` filtrowana `project` z następującymi kolumnami z obu tabel. Wynik kwerendy to pojedynczy typ magazynu kluczy, nazwa magazynu kluczy i nazwa subskrypcji, w jakiej się znajduje.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Podczas ograniczania `join` wyników `project`z , `join` właściwość używana przez odnoszą się do dwóch tabel, `project` _subscriptionId_ w powyższym przykładzie, muszą być uwzględnione w .

## <a name="supported-kql-language-elements"></a>Obsługiwane elementy języka KQL

Wykres zasobów obsługuje wszystkie [typy danych](/azure/kusto/query/scalar-data-types/)KQL, [funkcje skalarne,](/azure/kusto/query/scalarfunctions) [operatory skalarne](/azure/kusto/query/binoperators)i [funkcje agregacji.](/azure/kusto/query/any-aggfunction) Operatory [tabelaryczne](/azure/kusto/query/queries) są obsługiwane przez wykres zasobów, z których niektóre mają różne zachowania.

### <a name="supported-tabulartop-level-operators"></a>Obsługiwane operatory tabelaryczne/najwyższego poziomu

Oto lista operatorów tabelaricznych KQL obsługiwanych przez wykres zasobów z określonymi przykładami:

|Kql |Przykładowa kwerenda wykresu zasobów |Uwagi |
|---|---|---|
|[Liczba](/azure/kusto/query/countoperator) |[Zliczanie magazynów kluczy](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Pokaż różne wartości dla określonego aliasu](../samples/starter.md#distinct-alias-values) | |
|[Rozszerzyć](/azure/kusto/query/extendoperator) |[Liczba maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Magazyn kluczy z nazwą subskrypcji](../samples/advanced.md#join) |Dołącz do obsługiwanych smaków: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [wewnętrzny](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limit 3 `join` w jednym zapytaniu. Niestandardowe strategie sprzężenia, takie jak sprzężenie emisji, nie są dozwolone. Może być używany w obrębie jednej tabeli lub między _resources_ i _ResourceContainers_ tabel. |
|[Limit](/azure/kusto/query/limitoperator) |[Lista wszystkich publicznych adresów IP](../samples/starter.md#list-publicip) |Synonimem`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Legacy operator, `mv-expand` użyj zamiast tego. _RowLimit_ max 400. Wartość domyślna to 128. |
|[mv-rozwiń](/azure/kusto/query/mvexpandoperator) |[Lista usługi Cosmos DB z określonymi lokalizacjami zapisu](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max 400. Wartość domyślna to 128. |
|[Zamówienia](/azure/kusto/query/orderoperator) |[Lista zasobów posortowanych według nazwy](../samples/starter.md#list-resources) |Synonimem`sort` |
|[Projektu](/azure/kusto/query/projectoperator) |[Lista zasobów posortowanych według nazwy](../samples/starter.md#list-resources) | |
|[projekt-away](/azure/kusto/query/projectawayoperator) |[Usuwanie kolumn z wyników](../samples/advanced.md#remove-column) | |
|[Sortowania](/azure/kusto/query/sortoperator) |[Lista zasobów posortowanych według nazwy](../samples/starter.md#list-resources) |Synonimem`order` |
|[Podsumować](/azure/kusto/query/summarizeoperator) |[Liczba zasobów platformy Azure](../samples/starter.md#count-resources) |Uproszczona tylko pierwsza strona |
|[wziąć](/azure/kusto/query/takeoperator) |[Lista wszystkich publicznych adresów IP](../samples/starter.md#list-publicip) |Synonimem`limit` |
|[Do góry](/azure/kusto/query/topoperator) |[Pokaż pięć pierwszych maszyn wirtualnych według nazwy i ich typu systemu operacyjnego](../samples/starter.md#show-sorted) | |
|[Unii](/azure/kusto/query/unionoperator) |[Łączenie wyników z dwóch zapytań w jeden wynik](../samples/advanced.md#unionresults) |\[ `kind=` `inner` \| `outer` Dozwolona pojedyncza \] \[ `withsource=` _tabela:_ \] _Tabela_nazwy kolumny _T_ `| union` . Limit 3 `union` nóg w jednym zapytaniu. Rozmyta rozdzielczość `union` stołów nóg nie jest dozwolona. Może być używany w obrębie jednej tabeli lub między _resources_ i _ResourceContainers_ tabel. |
|[where](/azure/kusto/query/whereoperator) |[Pokaż zasoby zawierające magazyn](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Znaki ucieczki

Niektóre nazwy właściwości, takie jak `.` `$`te, które zawierają lub , muszą być zawinięte lub zmienione w kwerendzie lub nazwa właściwości jest interpretowana niepoprawnie i nie zapewnia oczekiwanych wyników.

- `.`- Zawiń nazwę właściwości jako taką:`['propertyname.withaperiod']`
  
  Przykładowa kwerenda, która zawija właściwość _odata.type:_

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Ucieczka znak w nazwie właściwości. Używany znak ucieczki zależy od powłoki Wykres zasobów jest uruchamiany z.

  - **Bash** - `\`

    Przykładowa kwerenda, która wyujmuje _ \$typ_ właściwości w bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Nie uciekaj przed postacią. `$`

  - **Powershell** - ``` ` ```

    Przykładowa kwerenda, która wyujmuje _ \$typ_ właściwości w programie PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [kwerendach startowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [kwerendach zaawansowanych](../samples/advanced.md).
- Dowiedz się więcej o [eksplorowanie zasobów](explore-resources.md).