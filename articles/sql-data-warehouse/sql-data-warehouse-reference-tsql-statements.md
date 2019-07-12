---
title: Instrukcje języka T-SQL — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zawiera łącza do dokumentacji dla instrukcji języka T-SQL obsługiwanych w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: af8aa85488240dbbe4a07811d73ebdbaafefcfef
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588612"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Instrukcje T-SQL obsługiwanych w usłudze Azure SQL Data Warehouse
Zawiera łącza do dokumentacji dla instrukcji języka T-SQL obsługiwanych w usłudze Azure SQL Data Warehouse.

## <a name="data-definition-language-ddl-statements"></a>Instrukcje języka DDL (Definition) danych
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [POLECENIA ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [ALTER MATERIALIZED VIEW](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza) 
* [ZMIENIĆ PROCEDURY](https://msdn.microsoft.com/library/ms189762.aspx)
* [ZMIANY SCHEMATU](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [UTWÓRZ INDEKS MAGAZYNU KOLUMN](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [TWORZENIE BAZY DANYCH O OKREŚLONYM ZAKRESIE POŚWIADCZEŃ](https://msdn.microsoft.com/library/mt270260.aspx)
* [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [TWORZENIE ZEWNĘTRZNEJ TABELI](https://msdn.microsoft.com/library/dn935021.aspx)
* [TWORZENIE FUNKCJI](https://msdn.microsoft.com/library/mt203952.aspx)
* [TWORZENIE INDEKSU](https://msdn.microsoft.com/library/ms188783.aspx)
* [Utwórz MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza) 
* [TWORZENIE PROCEDURY](https://msdn.microsoft.com/library/ms187926.aspx)
* [UTWÓRZ SCHEMAT](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [UTWÓRZ WIDOK](https://msdn.microsoft.com/library/ms187956.aspx)
* [TWORZENIE KLASYFIKATORA OBCIĄŻENIA](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [USUNĄĆ ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/mt146367.aspx)
* [UPUŚĆ FORMATU PLIKU ZEWNĘTRZNEGO](https://msdn.microsoft.com/library/mt146379.aspx)
* [USUNIĘCIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/mt130698.aspx)
* [USUŃ INDEKS](https://msdn.microsoft.com/library/ms176118.aspx)
* [PROCEDURY](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISTICS](https://msdn.microsoft.com/library/ms175075.aspx)
* [USUNIĘCIE TABELI](https://msdn.microsoft.com/library/ms173790.aspx)
* [PORZUĆ SCHEMAT](https://msdn.microsoft.com/library/ms186751.aspx)
* [WIDOK LISTY](https://msdn.microsoft.com/library/ms173492.aspx)
* [UPUŚĆ KLASYFIKATORA OBCIĄŻENIA](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [RENAME](https://msdn.microsoft.com/library/mt631611.aspx)
* [ZESTAW RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Instrukcje języka DML (Manipulation) danych
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Poleceń konsoli bazy danych
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [Polecenie DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza)
* [POLECENIE DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [POLECENIE DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [Polecenie DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (wersja zapoznawcza)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Instrukcji kwerendy
* [WYBIERZ POZYCJĘ](https://msdn.microsoft.com/library/ms189499.aspx)
* [Za pomocą common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [Z wyjątkiem i INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [EXPLAIN](https://msdn.microsoft.com/library/mt631615.aspx)
* [Z](https://msdn.microsoft.com/library/ms177634.aspx)
* [Za pomocą PIVOT i UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GRUPUJ WEDŁUG](https://msdn.microsoft.com/library/ms177673.aspx)
* [POSIADANIE](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNIA](https://msdn.microsoft.com/library/ms180026.aspx)
* [GDZIE](https://msdn.microsoft.com/library/ms188047.aspx)
* [DO GÓRY](https://msdn.microsoft.com/library/ms189463.aspx)
* [Tworzenie aliasów](https://msdn.microsoft.com/library/mt631614.aspx)
* [Warunek wyszukiwania](https://msdn.microsoft.com/library/ms173545.aspx)
* [Zapytania podrzędne](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Instrukcje zabezpieczeń
* Uprawnienia: [UDZIEL](https://msdn.microsoft.com/library/ms187965.aspx), [ODMÓW](https://msdn.microsoft.com/library/ms188338.aspx), [ODWOŁANIA](https://msdn.microsoft.com/library/ms187728.aspx)
* [INSTRUKCJA ALTER AUTORYZACJI](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTER CERTIFICATE](https://msdn.microsoft.com/library/ms189511.aspx)
* [INSTRUKCJA ALTER DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [INSTRUKCJA ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [INSTRUKCJA ALTER KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms186937.aspx)
* [INSTRUKCJA ALTER ROLI](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* [WYKONAJ KOPIĘ ZAPASOWĄ CERTYFIKATU](https://msdn.microsoft.com/library/ms178578.aspx)
* [ZAMKNIJ KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms188387.aspx)
* [TWORZENIE CERTYFIKATU](https://msdn.microsoft.com/library/ms187798.aspx)
* [CREATE DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb677241.aspx)
* [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)
* [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
* [TWORZENIE ROLI](https://msdn.microsoft.com/library/ms187936.aspx)
* [TWORZENIE UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms173463.aspx)
* [UPUŚĆ CERTYFIKATU](https://msdn.microsoft.com/library/ms179906.aspx)
* [DROP DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [UPUŚĆ KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms180071.aspx)
* [BEZPOŚREDNIEJ ROLI](https://msdn.microsoft.com/library/ms174988.aspx)
* [USUNĄĆ UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms189438.aspx)
* [OTWÓRZ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacje, zobacz [elementy języka T-SQL w usłudze Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-language-elements.md), i [widoki systemowe w usłudze Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-system-views.md).
