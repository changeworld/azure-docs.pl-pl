---
title: Pracuj z dużymi zestawami danych
description: Zapoznaj się z tematem jak uzyskać i kontrolować duże zestawy danych podczas pracy z usługą Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 4da890a5ef7acb44d0e8628dc4ec3904f6a065e4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980307"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Praca z dużymi zestawami danych zasobów platformy Azure

Usługa Azure Resource Graph została zaprojektowana do pracy z i uzyskiwania informacji o zasobach w środowisku platformy Azure. Wykres zasobów umożliwia szybkie pobieranie danych, nawet w przypadku wykonywania zapytań o tysiące rekordów. Wykres zasobów zawiera kilka opcji pracy z tymi dużymi zestawami danych.

Aby uzyskać wskazówki dotyczące pracy z kwerendami z dużą częstotliwością, zobacz [wskazówki dotyczące żądań ograniczających](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Rozmiar wyniku zestawu danych

Domyślnie wykres zasobów ogranicza wszelkie zapytania, aby zwracać tylko **100** rekordów. Ta kontrolka chroni zarówno użytkownika, jak i usługę przed niezamierzonymi zapytaniami, które spowodują powstanie dużych zestawów danych. To zdarzenie najczęściej występuje, gdy klient próbuje znaleźć i filtrować zasoby w taki sposób, aby odpowiadały one konkretnym potrzebom. Ta kontrolka różni się od użycia [górnego](/azure/kusto/query/topoperator) lub [ograniczającego](/azure/kusto/query/limitoperator) operatory języka Azure Eksplorator danych, aby ograniczyć wyniki.

> [!NOTE]
> Przy **pierwszym**użyciu zaleca się kolejność wyników według co najmniej jednej kolumny z `asc` lub `desc`. Bez sortowania, zwracane wyniki są losowe i nie można ich powtarzać.

Domyślny limit można zastąpić wszystkimi metodami współpracy z wykresem zasobów. W poniższych przykładach pokazano, jak zmienić limit rozmiaru zestawu danych na _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

W [interfejsie API REST](/rest/api/azureresourcegraph/resources/resources)formant jest **$Top** i jest częścią **QueryRequestOptions**.

Formant, który jest _najbardziej restrykcyjny_ , zostanie wygrany. Na przykład jeśli w zapytaniu jest używany operator **Top** lub **Limit** , a wynikiem będzie więcej rekordów niż **pierwsze**, Maksymalna liczba zwracanych rekordów będzie równa **pierwsze**. Podobnie, jeśli **górny** lub **Limit** jest mniejszy niż **pierwszy**, zwracany zestaw rekordów będzie mniejszą wartością skonfigurowaną przez wartość **Top** lub **Limit**.

**Pierwszy** obecnie ma maksymalną dozwoloną wartość _5000_.

## <a name="skipping-records"></a>Pomijanie rekordów

Następną opcją pracy z dużymi zestawami danych jest kontrolka **pomijania** . Ta kontrolka umożliwia kwerendy przeskoczenie lub pominięcie zdefiniowanej liczby rekordów przed zwróceniem wyników. **Pomijanie** jest przydatne w przypadku zapytań, które sortują wyniki w zrozumiały sposób, gdy celem jest uzyskanie rekordów w środku zestawu wyników. Jeśli wyniki są konieczne na końcu zwracanego zestawu danych, bardziej wydajne jest użycie innej konfiguracji sortowania i pobranie wyników z góry zestawu danych.

> [!NOTE]
> W przypadku używania **pomijania**zaleca się kolejność wyników według co najmniej jednej kolumny z `asc` lub `desc`. Bez sortowania, zwracane wyniki są losowe i nie można ich powtarzać.

W poniższych przykładach pokazano, jak pominąć pierwsze _10_ rekordów, a zamiast tego zostanie wyświetlony zwrócony zestaw wyników z 11 rekordu:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

W [interfejsie API REST](/rest/api/azureresourcegraph/resources/resources)formant jest **$Skip** i jest częścią **QueryRequestOptions**.

## <a name="paging-results"></a>Wyniki stronicowania

Gdy konieczne jest przerwanie zestawu wyników w mniejszych zestawach rekordów do przetworzenia lub ponieważ zestaw wyników będzie przekroczyć maksymalną dozwoloną wartość _1000_ zwracanych rekordów, należy użyć stronicowania. [Interfejs API REST](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** udostępnia wartości wskazujące, że zestaw wyników został podzielony: **resultTruncated** i **$skipToken**.
**resultTruncated** jest wartością logiczną, która informuje odbiorcę, jeśli w odpowiedzi nie zostały zwrócone dodatkowe rekordy. Ten stan można również zidentyfikować, gdy właściwość **Count** jest mniejsza niż Właściwość **totalRecords** . **totalRecords** definiuje liczbę rekordów, które pasują do zapytania.

Gdy **resultTruncated** ma **wartość true**, właściwość **$skipToken** jest ustawiana w odpowiedzi. Ta wartość jest używana z tymi samymi wartościami zapytania i subskrypcji w celu uzyskania następnego zestawu rekordów, które pasują do zapytania.

W poniższych przykładach pokazano, jak **pominąć** pierwsze 3000 rekordów i zwrócić **pierwsze** 1000 rekordów po pominiętych za pomocą interfejsu wiersza polecenia platformy Azure i Azure PowerShell:

```azurecli-interactive
az graph query -q "project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Zapytanie musi uwzględniać wartość pola **ID** **, aby** można było rozpocząć stronicowanie. Jeśli brakuje tego zapytania, odpowiedź nie będzie zawierać **$skipToken**.

Aby zapoznać się z przykładem, zobacz [następne zapytanie na stronie](/rest/api/azureresourcegraph/resources/resources#next-page-query) w dokumentacji interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w [zapytaniach początkowych](../samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](../samples/advanced.md).
- Dowiedz się, jak [eksplorować zasoby](explore-resources.md).