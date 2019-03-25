---
title: Migrowanie kodu SQL w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migracji kodu SQL do usługi Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fae3ae16ee0100ad446c0b6c7851553a3376bb4f
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400977"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrowanie kodu SQL w usłudze SQL Data Warehouse

W tym artykule opisano zmiany kodu, które mają być upewnij się, migrując kodu z innej bazy danych SQL Data Warehouse. Niektóre funkcje SQL Data Warehouse może znacznie poprawić wydajność, ponieważ są one przeznaczone do pracy w sposób rozproszonych. Jednak aby zachować wydajność i skalę, niektóre funkcje są również dostępne.

## <a name="common-t-sql-limitations"></a>Typowe ograniczenia języka T-SQL

Poniższa lista zawiera podsumowanie najbardziej typowych funkcji, które nie obsługuje usługi SQL Data Warehouse. Linki prowadzą do obejścia nieobsługiwane funkcje:

* [Sprzężenia ANSI w aktualizacji][ANSI joins on updates]
* [Sprzężenia ANSI w usuwaniu][ANSI joins on deletes]
* [Instrukcja merge][merge statement]
* sprzężeń między bazami danych
* [Kursory][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* klauzuli OUTPUT
* wbudowane funkcje zdefiniowane przez użytkownika
* wieloinstrukcyjnych funkcji
* wspólnych wyrażeń tabel
* [cyklicznych wspólnych wyrażeń tabel (CTE)] (#Recursive-common-table-expressions-(CTE)
* Funkcje środowiska CLR i procedury
* $partition — funkcja
* zmienne tabeli
* Tabela wartości parametrów
* Transakcje rozproszone
* Commit / rollback pracy
* Zapisz transakcji
* kontekstami wykonywania (EXECUTE AS)
* [Grupuj według klauzuli z pakietem zbiorczym / modułu / Ustawia opcje grupowania][group by clause with rollup / cube / grouping sets options]
* [poziomów zagnieżdżenia poza 8][nesting levels beyond 8]
* [aktualizowanie przy użyciu widoków][updating through views]
* [Typ danych nie MAX dynamiczne ciągów SQL][no MAX data type for dynamic SQL strings]

Na szczęście większość z tych ograniczeń możesz pracować nad wokół. Wyjaśnienie znajdują się w artykułach odpowiednich zmianach wyżej.

## <a name="supported-cte-features"></a>Obsługiwane funkcje CTE

Wspólnych wyrażeń tabel (wyrażeń CTE) są obsługiwane częściowo w usłudze SQL Data Warehouse.  Następujące funkcje CTE są obecnie obsługiwane:

* CTE można określić w instrukcji SELECT.
* CTE można określić w instrukcji CREATE VIEW.
* CTE można określić w instrukcji CREATE TABLE AS SELECT (CTAS).
* CTE można określić w instrukcji tworzenia zdalnej tabeli AS wybierz (CRTAS).
* CTE można określić w instrukcji tworzenie zewnętrznej tabeli AS wybierz (CETAS).
* Tabela zdalna mogą być przywoływane z CTE.
* Tabela zewnętrzna mogą być przywoływane z CTE.
* Wiele definicji zapytania CTE można zdefiniować w CTE.

## <a name="cte-limitations"></a>Ograniczenia CTE

Wspólnych wyrażeń tabeli mają pewne ograniczenia, w tym usługa SQL Data Warehouse:

* CTE musi następować pojedynczą instrukcję SELECT. INSERT, UPDATE, DELETE i MERGE instrukcje nie są obsługiwane.
* Wspólne wyrażenie tabeli, który zawiera odwołania do samego siebie (cyklicznego wspólnego wyrażenia tabeli) nie jest obsługiwany (patrz poniżej).
* Określanie więcej niż jeden z klauzulą w CTE jest niedozwolone. Na przykład, jeśli CTE_query_definition zawiera podzapytanie, tym podzapytanie nie może zawierać zagnieżdżoną przy użyciu klauzuli, która definiuje CTE innego.
* Nie można użyć klauzuli ORDER BY w CTE_query_definition, z wyjątkiem sytuacji, gdy określono klauzulę TOP.
* Stosowania CTE w instrukcji, która jest częścią partii instrukcji przed nią musi następować średnikiem.
* W przypadku użycia w instrukcjach przygotowane przez sp_prepare, wyrażeń CTE działają w taki sam sposób jak inne instrukcji SELECT w PDW. Jednak jeśli wyrażeń CTE są używane jako część CETAS przygotowane przez sp_prepare, zachowanie może odroczyć z programu SQL Server i inne instrukcje PDW ze względu na sposób, w których wiązanie jest implementowana sp_prepare. Jeśli WYBIERZESZ że odwołania CTE korzysta z niewłaściwej kolumnie, który nie istnieje w CTE sp_prepare przekazują bez wykrycia błędu, ale zostanie zwrócony błąd podczas sp_execute zamiast tego.

## <a name="recursive-ctes"></a>Wyrażeń CTE cykliczne

Wyrażeń CTE cykliczne nie są obsługiwane w usłudze SQL Data Warehouse.  Migracja cyklicznego CTE może być dość złożony i najlepsze proces jest podzielenie go na wiele kroków. Zazwyczaj można użyć pętli i wypełnić tabelę tymczasową, jak iteracyjne tymczasowe kwerendy cykliczne. Po zapełnieniu tabeli tymczasowej można zwrócić dane jako pojedynczy zestaw wyników. Podejście podobne został użyty do rozwiązania `GROUP BY WITH CUBE` w [Grupuj według klauzuli z pakietem zbiorczym / modułu / Ustawia opcje grupowania] [ group by clause with rollup / cube / grouping sets options] artykułu.

## <a name="unsupported-system-functions"></a>Nieobsługiwany system funkcji

Dostępne są także niektóre funkcje systemu, które nie są obsługiwane. Najważniejsze z nich, którą można zwykle znaleźć używanych w magazynie danych, należą:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE() —

Niektóre z tych problemów możesz pracować nad wokół.

## <a name="rowcount-workaround"></a>@@ROWCOUNT obejście

Aby obejść Brak obsługi @@ROWCOUNT, Utwórz procedurę składowaną, która pobrać liczbę ostatnich wierszy z sys.dm_pdw_request_steps, a następnie wykonaj `EXEC LastRowCount` po instrukcji DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełną listę wszystkich obsługiwanych instrukcji języka T-SQL, zobacz [tematy języka Transact-SQL][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
