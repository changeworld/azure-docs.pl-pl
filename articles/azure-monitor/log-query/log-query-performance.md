---
title: Pisanie zapytań wydajne dzienników w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Odwołania do zasobów do nauki, jak pisać zapytania w usłudze Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519364"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Pisanie zapytań wydajne dzienników w usłudze Azure Monitor
Ten artykuł zawiera zalecenia dotyczące pisania zapytań wydajne dziennika w usłudze Azure Monitor. Korzystając z następujących strategii, można zagwarantować, że Twoje zapytania będą uruchamiane szybko i przy minimalnym dla.

## <a name="scope-your-query"></a>Ustal zakres zapytania
Istnienie zapytania przetwarzania większej ilości danych niż jest potrzebna może prowadzić do kwerendy długotrwałych i często skutkuje zbyt dużej ilości danych w wynikach do skutecznego analizowania. W skrajnych przypadkach zapytanie może nawet limit czasu i zakończyć się niepowodzeniem.

### <a name="specify-your-data-source"></a>Określ źródło danych
Pierwszym krokiem podczas pisania efektywnej kwerendy jest ograniczenie jego zakres, aby jego źródłom danych wymagane. Określanie tabeli jest zawsze preferowana względem uruchomiona usługa search szerokości tekstu, takie jak `search *`. Kwerendy określonej tabeli, uruchom zapytanie nazwę tabeli, tak jak poniżej:

``` Kusto
requests | ...
```

Możesz użyć [wyszukiwania](/azure/kusto/query/searchoperator) do wyszukiwania wartości w wielu kolumnach w określonych tabel przy użyciu zapytania, podobnie do poniższego:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Użyj [Unii](/azure/kusto/query/unionoperator) kwerendy kilka tabel, podobnie do poniższego:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Określ zakres czasu
Należy również ograniczyć zapytanie do zakresu czasu dla danych, która jest wymagana. Domyślnie zapytanie obejmuje dane zebrane w ciągu ostatnich 24 godzin. Możesz zmienić tę opcję w [selektor zakresu czasu](get-started-portal.md#select-a-time-range) lub jawnie dodać do zapytania. Zaleca się dodać filtr czasu natychmiast po nazwę tabeli, tak, aby pozostała część zapytania tylko przetwarza dane, w tym zakresie:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Pobieranie najnowszych danych

Aby zwrócić tylko najnowszych danych, użyj *górnej* zalogowany operatora, jak poniższe zapytanie, które zwraca 10 najnowszych rekordów *ślady* tabeli:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrowanie rekordów
Aby zapoznać się z tylko dzienniki, które odpowiadają określony warunek, należy użyć *gdzie* operatora, jak następującego zapytania, które zwraca tylko rekordy, w którym _severityLevel_ wartość jest większa niż 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Porównywanie ciągów
Gdy [oceny ciągi](/azure/kusto/query/datatypes-string-operators), zazwyczaj należy korzystać `has` zamiast `contains` podczas wyszukiwania dla pełnego tokenów. `has` jest bardziej wydajne, ponieważ nie ma on wyszukiwania podciągów.

## <a name="returned-columns"></a>Zwrócone kolumny

Użyj [projektu](/azure/kusto/query/projectoperator) zawęzić zestaw kolumn, które są przetwarzane w celu tylko te, które należy:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Za pomocą [rozszerzyć](/azure/kusto/query/extendoperator) do obliczania wartości i utworzyć własne kolumny, zazwyczaj będzie bardziej wydajne, aby filtrować według kolumny tabeli.

Na przykład pierwszy poniższe zapytanie filtrujące na _operacji\_nazwa_ może być bardziej efektywne niż druga, która tworzy nową _subskrypcji_ kolumny i filtry na nim:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Za pomocą sprzężeń
Korzystając z [sprzężenia](/azure/kusto/query/joinoperator) operatora, wybierz tabelę, z mniejszą liczbę wierszy w lewej części zapytania.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o najlepszych praktykach zapytań, zobacz [zapytania najlepszych rozwiązań](/azure/kusto/query/best-practices).