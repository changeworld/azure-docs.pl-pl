---
title: Dołącz działania zmiennej w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować działanie Dołącz do zmiennej, aby dodać wartość do istniejącej zmiennej tablicy zdefiniowanych w potoku usługi fabryka danych
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60557226"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Dołącz działania zmiennej w usłudze Azure Data Factory

Działanie Dołącz zmienna służy do Dodaj wartość istniejącej zmiennej tablicy zdefiniowanych w potoku usługi fabryka danych.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Tak
description | Tekst opisujący, co działanie robi | nie
type | Typ działania jest AppendVariable | tak
value | Ciąg literału lub wyrażenie wartości obiektu używane do dołączenia do określonej zmiennej | tak
nazwa_zmiennej | Nazwa zmiennej, która będzie modyfikowana przez działanie, zmienna musi być typu "Array" | tak

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat obsługiwanych przez usługę Data Factory działanie przepływu sterowania pokrewne: 

- [Ustaw działania zmiennej](control-flow-set-variable-activity.md)
