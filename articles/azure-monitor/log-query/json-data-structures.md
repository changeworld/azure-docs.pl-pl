---
title: Praca z ciągami zapytań dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera samouczek dotyczący korzystania z usługi Azure Monitor Log Analytics w witrynie Azure portal do tworzenia zapytań i analizowania danych dziennika w usłudze Azure Monitor.
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
ms.openlocfilehash: 718b12c8a66d66a75796f88ef31b5f0f62abbbc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519660"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Praca z formatami JSON i danych struktury w zapytaniach dzienników usługi Azure Monitor

> [!NOTE]
> Należy wykonać [Rozpoczynanie pracy z usługą Azure Monitor Log Analytics](get-started-portal.md) i [wprowadzenie do usługi Azure Monitor dziennika zapytań](get-started-queries.md) przed wykonaniem tej lekcji.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Obiekty zagnieżdżone są obiektami, które zawierają inne obiekty w tablicy lub mapę pary klucz wartość. Te obiekty są reprezentowane jako ciągi formatu JSON. W tym artykule opisano, jak JSON służy do pobierania danych i analizowanie obiektów zagnieżdżonych.

## <a name="working-with-json-strings"></a>Praca z ciągami formatu JSON
Użyj `extractjson` umożliwiają dostęp do określonego elementu JSON w znanej ścieżce. Ta funkcja wymaga, aby wyrażenie ścieżki, która używa następujących konwencji.

- _$_ Aby odwołać się do folderu głównego
- Notacja nawias kwadratowy lub kropki służą do odwoływania się do indeksów i elementy, jak pokazano w poniższych przykładach.


Użyj nawiasów, indeksy i kropki do oddzielania elementów:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Jest to ten sam wynik, za pomocą tylko notacji nawiasy:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Jeśli istnieje tylko jeden element, można użyć tylko kropkowego:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Praca z obiektami

### <a name="parsejson"></a>parsejson
Aby uzyskać dostęp do wielu elementów w strukturze json, łatwiej jest uzyskać dostęp jako obiekt dynamiczny. Użyj `parsejson` można rzutować danych tekstowych, aby obiekt dynamiczny. Po konwersji typu dynamicznego, dodatkowe funkcje może służyć do analizowania danych.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Użyj `arraylength` do liczby elementów w tablicy:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Użyj `mvexpand` można przerwać właściwości obiektu w oddzielnych wierszach.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Użyj `buildschema` można pobrać schematu, który dopuszcza wszystkie wartości obiektu:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Dane wyjściowe są schematu w formacie JSON:
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
Te dane wyjściowe w tym artykule opisano nazwy pola obiektów i ich zgodnych typów danych. 

Zagnieżdżone obiekty mogą mieć różne schematy w następującym przykładzie:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Tworzenie schematu](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne lekcje dotyczące korzystania z zapytań dzienników w usłudze Azure Monitor:

- [Operacje na ciągach](string-operations.md)
- [Operacje daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Zaawansowane agregacji](advanced-aggregations.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)