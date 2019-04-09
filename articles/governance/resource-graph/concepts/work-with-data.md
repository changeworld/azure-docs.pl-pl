---
title: Praca z dużymi zestawami danych
description: Dowiedz się, jak pobrać i kontroli dużych zestawów danych podczas pracy z wykres zasobów platformy Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 40aa8ca0ebfcc8eb5b686143960af1441768622a
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058395"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Praca z zestawami danych w dużej ilości zasobów platformy Azure

Wykres zasobów platformy Azure jest przeznaczona dla pracę i pobieranie informacji na temat zasobów w środowisku platformy Azure. Wykres zasobów sprawia, że szybkie, pobieranie tych danych nawet wtedy, gdy podczas badania tysięcy rekordów. Wykres zasobów ma kilka opcji do pracy z tymi dużych zestawów danych.

## <a name="data-set-result-size"></a>Rozmiar wyników zestawu danych

Domyślnie wykres zasobów ogranicza dowolne zapytanie zwraca tylko **100** rekordów. Ten formant użytkownika i usługa ochronę niezamierzone zapytań, które mogłyby spowodować dużych zestawów danych. W większości przypadków to zdarzenie występuje, ponieważ klient jest eksperymentowanie z zapytaniami do znalezienia i Filtruj zasoby w taki sposób, który odpowiada ich potrzebom. Ten formant jest inny niż w [górnej](/azure/kusto/query/topoperator) lub [limit](/azure/kusto/query/limitoperator) operatory języka Eksploratora danych usługi Azure, aby ograniczyć wyniki.

> [!NOTE]
> Korzystając z **pierwszy**, zaleca się Uporządkuj wyników według co najmniej jedną kolumnę z `asc` lub `desc`. Bez sortowania, zwrócone wyniki są losowych i repeatable nie.

Domyślny limit można zastąpić za pomocą wszystkich metod interakcji z wykresem zasobów. W poniższych przykładach pokazano, jak zmienić limit rozmiaru zestawu danych, aby _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

W [interfejsu API REST](/rest/api/azureresourcegraph/resources/resources), znajduje się kontrolka **$top** i jest częścią **QueryRequestOptions**.

Formant który jest _najbardziej restrykcyjne_ zostanie zarejestrowane. Na przykład, jeśli zapytanie używa **górnej** lub **limit** operatorów i spowodują więcej rekordów niż **pierwszy**, maksymalna liczba rekordów zwracane, może być taka sama jak **Pierwszy**. Podobnie jeśli **górnej** lub **limit** jest mniejszy niż **pierwszy**, rekord, w zwróconym zestawie będzie mniejsze wartości ustawionej przez **górnej** lub **limit**.

**Pierwszy** obecnie ma maksymalną dozwoloną wartość _5000_.

## <a name="skipping-records"></a>Pomija rekordy

Opcja dalej do pracy z dużymi zestawami danych jest **Pomiń** kontroli. Dzięki temu Twoje zapytanie, aby przeskoczyć nad lub pominąć zdefiniowanej liczby rekordów przed zwróceniem wyników. **Pomiń** jest przydatne w przypadku zapytań, które sortowanie wyników w znaczący sposób, w których celem jest pobrać teraz rekordów gdzieś w trakcie wykonywania zestawu wyników. Jeśli wyniki potrzebne znajdują się na końcu zwróconego zestawu danych, jest bardziej wydajne, korzysta z konfiguracji sortowania i zamiast tego Pobierz wyniki w górnej części zestawu danych.

> [!NOTE]
> Korzystając z **Pomiń**, zaleca się Uporządkuj wyników według co najmniej jedną kolumnę z `asc` lub `desc`. Bez sortowania, zwrócone wyniki są losowych i repeatable nie.

W poniższych przykładach pokazano, jak pominąć pierwszy _10_ rekordów zapytanie spowoduje, zamiast uruchamiania zwracany wynik ustawione przy użyciu rekordu 11:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

W [interfejsu API REST](/rest/api/azureresourcegraph/resources/resources), znajduje się kontrolka **$skip** i jest częścią **QueryRequestOptions**.

## <a name="paging-results"></a>Stronicowanie wyników

Gdy jest to konieczne, można przerwać zestawu do prezentowania mniejszych zestawów rekordów do przetwarzania wyników, lub ponieważ zestaw wyników przekracza maksymalną dozwoloną wartość _1000_ zwrócone rekordy, użyj stronicowania. [Interfejsu API REST](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** zawiera wartości, aby wskazać wyników, dzielone zestawu: **resultTruncated** i **$skipToken** .
**resultTruncated** jest wartość logiczna, która informuje użytkownika, jeśli istnieją dodatkowe rekordy nie jest zwracana w odpowiedzi. Ten stan może być również zidentyfikować, kiedy **liczba** właściwość jest mniejsza niż **totalRecords** właściwości. **totalRecords** definiuje, ile rekordy, które pasujących do zapytania.

Gdy **resultTruncated** jest **true**, **$skipToken** właściwość jest ustawiona w odpowiedzi. Ta wartość jest używana przy użyciu tej samej wartości zapytania i subskrypcji do następnego zestawu rekordów pasujących do zapytania.

> [!IMPORTANT]
> Zapytanie musi **projektu** **identyfikator** pola w kolejności do dzielenia na strony do pracy. Jeśli brakuje zapytania, odpowiedź interfejsu API REST nie będzie zawierać **$skipToken**.

Aby uzyskać przykład, zobacz [następnej strony kwerendy](/rest/api/azureresourcegraph/resources/resources#next_page_query) w dokumentacji interfejsu API REST.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz język używany w [początkowego zapytania](../samples/starter.md)
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md)
- Dowiedz się, jak [eksplorować zasoby](explore-resources.md)