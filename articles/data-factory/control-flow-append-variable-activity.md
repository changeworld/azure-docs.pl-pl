---
title: Działanie dołączania zmiennej w Azure Data Factory
description: Dowiedz się, jak ustawić działanie Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679974"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Działanie dołączania zmiennej w Azure Data Factory

Użyj działania Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicowej zdefiniowanej w potoku Data Factory.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagany
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący działanie działania | nie
type | Typ działania to AppendVariable | tak
wartość | Literał ciągu lub wartość obiektu Expression użyta do dołączenia do określonej zmiennej | tak
variableName | Nazwa zmiennej, która zostanie zmodyfikowana przez działanie, zmienna musi być typu "Array" | tak

## <a name="next-steps"></a>Następne kroki
Informacje na temat działania związanego z przepływem sterowania obsługiwanego przez Data Factory: 

- [Działanie ustawiania zmiennej](control-flow-set-variable-activity.md)
