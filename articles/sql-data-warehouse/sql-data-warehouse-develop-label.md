---
title: Za pomocą etykiety do dokumentu zapytania w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące korzystania z etykiet do dokumentu zapytania w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 22737faa146d83f1f31489125dee4146c7d11ac1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524248"
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


