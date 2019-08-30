---
title: Ustaw aktywność zmiennej w Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak użyć działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej zdefiniowanej w potoku Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: cfe6dd63234a7750fe01614d6f1b38bb7cce1adb
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142441"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw aktywność zmiennej w Azure Data Factory

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, bool lub Array zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący działanie działania | nie
type | Typ działania to setvariable | tak
value | Literał ciągu lub wartość obiektu Expression używana do ustawiania określonej zmiennej | tak
variableName | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak


## <a name="next-steps"></a>Następne kroki
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Dołącz działanie zmiennej](control-flow-append-variable-activity.md)
