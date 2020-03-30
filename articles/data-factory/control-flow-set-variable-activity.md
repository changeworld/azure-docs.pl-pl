---
title: Ustawianie aktywności zmiennej w fabryce danych platformy Azure
description: Dowiedz się, jak ustawić wartość istniejącej zmiennej zdefiniowanej w potoku Fabryki danych za pomocą działania Ustaw zmienną.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930642"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustawianie aktywności zmiennej w fabryce danych platformy Azure

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, Bool lub Array zdefiniowanej w potoku fabryki danych.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagany
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący działanie | nie
type | Typ działania jest SetVariable | tak
value | Wartość obiektu literału ciągu lub wyrażenia używana do ustawiania określonej zmiennej | tak
Variablename | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o powiązanej aktywności przepływu sterowania obsługiwanej przez fabrykę danych: 

- [Działanie dołączania zmiennej](control-flow-append-variable-activity.md)
