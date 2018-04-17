---
title: Za pomocą etykiety do dokumentu zapytania w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące korzystania z etykiet do dokumentu zapytania w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 48fe625573639c0ec98e672f02a35e4a9ae268e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Za pomocą etykiety do dokumentu zapytania w usłudze Azure SQL Data Warehouse
Porady dotyczące korzystania z etykiet do dokumentu zapytania w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.


## <a name="what-are-labels"></a>Co to są etykiety?
Magazyn danych SQL obsługuje pojęcie o nazwie etykiety zapytania. Przed przejściem do dowolnej głębokości, Przyjrzyjmy się przykładem:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz znaczniki ciąg "Moje zapytania etykieta" w zapytaniu. Ten tag jest szczególnie przydatne, ponieważ etykieta to zapytanie może przy użyciu widoków DMV. Wykonanie zapytania dotyczącego etykiety udostępnia mechanizm lokalizowania problem zapytania i pomaga w identyfikacji postępu za pośrednictwem ELT Uruchom.

Dobrym konwencji nazewnictwa naprawdę pomaga. Na przykład etykieta począwszy od projektu, procedury, instrukcji lub komentarz pomaga do jednoznacznej identyfikacji zapytania między cały kod w kontroli źródła.

Następujące zapytanie używa widoku dynamicznego zarządzania, aby przeprowadzić wyszukiwanie według etykiety.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Istotne jest umieścić nawiasy kwadratowe lub cudzysłowów wokół etykiety word podczas wykonywania zapytania. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie są rozdzielone. 
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).


