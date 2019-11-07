---
title: Używanie etykiet do Instrumentacji zapytań
description: Porady dotyczące używania etykiet do Instrumentacji zapytań w Azure SQL Data Warehouse do tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4ca0b3564418aafe774158057bc3efd541f71f66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692824"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Używanie etykiet do Instrumentacji zapytań w Azure SQL Data Warehouse
Porady dotyczące używania etykiet do Instrumentacji zapytań w Azure SQL Data Warehouse do tworzenia rozwiązań.


## <a name="what-are-labels"></a>Co to są etykiety?
SQL Data Warehouse obsługuje koncepcję o nazwie etykiety zapytań. Przed przejściem do dowolnej głębokości Przyjrzyjmy się przykładowi:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz określa ciąg "My Query Label" w zapytaniu. Ten tag jest szczególnie przydatny, ponieważ etykieta jest w trakcie wykonywania zapytania przez widoków DMV. Wykonywanie zapytań dotyczących etykiet zapewnia mechanizm lokalizowania zapytań o problemy oraz ułatwiający identyfikację postępu przez uruchomienie ELT.

Dobrą konwencją nazewnictwa są naprawdę pomocne. Na przykład uruchomienie etykiety z projektem, procedurą, INSTRUKCJą lub KOMENTARZEm pomaga w jednoznacznej identyfikacji zapytania między kodem w kontroli źródła.

Poniższe zapytanie używa dynamicznego widoku zarządzania do wyszukiwania według etykiety.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Podczas wykonywania zapytań należy umieścić nawiasy kwadratowe lub podwójne cudzysłowy wokół etykiety wyrazu. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie zostanie on rozdzielony. 
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).


