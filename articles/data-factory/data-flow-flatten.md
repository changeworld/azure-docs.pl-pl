---
title: Mapowanie transformacji spłaszczania przepływu danych
description: Transformacja spłaszczania przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 435d2469be8eb572caa02e381d84ae4e9ac32f4b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674846"
---
# <a name="azure-data-factory-flatten-transformation"></a>Przekształcenie Azure Data Factory spłaszczania

Transformacja spłaszczania może służyć do przestawiania wartości tablicy w strukturze hierarchicznej na nowe wiersze, co zasadniczo denormalizuje dane.

![Przybornik transformacji](media/data-flow/flatten5.png "Przybornik transformacji")

![Spłaszcz transformację 1](media/data-flow/flatten7.png "Spłaszcz transformację 1")

## <a name="unroll-by"></a>Odwrócenie według

Najpierw wybierz kolumnę tablicową, którą chcesz odrzucić i przestawić.

![Spłaszczanie ustawień transformacji](media/data-flow/flatten1.png "Spłaszczanie ustawień transformacji")

## <a name="unroll-root"></a>Odwrócenie elementu głównego

Domyślnie automatyczny podajnik APD zostanie spłaszczony przy użyciu tablicy odwrócenia wybranej powyżej. Można też wybrać inną część hierarchii, do której ma zostać cofnięte odwrócenie.

## <a name="input-columns"></a>Kolumny wejściowe

Na koniec wybierz rzutowanie nowej struktury na podstawie pól przychodzących, a także kolumnę znormalizowaną, która została cofnięta.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Przykład

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Następne kroki

* Użyj [transformacji przestawnej](data-flow-pivot.md) , aby przestawić wiersze do kolumn.
* Użyj [transformacji UNPIVOT](data-flow-unpivot.md) , aby przestawić kolumny do wierszy.
