---
title: Instrukcje języka T-SQL
description: Łącza do dokumentacji instrukcji T-SQL obsługiwanych w usłudze SQL Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 53609049f69f0a3d50430ef512a5bf4a8ffd6d82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351350"
---
# <a name="t-sql-statements-supported-in-sql-analytics"></a>Instrukcje T-SQL obsługiwane w usłudze SQL Analytics
Łącza do dokumentacji instrukcji T-SQL obsługiwanych w usłudze SQL Analytics.

## <a name="data-definition-language-ddl-statements"></a>Instrukcje języka DDL (Data Definition Language)
* [ZMIEŃ BAZĘ DANYCH](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [ALTER MATERIALIZED VIEW](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (Podgląd) 
* [ZMIEŃ PROCEDURĘ](https://msdn.microsoft.com/library/ms189762.aspx)
* [ZMIEŃ SCHEMAT](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [TWORZENIE INDEKSU MAGAZYNU KOLUMN](https://msdn.microsoft.com/library/gg492153.aspx)
* [TWORZENIE BAZY DANYCH](https://msdn.microsoft.com/library/mt204021.aspx)
* [TWORZENIE POŚWIADCZEŃ O ZAKRESIE BAZY DANYCH](https://msdn.microsoft.com/library/mt270260.aspx)
* [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [TWORZENIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx)
* [TWORZENIE FUNKCJI](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [TWORZENIE ZMATERIALIZOWANEGO WIDOKU JAKO WYBIERZ](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (Podgląd) 
* [PROCEDURA TWORZENIA](https://msdn.microsoft.com/library/ms187926.aspx)
* [TWORZENIE SCHEMATU](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [UTWÓRZ TABELĘ](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [TWORZENIE WIDOKU](https://msdn.microsoft.com/library/ms187956.aspx)
* [TWORZENIE KLASYFIKATORA OBCIĄŻENIA](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [UPUŚĆ ZEWNĘTRZNE ŹRÓDŁO DANYCH](https://msdn.microsoft.com/library/mt146367.aspx)
* [UPUŚĆ FORMAT PLIKU ZEWNĘTRZNEGO](https://msdn.microsoft.com/library/mt146379.aspx)
* [UPUŚĆ TABELĘ ZEWNĘTRZNĄ](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [PROCEDURA UPUSZCZANIA](https://msdn.microsoft.com/library/ms174969.aspx)
* [STATYSTYKI UPUSZCZENIA](https://msdn.microsoft.com/library/ms175075.aspx)
* [TABELA UPUSZCZANIA](https://msdn.microsoft.com/library/ms173790.aspx)
* [SCHEMAT UPUSZCZANIA](https://msdn.microsoft.com/library/ms186751.aspx)
* [WIDOK UPUSZCZANIA](https://msdn.microsoft.com/library/ms173492.aspx)
* [KLASYFIKATOR OBCIĄŻENIA UPUSZCZANIA](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [Zmienić nazwę](https://msdn.microsoft.com/library/mt631611.aspx)
* [ZESTAW RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [OBCINANIE TABELI](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Instrukcje języka DML (Data Manipulation Language)
* [Usunąć](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [Aktualizacji](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Polecenia konsoli bazy danych
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (Wersja zapoznawcza)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (Wersja zapoznawcza)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Instrukcje kwerend
* [Wybierz](https://msdn.microsoft.com/library/ms189499.aspx)
* [Z common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [Z WYJĄTKIEM I KRZYŻOWANIA](https://msdn.microsoft.com/library/ms188055.aspx)
* [Wyjaśnić](https://msdn.microsoft.com/library/mt631615.aspx)
* [Z](https://msdn.microsoft.com/library/ms177634.aspx)
* [Korzystanie z funkcji PIVOT i UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROUP BY](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [Unii](https://msdn.microsoft.com/library/ms180026.aspx)
* [Gdzie](https://msdn.microsoft.com/library/ms188047.aspx)
* [Do góry](https://msdn.microsoft.com/library/ms189463.aspx)
* [Tworzenie aliasów](https://msdn.microsoft.com/library/mt631614.aspx)
* [Warunek wyszukiwania](https://msdn.microsoft.com/library/ms173545.aspx)
* [Podkwerend](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Instrukcje zabezpieczeń
* Uprawnienia: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ZMIEŃ AUTORYZACJĘ](https://msdn.microsoft.com/library/ms187359.aspx)
* [ZMIEŃ CERTYFIKAT](https://msdn.microsoft.com/library/ms189511.aspx)
* [ZMIENIANIE KLUCZA SZYFROWANIA BAZY DANYCH](https://msdn.microsoft.com/library/bb630389.aspx)
* [ZMIEŃ LOGOWANIE](https://msdn.microsoft.com/library/ms189828.aspx)
* [ZMIEŃ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms186937.aspx)
* [ZMIEŃ ROLĘ](https://msdn.microsoft.com/library/ms189775.aspx)
* [ZMIEŃ UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms176060.aspx)
* [CERTYFIKAT KOPII ZAPASOWEJ](https://msdn.microsoft.com/library/ms178578.aspx)
* [ZAMKNIJ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms188387.aspx)
* [TWORZENIE CERTYFIKATU](https://msdn.microsoft.com/library/ms187798.aspx)
* [TWORZENIE KLUCZA SZYFROWANIA BAZY DANYCH](https://msdn.microsoft.com/library/bb677241.aspx)
* [TWORZENIE LOGOWANIA](https://msdn.microsoft.com/library/ms189751.aspx)
* [TWORZENIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms174382.aspx)
* [TWORZENIE ROLI](https://msdn.microsoft.com/library/ms187936.aspx)
* [TWORZENIE UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms173463.aspx)
* [CERTYFIKAT UPUSZCZANIA](https://msdn.microsoft.com/library/ms179906.aspx)
* [KLUCZ SZYFROWANIA BAZY DANYCH UPUSZCZANIA](https://msdn.microsoft.com/library/bb630256.aspx)
* [UPUŚCIĆ LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [UPUSZCZANIE KLUCZA GŁÓWNEGO](https://msdn.microsoft.com/library/ms180071.aspx)
* [ROLA UPUSZCZANIA](https://msdn.microsoft.com/library/ms174988.aspx)
* [UPUŚĆ UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms189438.aspx)
* [OTWÓRZ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [Elementy języka T-SQL w usłudze SQL Analytics](sql-data-warehouse-reference-tsql-language-elements.md)i Widoki [systemowe w usłudze SQL Analytics](sql-data-warehouse-reference-tsql-system-views.md).
