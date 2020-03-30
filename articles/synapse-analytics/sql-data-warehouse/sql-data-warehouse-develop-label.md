---
title: Używanie etykiet do instrumencie zapytań
description: Porady dotyczące używania etykiet do instrumencie zapytań w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 828e4a406cd0fb12877af44263ab1f338c20850c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351675"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Używanie etykiet do instrumencie zapytań w usłudze Azure SQL Data Warehouse
Porady dotyczące używania etykiet do instrumencie zapytań w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.


## <a name="what-are-labels"></a>Co to są etykiety?
Usługa SQL Data Warehouse obsługuje koncepcję zwaną etykietami zapytań. Przed wejściem w jakąkolwiek głębię przyjrzyjmy się przykładowi:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz oznacza ciąg "Moja etykieta zapytania" do kwerendy. Ten tag jest szczególnie przydatny, ponieważ etykieta jest w stanie zapytania za pośrednictwem dmvs. Wykonywanie zapytań o etykiety zapewnia mechanizm lokalizowania zapytań o problemy i pomaga zidentyfikować postęp za pośrednictwem uruchomienia ELT.

Dobra konwencja nazewnictwa naprawdę pomaga. Na przykład rozpoczęcie etykiety z PROJEKTU, PROCEDURY, INSTRUKCJI lub KOMENTARZ pomaga jednoznacznie zidentyfikować kwerendę wśród całego kodu w formancie źródłowym.

Poniższa kwerenda używa dynamicznego widoku zarządzania do wyszukiwania według etykiety.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Istotne jest, aby umieścić nawiasy kwadratowe lub cudzysłowy wokół etykiety słowa podczas wykonywania zapytań. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie jest rozdzielany. 
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).


