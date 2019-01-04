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
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: e904075908fe7108c0566856b25fe03be0b7fd86
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023811"
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
