---
title: Ustaw zmienną działania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć działania Ustaw zmienną można ustawić wartość istniejącej zmiennej zdefiniowanych w potoku usługi fabryka danych
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
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: ff9bfce1f9262d78ba17abdd88c481d5057d5f38
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076423"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Ustaw zmienną działania w usłudze Azure Data Factory

Użyj działania Ustaw zmienną można ustawić wartość istniejącej zmiennej typu String, Bool lub tablica, zdefiniowanych w potoku usługi fabryka danych.

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa działania w potoku | Yes
description | Tekst opisujący, co działanie robi | nie
type | Typ działania jest SetVariable | tak
wartość | Ciąg literału lub wyrażenie obiektu wartość używana do ustawiania określonej zmiennej | tak
nazwa_zmiennej | Nazwa zmiennej, która zostanie ustawiona przez to działanie | tak


## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat obsługiwanych przez usługę Data Factory działanie przepływu sterowania pokrewne: 

- [Dołącz działania zmiennej](control-flow-append-variable-activity.md)
