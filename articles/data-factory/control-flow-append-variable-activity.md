---
title: Dołączanie działania zmiennej w fabryce danych platformy Azure
description: Dowiedz się, jak ustawić działanie Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicy zdefiniowanej w potoku Data Factory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679974"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Dołączanie działania zmiennej w fabryce danych platformy Azure

Użyj działania Dołącz zmienną, aby dodać wartość do istniejącej zmiennej tablicy zdefiniowanej w potoku fabryki danych.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagany
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący działanie | nie
type | Typ działania jest AppendVariable | tak
value | Wartość obiektu literału ciągu lub wyrażenia używana do dołączania do określonej zmiennej | tak
Variablename | Nazwa zmiennej, która zostanie zmodyfikowana według działania, zmienna musi być typu "Tablica" | tak

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o powiązanej aktywności przepływu sterowania obsługiwanej przez fabrykę danych: 

- [Działanie ustawiania zmiennej](control-flow-set-variable-activity.md)
