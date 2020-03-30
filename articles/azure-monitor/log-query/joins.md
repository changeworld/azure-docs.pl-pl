---
title: Dołączanie do zapytań dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł zawiera lekcję dotyczącą używania sprzężeń w kwerendach dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670206"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Dołączanie do zapytań dziennika usługi Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy wykonać wprowadzenie [do usługi Azure Monitor Log Analytics](get-started-portal.md) i [zapytań dziennika usługi Azure Monitor.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Sprzężenia umożliwiają analizowanie danych z wielu tabel w tej samej kwerendzie. Łączą wiersze dwóch zestawów danych przez pasujące wartości określonych kolumn.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

W tym przykładzie pierwszy zestaw danych filtruje dla wszystkich zdarzeń logowania. Jest to połączone z drugim zestawem danych, który filtruje wszystkie zdarzenia wylogowania. Przewidywane kolumny to _Komputer,_ _Konto,_ _TargetLogonId_i _TimeGenerated_. Zestawy danych są skorelowane przez kolumnę udostępnioną _TargetLogonId_. Dane wyjściowe są pojedynczym rekordem na korelację, która ma czas logowania i wylogowania.

Jeśli oba zestawy danych mają kolumny o tych samych nazwach, kolumny zestawu danych po prawej stronie otrzymają numer indeksu, więc w tym przykładzie wyniki pokażą _TargetLogonId_ z wartościami z tabeli po lewej stronie i _TargetLogonId1_ z wartościami z tabeli po prawej stronie. W takim przypadku druga kolumna _TargetLogonId1_ `project-away` została usunięta przy użyciu operatora.

> [!NOTE]
> Aby zwiększyć wydajność, należy zachować tylko odpowiednie kolumny połączonych `project` zestawów danych, przy użyciu operatora.


Użyj następującej składni, aby połączyć dwa zestawy danych, a klucz sprzężony ma inną nazwę między dwiema tabelami:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Tabele odnośników
Typowym zastosowaniem sprzężeń jest przy `datatable` użyciu statycznego mapowania wartości przy użyciu, które mogą pomóc w przekształcaniu wyników w bardziej reprezentacyjny sposób. Na przykład, aby wzbogacić dane zdarzenia zabezpieczeń o nazwę zdarzenia dla każdego identyfikatora zdarzenia.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Łączenie za pomocą datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Sprzężenie rodzajów
Określ typ sprzężenia z argumentem _rodzaju._ Każdy typ wykonuje inną dopasować między rekordami danej tabel, jak opisano w poniższej tabeli.

| Typ sprzężenia | Opis |
|:---|:---|
| innerunique (innerunique) | Jest to domyślny tryb sprzężenia. Najpierw znajdują się wartości dopasowanej kolumny w lewej tabeli, a zduplikowane wartości są usuwane.  Następnie zestaw unikatowych wartości jest dopasowywał się do prawej tabeli. |
| Wewnętrzny | W wynikach uwzględniono tylko pasujące rekordy w obu tabelach. |
| lewaka | Wszystkie rekordy w lewej tabeli i pasujące rekordy w prawej tabeli są uwzględniane w wynikach. Niedopasowane właściwości danych wyjściowych zawierają wartości null.  |
| leftanti | Rekordy z lewej strony, które nie mają dopasowań z prawej strony są uwzględniane w wynikach. Tabela wyników zawiera tylko kolumny z lewej tabeli. |
| leftsemi (leftsemi) | Rekordy z lewej strony, które mają dopasowania z prawej strony są uwzględniane w wynikach. Tabela wyników zawiera tylko kolumny z lewej tabeli. |


## <a name="best-practices"></a>Najlepsze rozwiązania

Aby uzyskać optymalną wydajność, należy wziąć pod uwagę następujące kwestie:

- Użyj filtru czasu w każdej tabeli, aby zmniejszyć rekordy, które muszą być oceniane dla sprzężenia.
- Użyj `where` `project` i zmniejszyć liczbę wierszy i kolumn w tabelach wejściowych przed sprzężeniem.
- Jeśli jedna tabela jest zawsze mniejsza od drugiej, użyj jej jako lewej strony sprzężenia.


## <a name="next-steps"></a>Następne kroki
Zobacz inne lekcje dotyczące korzystania z zapytań dziennika usługi Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Wykresy](charts.md)
