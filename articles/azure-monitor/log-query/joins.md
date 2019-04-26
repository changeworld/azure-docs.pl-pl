---
title: Sprzężenia w zapytaniach dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera lekcji dotyczących używania sprzężeń w zapytaniach dzienników usługi Azure Monitor.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 2ea5b4e3af6591e6e25a863998baa7cecb3e29e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60520115"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Sprzężenia w zapytaniach dzienników usługi Azure Monitor

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą Azure Monitor Log Analytics](get-started-portal.md) i [zapytań dzienników usługi Azure Monitor](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Przyłączenia pozwalają analizować dane z wielu tabel w jednym zapytaniu. Scalają wiersze dwa zestawy danych, dopasowując wartości określone kolumny.


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

W tym przykładzie pierwszego zestawu danych służy do przefiltrowania wszystkich zdarzeń logowania. To jest sprzężony z drugiego zestawu danych, który filtruje dla wszystkich zdarzeń wylogowania. Przewidywany kolumny są _komputera_, _konta_, _TargetLogonId_, i _TimeGenerated_. Zestawy danych są powiązane za pomocą udostępnionego kolumny _TargetLogonId_. Wynikiem jest jeden rekord dla korelacji, który ma czas logowania i wylogowywania.

Jeśli oba zestawy danych ma kolumny z tej samej nazwy, kolumny zestawu danych po prawej stronie zapewnianą numer indeksu, dlatego w tym przykładzie wyniki będą widoczne _TargetLogonId_ przy użyciu wartości z tabeli po lewej stronie i  _TargetLogonId1_ przy użyciu wartości z tabeli po prawej stronie. W tym przypadku drugi _TargetLogonId1_ kolumny został usunięty przy użyciu `project-away` operatora.

> [!NOTE]
> Aby zwiększyć wydajność, należy zachować tylko odpowiednich kolumn sprzężonych-zestawów danych, za pomocą `project` operatora.


Użyj następującej składni, aby dołączyć dwa zestawy danych i połączone klucz ma pod inną nazwą, między dwiema tabelami:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Tabele odnośników
Typowym zastosowaniem sprzężeń używa statycznego mapowania wartości za pomocą `datatable` , mogą pomóc w transformacji wyniki w sposób bardziej zawartości. Na przykład aby wzbogacić zabezpieczeń dane zdarzeń z nazwą zdarzenia dla każdego zdarzenia identyfikatora.

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

![Dołącz do przy użyciu elementu datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Dołącz do typów
Określ typ sprzężenia z _rodzaj_ argumentu. Każdy typ wykonuje różne dopasowanie między rekordami w danej tabeli, zgodnie z opisem w poniższej tabeli.

| Typ przyłączenia | Opis |
|:---|:---|
| innerunique | Jest to domyślny tryb złączenia. Najpierw znajdują się wartości dopasowane kolumny w tabeli po lewej stronie, a zduplikowane wartości są usuwane.  Następnie zestaw unikatowych wartości jest dopasowywana tabelą po prawej stronie. |
| wewnętrzny | Tylko pasujące rekordy w obu tabelach są uwzględnione w wynikach. |
| wartości leftouter | Wszystkie rekordy w tabeli po lewej stronie i rekordy w tabeli po prawej znajdują się w wynikach. Właściwości niedopasowane dane wyjściowe zawierają wartości null.  |
| leftanti | Rekordy z lewej strony, które nie mają zgodne z prawej strony znajdują się w wynikach. Tabela wyników zawiera tylko kolumny z tabeli po lewej stronie. |
| leftsemi | Rekordy z lewej strony, które mają zgodne z prawej strony znajdują się w wynikach. Tabela wyników zawiera tylko kolumny z tabeli po lewej stronie. |


## <a name="best-practices"></a>Najlepsze praktyki

Należy wziąć pod uwagę następujące kwestie, aby uzyskać optymalną wydajność:

- Umożliwia filtr czasu dla każdej tabeli ograniczenie rekordy, które muszą być obliczane w celu utworzenia sprzężenia.
- Użyj `where` i `project` zmniejszenie liczby wierszy i kolumn w tabelach danych wejściowych przed sprzężenia.
- Jeśli jedna tabela jest zawsze mniejsza niż ten drugi, użyć jej jako z lewej strony sprzężenia.


## <a name="next-steps"></a>Kolejne kroki
Zobacz inne lekcje dotyczące korzystania z usługi Azure Monitor dziennika zapytań:

- [Operacje na ciągach](string-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [JSON i struktur danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Wykresy](charts.md)