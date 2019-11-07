---
title: Ustaw aktywność zmiennej w Azure Data Factory
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
ms.openlocfilehash: 2589e56d3170b235f31d07f902eb75c749e8573f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679302"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw aktywność zmiennej w Azure Data Factory

Użyj działania Ustaw zmienną, aby ustawić wartość istniejącej zmiennej typu String, bool lub Array zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagany
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący działanie działania | nie
type | Typ działania to setvariable | tak
wartość | Literał ciągu lub wartość obiektu Expression używana do ustawiania określonej zmiennej | tak
variableName | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak


## <a name="next-steps"></a>Następne kroki
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Dołącz działanie zmiennej](control-flow-append-variable-activity.md)
