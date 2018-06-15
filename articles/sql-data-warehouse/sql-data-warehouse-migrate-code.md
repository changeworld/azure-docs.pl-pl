---
title: Migrowanie kodu SQL do SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migrowania kodu SQL Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: b17e8e306c01bef4c58658b35f3a67d0e721633c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527457"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrowanie kodu SQL do SQL Data Warehouse
W tym artykule opisano zmiany kodu, które mają być podczas migracji kodu z innej bazy danych do usługi SQL Data Warehouse. Niektóre funkcje usługi SQL Data Warehouse może znacznie poprawić wydajność, ponieważ są one przeznaczone do pracy w sposób rozproszonych. Jednak aby zachować wydajność i skalę, niektóre funkcje są również dostępne.

## <a name="common-t-sql-limitations"></a>Typowych ograniczeń T-SQL
Poniższa lista zawiera podsumowanie najczęściej używane funkcje, których nie obsługuje usługi SQL Data Warehouse. Łącza prowadzą do obejścia nieobsługiwane funkcje:

* [Sprzężenia ANSI na aktualizacje][ANSI joins on updates]
* [Sprzężenia ANSI na usuwaniu][ANSI joins on deletes]
* [Instrukcja merge][merge statement]
* sprzężenia między bazami danych
* [Kursory][cursors]
* [INSERT... EXEC][INSERT..EXEC]
* klauzuli OUTPUT
* wbudowane funkcje zdefiniowane przez użytkownika
* wiele instrukcji funkcji
* [wspólnych wyrażeniach tabel](#Common-table-expressions)
* [cykliczne wspólnych wyrażeniach tabel (CTE)] (#Recursive-common-table-expressions-(CTE)
* Funkcje środowiska CLR i procedury
* Funkcja $partition
* zmiennych tabel
* Tabela wartości parametrów
* Transakcje rozproszone
* Commit / rollback Praca
* Zapisz transakcji
* Kontekst wykonywania (EXECUTE AS)
* [Grupuj według klauzuli z pakietem zbiorczym / modułu / Ustawia opcje grupowania][group by clause with rollup / cube / grouping sets options]
* [poziomów zagnieżdżenia niż 8][nesting levels beyond 8]
* [aktualizowanie za pośrednictwem widoków][updating through views]
* [Użycie wybierz przypisanie zmiennej][use of select for variable assignment]
* [Typ danych nie MAX dynamiczne ciągów SQL][no MAX data type for dynamic SQL strings]

Na szczęście wokół można pracować większość tych ograniczeń. Wyjaśnienia dotyczące znajdują się w artykułów odpowiednich programowanie wymienionych powyżej.

## <a name="supported-cte-features"></a>Obsługiwane funkcje CTE
Wspólnych wyrażeniach tabel (wyrażeń CTE) są obsługiwane częściowo w usłudze SQL Data Warehouse.  Następujące funkcje CTE są obecnie obsługiwane:

* CTE można określić w instrukcji SELECT.
* CTE można określić w instrukcji CREATE VIEW.
* CTE można określić w instrukcji tworzenia tabeli jako wybierz (CTAS).
* CTE można określić w instrukcji tworzenia zdalnego tabeli jako wybierz (CRTAS).
* CTE można określić w instrukcji tworzenia zewnętrznego tabeli jako wybierz (CETAS).
* Tabela zdalna mogą być przywoływane z CTE.
* Tabeli zewnętrznej można odwoływać się z CTE.
* Wiele definicji zapytania CTE można zdefiniować w CTE.

## <a name="cte-limitations"></a>Ograniczenia CTE
Wspólnego wyrażenia tabeli mają następujące ograniczenia z magazynu danych SQL, w tym:

* CTE musi następować jednej instrukcji SELECT. INSERT, UPDATE, DELETE i instrukcjach MERGE nie są obsługiwane.
* Wspólne wyrażenie tabeli, która zawiera odwołania do samego siebie (cykliczne wspólne wyrażenie tabeli) nie jest obsługiwany (patrz poniżej).
* Określanie więcej niż jeden z klauzulą w CTE jest niedozwolone. Na przykład jeśli CTE_query_definition zawiera podzapytanie, że podzapytania nie może zawierać zagnieżdżoną z klauzulą definiujący CTE innego.
* Nie można użyć klauzuli ORDER BY w CTE_query_definition, z wyjątkiem, gdy określono klauzulę TOP.
* CTE jest używana w instrukcji, która jest częścią partii, instrukcję przed jego musi następować średnikiem.
* Gdy jest używany w instrukcjach przygotowane przez sp_prepare, wspólnych wyrażeń tabel działają w taki sam sposób jak inne instrukcji SELECT w PDW. Jednak jeśli wspólnych wyrażeń tabel są używane jako część CETAS przygotowane przez sp_prepare, zachowanie może odroczyć z programu SQL Server i inne instrukcje PDW ze względu na sposób powiązania jest zaimplementowany dla sp_prepare. Wybierz OPCJĘ odwołania CTE wykorzystanie niewłaściwej kolumnie, która nie istnieje w CTE sp_prepare przechodzą bez wykrycia błędu, ale błąd zostanie zgłoszony podczas procedury składowanej sp_execute zamiast tego.

## <a name="recursive-ctes"></a>Cyklicznych wspólnych wyrażeń tabel
Cyklicznych wspólnych wyrażeń tabel nie są obsługiwane w usłudze SQL Data Warehouse.  Migracja cykliczne CTE mogą być nieco złożone i proces najlepiej jest aby podzielić go na wiele kroków. Zwykle można skorzystać z pętli i wypełniania tabeli tymczasowej jako iteracja tymczasowe zapytania cyklicznego. Po zapełnieniu tabeli tymczasowej należy zwrócić dane jako pojedynczy zestaw wyników. Podejście podobne został użyty do rozwiązania `GROUP BY WITH CUBE` w [Grupuj według klauzuli z pakietem zbiorczym / modułu / Ustawia opcje grupowania] [ group by clause with rollup / cube / grouping sets options] artykułu.

## <a name="unsupported-system-functions"></a>Nieobsługiwany system funkcji
Dostępne są także niektóre funkcje systemu, które nie są obsługiwane. Główne te, które zwykle można znaleźć używane w magazynowania danych, należą:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE() —

Niektóre z tych problemów można pracować wokół.

## <a name="rowcount-workaround"></a>@@ROWCOUNT obejście problemu
Aby obejść Brak obsługi @@ROWCOUNT, Tworzenie procedury przechowywanej, która będzie pobrać liczbę ostatnich wierszy z sys.dm_pdw_request_steps, a następnie wykonaj `EXEC LastRowCount` po instrukcji DML.

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
Aby uzyskać pełną listę wszystkich obsługiwanych instrukcje T-SQL, zobacz [tematy języka Transact-SQL][Transact-SQL topics].

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
