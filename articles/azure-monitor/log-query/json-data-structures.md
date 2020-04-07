---
title: Praca z ciągami w kwerendach dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł zawiera samouczek dotyczący używania usługi Azure Monitor Log Analytics w portalu Azure do wykonywania zapytań i analizowania danych dziennika w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672971"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Praca z JSON i strukturami danych w kwerendach dziennika usługi Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy wykonać wprowadzenie [do usługi Azure Monitor Log Analytics](get-started-portal.md) i wprowadzenie do [zapytań dziennika usługi Azure Monitor.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Obiekty zagnieżdżone to obiekty, które zawierają inne obiekty w tablicy lub mapę par klucz-wartość. Obiekty te są reprezentowane jako ciągi JSON. W tym artykule opisano, jak JSON jest używany do pobierania danych i analizowania obiektów zagnieżdżonych.

## <a name="working-with-json-strings"></a>Praca z ciągami JSON
Służy `extractjson` do uzyskiwania dostępu do określonego elementu JSON w znanej ścieżce. Ta funkcja wymaga wyrażenia ścieżki, które używa następujących konwencji.

- _$_, aby odwołać się do folderu głównego
- Użyj wspornika lub kropki, aby odwołać się do indeksów i elementów, jak pokazano w poniższych przykładach.


Użyj nawiasów dla indeksów i kropek, aby oddzielić elementy:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Jest to ten sam wynik przy użyciu tylko w nawiasach notacji:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Jeśli istnieje tylko jeden element, można użyć tylko kropki:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Praca z obiektami

### <a name="parsejson"></a>parsejson ( parsejson )
Aby uzyskać dostęp do wielu elementów w strukturze json, łatwiej jest uzyskać do niego dostęp jako obiekt dynamiczny. Służy `parsejson` do rzutowanie danych tekstowych na obiekt dynamiczny. Po przekonwertowaniu na typ dynamiczny można użyć dodatkowych funkcji do analizy danych.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>połówek
Służy `arraylength` do zliczania liczby elementów w tablicy:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Służy `mvexpand` do podziału właściwości obiektu na oddzielne wiersze.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema ( buildschema )
Służy `buildschema` do uzyskania schematu, który dopuszcza wszystkie wartości obiektu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Dane wyjściowe to schemat w formacie JSON:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
To dane wyjściowe opisuje nazwy pól obiektów i ich pasujące typy danych. 

Obiekty zagnieżdżone mogą mieć różne schematy, takie jak w poniższym przykładzie:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Tworzenie schematu](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Następne kroki
Zobacz inne lekcje dotyczące używania zapytań dziennika w usłudze Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
