---
title: Używanie etykiet do instrumencie zapytań
description: Porady dotyczące używania etykiet do instrumencie zapytań w puli synapse SQL do tworzenia rozwiązań.
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
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633490"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Używanie etykiet do instrumencie zapytań w puli Synapse SQL

W tym artykule znajdują się wskazówki dotyczące opracowywania rozwiązań przy użyciu etykiet do instrumencie zapytań w puli SQL.

Porady dotyczące używania etykiet do instrumencie zapytań w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.

## <a name="what-are-labels"></a>Co to są etykiety?

Pula SQL obsługuje koncepcję o nazwie etykiety zapytań. Przed wejściem w jakąkolwiek głębię przyjrzyjmy się przykładowi:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Ostatni wiersz oznacza ciąg "Moja etykieta zapytania" do kwerendy. Ten tag jest przydatne, ponieważ etykieta jest w stanie zapytania za pośrednictwem DMVs.

Wykonywanie zapytań o etykiety zapewnia mechanizm lokalizowania zapytań o problemy i pomaga zidentyfikować postęp za pośrednictwem uruchomienia ELT.

Dobra konwencja nazewnictwa naprawdę pomaga. Na przykład rozpoczęcie etykiety z PROJEKTU, PROCEDURY, INSTRUKCJI lub KOMENTARZ jednoznacznie identyfikuje kwerendę wśród całego kodu w formancie źródłowym.

Następująca kwerenda używa dynamicznego widoku zarządzania do wyszukiwania według etykiety:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Istotne jest, aby umieścić nawiasy kwadratowe lub cudzysłowy wokół etykiety słowa podczas wykonywania zapytań. Etykieta jest słowem zastrzeżonym i powoduje błąd, gdy nie jest rozdzielany.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).
