---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129267"
---
### <a name="event-system-properties-mapping"></a>Mapowanie właściwości systemu zdarzeń

> [!Note]
> * Właściwości systemu są obsługiwane dla zdarzeń z pojedynczym rekordem.
> * Na potrzeby mapowania `csv` na początku rekordu są dodawane właściwości. Na potrzeby mapowania `json` właściwości są dodawane według nazwy, która pojawia się na liście rozwijanej.

W przypadku wybrania **Właściwości systemu zdarzeń** w sekcji **Źródło danych** tabeli należy uwzględnić następujące właściwości w schemacie i mapowaniu tabeli.

**Przykład schematu tabeli**

Jeśli dane zawierają trzy kolumny (`Timespan`, `Metric`i `Value`) i uwzględnione właściwości są `x-opt-enqueued-time` i `x-opt-offset`, Utwórz lub Zmień schemat tabeli przy użyciu tego polecenia:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Przykład mapowania CSV**

Uruchom następujące polecenia, aby dodać dane na początku rekordu. Zwróć uwagę na wartości porządkowe.

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

Dane są dodawane przy użyciu nazw właściwości systemu, które są wyświetlane na liście **Właściwości systemu zdarzeń** bloku **połączenia danych** . Uruchom następujące polecenia:

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
