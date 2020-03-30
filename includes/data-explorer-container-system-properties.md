---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79129267"
---
### <a name="event-system-properties-mapping"></a>Mapowanie właściwości systemu zdarzeń

> [!Note]
> * Właściwości systemu są obsługiwane dla zdarzeń pojedynczego rekordu.
> * W `csv` przypadku mapowania właściwości są dodawane na początku rekordu. W `json` przypadku mapowania właściwości są dodawane zgodnie z nazwą wyświetlaną na liście rozwijanej.

Jeśli w sekcji **Źródło danych** tabeli **wybrano właściwości systemu zdarzeń,** w schemacie tabeli i w mapowaniu należy uwzględnić następujące właściwości.

**Przykład schematu tabeli**

Jeśli dane zawierają trzy`Timespan`kolumny `Metric`( `Value`, , i ) `x-opt-enqueued-time` `x-opt-offset`oraz właściwości, które należy uwzględnić, są i , utwórz lub zmień schemat tabeli za pomocą tego polecenia:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Przykład mapowania CSV**

Uruchom następujące polecenia, aby dodać dane na początku rekordu. Zanotuj wartości porządkowe.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Przykład mapowania JSON**

Dane są dodawane przy użyciu nazw właściwości systemu, które pojawiają się na liście **właściwości systemu** zdarzenia bloku **połączenia danych.** Uruchom te polecenia:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
