---
title: Ustaw aktywność zmiennej w Azure Data Factory
description: Dowiedz się, jak użyć działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej zdefiniowanej w potoku Data Factory
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930642"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw aktywność zmiennej w Azure Data Factory

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, bool lub Array zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący działanie działania | nie
type | Typ działania to setvariable | tak
wartość | Literał ciągu lub wartość obiektu Expression używana do ustawiania określonej zmiennej | tak
variableName | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak


## <a name="next-steps"></a>Następne kroki
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Dołącz działanie zmiennej](control-flow-append-variable-activity.md)
