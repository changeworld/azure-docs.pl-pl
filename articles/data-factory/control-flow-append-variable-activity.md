---
title: Dołącz działania zmiennej w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować działanie Dołącz do zmiennej, aby dodać wartość do istniejącej zmiennej tablicy zdefiniowanych w potoku usługi fabryka danych
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 03652ce20d82565d5714cdc43a01a9e7c3074f6a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904292"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Dołącz działania zmiennej w usłudze Azure Data Factory

Działanie Dołącz zmienna służy do Dodaj wartość istniejącej zmiennej tablicy zdefiniowanych w potoku usługi fabryka danych.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Yes
description | Tekst opisujący, co działanie robi | nie
type | Typ działania jest AppendVariable | tak
wartość | Ciąg literału lub wyrażenie wartości obiektu używane do dołączenia do określonej zmiennej | tak
nazwa_zmiennej | Nazwa zmiennej, która będzie modyfikowana przez działanie, zmienna musi być typu "Array" | tak

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat obsługiwanych przez usługę Data Factory działanie przepływu sterowania pokrewne: 

- [Ustaw działania zmiennej](control-flow-set-variable-activity.md)
