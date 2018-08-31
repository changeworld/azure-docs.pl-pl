---
title: Używanie etykiet do Instrumentacji zapytań w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące używania etykiety do dokumentu zapytań w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 959fddfd24041a245f80b048eca4bef3cd612905
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301150"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Używanie etykiet do Instrumentacji zapytań w usłudze Azure SQL Data Warehouse
Porady dotyczące używania etykiety do dokumentu zapytań w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.


## <a name="what-are-labels"></a>Co to są etykiety?
Usługa SQL Data Warehouse obsługuje pojęcie etykiety zapytanie. Przed przejściem na dowolnym poziomie, Spójrzmy na przykład:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz tagi ciąg "Moje zapytania etykieta" zapytania. Ten tag jest szczególnie przydatne, ponieważ etykieta to zapytanie może za pomocą dynamicznych widoków zarządzania. Wykonywanie zapytań dotyczących etykiety udostępnia mechanizm do lokalizowania problematycznych zapytań i ułatwia identyfikowanie postęp ELT Uruchom.

Dobre konwencji nazewnictwa tak naprawdę pomaga. Na przykład etykieta począwszy od projektu, procedury, instrukcja lub komentarz pomaga do unikatowego identyfikowania zapytania wśród całego kodu w kontroli źródła.

Następujące zapytanie używa dynamicznych widoków zarządzania, aby przeprowadzić wyszukiwanie według etykiety.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Istotne jest umieszczenie nawiasy kwadratowe lub podwójny cudzysłów wokół etykiety programu word, podczas wykonywania zapytania. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie jest rozdzielany. 
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).


