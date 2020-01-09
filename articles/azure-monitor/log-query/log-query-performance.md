---
title: Pisanie wydajnych zapytań dzienników w Azure Monitor | Microsoft Docs
description: Odwołania do zasobów na potrzeby uczenia się, jak pisać zapytania w Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: 9281abaf7afd97573211ff20f15fa97b19724218
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365295"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Pisanie wydajnych zapytań dzienników w Azure Monitor
Ten artykuł zawiera zalecenia dotyczące pisania wydajnych zapytań dzienników w Azure Monitor. Korzystając z tych strategii, można zagwarantować, że zapytania będą działać szybko i przy minimalnym przeniesieniu.

## <a name="scope-your-query"></a>Określanie zakresu zapytania
Wykonanie zapytania przetwarza więcej danych, niż jest to konieczne, może prowadzić do długotrwałej kwerendy i często powodować zbyt dużą ilość danych w wynikach do efektywnego analizowania. W skrajnych przypadkach zapytanie może nawet przekroczyć limit czasu i zakończyć się niepowodzeniem.

### <a name="specify-your-data-source"></a>Określanie źródła danych
Pierwszy krok tworzenia wydajnego zapytania ogranicza swój zakres do wymaganych źródeł danych. Określanie tabeli jest zawsze preferowane w przypadku uruchamiania wyszukiwania szerokiego tekstu, takiego jak `search *`. Aby wykonać zapytanie dotyczące określonej tabeli, uruchom zapytanie z nazwą tabeli w następujący sposób:

``` Kusto
requests | ...
```

Możesz użyć [wyszukiwania](/azure/kusto/query/searchoperator) , aby wyszukać wartość w wielu kolumnach w określonych tabelach przy użyciu kwerendy podobnej do następującej:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Użyj [Union](/azure/kusto/query/unionoperator) , aby wykonać zapytanie do kilku tabel w następujący sposób:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Określ zakres czasu
Należy również ograniczyć zapytanie do wymaganego zakresu czasu danych. Domyślnie zapytanie będzie zawierać dane zebrane w ciągu ostatnich 24 godzin. Można zmienić tę opcję w [selektorze zakresu czasu](get-started-portal.md#select-a-time-range) lub dodać ją jawnie do zapytania. Najlepiej dodać filtr czasu bezpośrednio po nazwie tabeli, aby pozostała część zapytania przetwarzał tylko dane z tego zakresu:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Pobierz tylko najnowsze rekordy

Aby zwrócić tylko najnowsze rekordy, użyj operatora *Top* , jak w poniższym zapytaniu, które zwraca najnowsze 10 rekordów zarejestrowanych w tabeli *TRACES* :

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtruj rekordy
Aby przejrzeć tylko te dzienniki, które pasują do danego warunku, użyj operatora *WHERE* w następującej kwerendzie, która zwraca tylko rekordy, w których wartość _severityLevel_ jest większa niż 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Porównanie ciągów
Podczas [oceniania ciągów](/azure/kusto/query/datatypes-string-operators), zazwyczaj należy używać `has` zamiast `contains` podczas wyszukiwania pełnych tokenów. `has` jest wydajniejszy, ponieważ nie musi wyszukiwać podciągów.

## <a name="returned-columns"></a>Zwrócone kolumny

Użyj [programu Project](/azure/kusto/query/projectoperator) , aby zawęzić zestaw kolumn przetwarzanych tylko do tych, które są potrzebne:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Chociaż możesz użyć opcji [Przekrocz](/azure/kusto/query/extendoperator) , aby obliczyć wartości i utworzyć własne kolumny, zwykle bardziej wydajne będzie filtrowanie według kolumny tabeli.

Na przykład pierwsze zapytanie poniżej, które filtruje w _operacji\_nazwa_ będzie bardziej wydajne niż druga, która tworzy nową kolumnę _subskrypcji_ i filtry na niej:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Korzystanie z sprzężeń
W przypadku korzystania z operatora [Join](/azure/kusto/query/joinoperator) wybierz tabelę zawierającą mniej wierszy, które mają znajdować się po lewej stronie zapytania.


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących zapytań, zobacz [najlepsze rozwiązania dotyczące zapytań](/azure/kusto/query/best-practices).
