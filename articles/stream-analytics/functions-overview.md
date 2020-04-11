---
title: Funkcje zdefiniowane przez użytkownika w usłudze Azure Stream Analytics
description: Ten artykuł zawiera omówienie funkcji zdefiniowanych przez użytkownika w usłudze Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115588"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funkcje zdefiniowane przez użytkownika w usłudze Azure Stream Analytics

Język zapytań podobny do języka SQL w usłudze Azure Stream Analytics ułatwia implementowanie logiki analizy w czasie rzeczywistym na danych przesyłania strumieniowego. Usługa Stream Analytics zapewnia dodatkową elastyczność za pośrednictwem niestandardowych funkcji, które są wywoływane w zapytaniu. Poniższy przykład kodu jest `sampleFunction` wywołany UDF, który akceptuje jeden parametr, każdy rekord wejściowy `sampleResult`odbiera zadanie, a wynik jest zapisywany na wyjściu jako .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Rodzaje funkcji

Usługa Azure Stream Analytics obsługuje następujące cztery typy funkcji: 

* Funkcje języka JavaScript zdefiniowane przez użytkownika 
* Agregaty zdefiniowane przez użytkownika w języku JavaScript 
* Funkcje zdefiniowane przez użytkownika w języku C# (przy użyciu programu Visual Studio) 
* Azure Machine Learning 

Tych funkcji można używać w scenariuszach, takich jak ocenianie w czasie rzeczywistym przy użyciu modeli uczenia maszynowego, manipulacje ciągami, złożone obliczenia matematyczne, kodowanie i dekodowanie danych. 

## <a name="limitations"></a>Ograniczenia

Funkcje zdefiniowane przez użytkownika są bezstanowe, a zwracana wartość może być tylko wartością skalarną. Nie można wywołać do zewnętrznych punktów końcowych REST z tych funkcji zdefiniowanych przez użytkownika, ponieważ prawdopodobnie będzie to miało wpływ na wydajność zadania. 

Usługa Azure Stream Analytics nie przechowuje rejestru wszystkich wywołań funkcji i zwróconych wyników. Aby zagwarantować powtarzalność — na przykład ponowne uruchomienie zadania ze starszego znacznika czasu powoduje `Date.GetData()` ponowne `Math.random()`uzyskanie tych samych wyników — nie należy używać funkcji, takich jak lub , ponieważ te funkcje nie zwracają tego samego wyniku dla każdego wywołania.  

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Wszelkie błędy środowiska uruchomieniowego są uważane za krytyczne i są rzuty uruchamiane za pośrednictwem dzienników aktywności i diagnostyki. Zaleca się, że funkcja obsługuje wszystkie wyjątki i błędy i zwraca prawidłowy wynik do kwerendy. Uniemożliwi to zadanie przejście do [stanu niepowodzenie](job-states.md).  


## <a name="next-steps"></a>Następne kroki

* [Funkcje zdefiniowane przez użytkownika JavaScript w usłudze Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Agregacje zdefiniowane przez użytkownika usługi Azure Stream Analytics w języku JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Tworzenie standardowych funkcji zdefiniowanych przez użytkownika platformy .NET dla zadań usługi Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integracja usługi Azure Stream Analytics z usługą Azure Machine Learning](machine-learning-udf.md)

