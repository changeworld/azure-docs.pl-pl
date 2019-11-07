---
title: Instrukcje języka T-SQL
description: Linki do dokumentacji dotyczącej instrukcji języka T-SQL obsługiwanych w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 861675941693697a20da189edebe33d91918782c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685519"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Instrukcje języka T-SQL obsługiwane w Azure SQL Data Warehouse
Linki do dokumentacji dotyczącej instrukcji języka T-SQL obsługiwanych w Azure SQL Data Warehouse.

## <a name="data-definition-language-ddl-statements"></a>Instrukcje języka definicji danych (DDL)
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ZMIEŃ INDEKS](https://msdn.microsoft.com/library/ms188388.aspx)
* [Zmień widok z materiałami](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza) 
* [ALTER — PROCEDURA](https://msdn.microsoft.com/library/ms189762.aspx)
* [ZMIEŃ SCHEMAT](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [UTWÓRZ INDEKS MAGAZYNU KOLUMN](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [UTWÓRZ POŚWIADCZENIA W ZAKRESIE BAZY DANYCH](https://msdn.microsoft.com/library/mt270260.aspx)
* [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [TWORZENIE TABELI ZEWNĘTRZNEJ](https://msdn.microsoft.com/library/dn935021.aspx)
* [UTWÓRZ FUNKCJĘ](https://msdn.microsoft.com/library/mt203952.aspx)
* [UTWÓRZ INDEKS](https://msdn.microsoft.com/library/ms188783.aspx)
* [Utwórz widok z materiałami jako wybrany](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza) 
* [UTWÓRZ PROCEDURĘ](https://msdn.microsoft.com/library/ms187926.aspx)
* [UTWÓRZ SCHEMAT](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [UTWÓRZ WIDOK](https://msdn.microsoft.com/library/ms187956.aspx)
* [UTWÓRZ KLASYFIKATOR OBCIĄŻEŃ](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [UPUŚĆ ZEWNĘTRZNE ŹRÓDŁO DANYCH](https://msdn.microsoft.com/library/mt146367.aspx)
* [PORZUĆ FORMAT PLIKU ZEWNĘTRZNEGO](https://msdn.microsoft.com/library/mt146379.aspx)
* [UPUŚĆ TABELĘ ZEWNĘTRZNĄ](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [PROCEDURA DROP](https://msdn.microsoft.com/library/ms174969.aspx)
* [USUWANIE STATYSTYK](https://msdn.microsoft.com/library/ms175075.aspx)
* [USUŃ TABELĘ](https://msdn.microsoft.com/library/ms173790.aspx)
* [UPUŚĆ SCHEMAT](https://msdn.microsoft.com/library/ms186751.aspx)
* [UPUŚĆ WIDOK](https://msdn.microsoft.com/library/ms173492.aspx)
* [PORZUĆ KLASYFIKATOR OBCIĄŻEŃ](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [ZMIEŃNAZWĘ](https://msdn.microsoft.com/library/mt631611.aspx)
* [USTAW RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Instrukcje języka operowania danymi (DML)
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [AKTUALIZACJI](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Polecenia konsoli bazy danych
* [POLECENIE DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza)
* [POLECENIE DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [POLECENIE DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [POLECENIE DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (wersja zapoznawcza)
* [POLECENIE DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [POLECENIE DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [POLECENIE DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Instrukcje zapytania
* [ZAZNACZENIA](https://msdn.microsoft.com/library/ms189499.aspx)
* [Z common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [POZA i INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [UZASADNIENI](https://msdn.microsoft.com/library/mt631615.aspx)
* [WNIOSEK](https://msdn.microsoft.com/library/ms177634.aspx)
* [Korzystanie z tabeli przestawnej i operatora UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GRUPUJ WEDŁUG](https://msdn.microsoft.com/library/ms177673.aspx)
* [DYSPONUJĄC](https://msdn.microsoft.com/library/ms180199.aspx)
* [PORZĄDKUJ WEDŁUG](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [Unii](https://msdn.microsoft.com/library/ms180026.aspx)
* [MIEJSCU](https://msdn.microsoft.com/library/ms188047.aspx)
* [Do góry](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasowanie](https://msdn.microsoft.com/library/mt631614.aspx)
* [Warunek wyszukiwania](https://msdn.microsoft.com/library/ms173545.aspx)
* [Podzapytania](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Instrukcje zabezpieczeń
* Uprawnienia: [Udziel](https://msdn.microsoft.com/library/ms187965.aspx), [Odmów](https://msdn.microsoft.com/library/ms188338.aspx), [odwołaj](https://msdn.microsoft.com/library/ms187728.aspx)
* [ZMIEŃ AUTORYZACJĘ](https://msdn.microsoft.com/library/ms187359.aspx)
* [ZMIEŃ CERTYFIKAT](https://msdn.microsoft.com/library/ms189511.aspx)
* [ZMIEŃ KLUCZ SZYFROWANIA BAZY DANYCH](https://msdn.microsoft.com/library/bb630389.aspx)
* [ZMIEŃ NAZWĘ LOGOWANIA](https://msdn.microsoft.com/library/ms189828.aspx)
* [ZMIEŃ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms186937.aspx)
* [ZMIEŃ ROLĘ](https://msdn.microsoft.com/library/ms189775.aspx)
* [ZMIEŃ UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms176060.aspx)
* [CERTYFIKAT KOPII ZAPASOWEJ](https://msdn.microsoft.com/library/ms178578.aspx)
* [ZAMKNIJ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms188387.aspx)
* [UTWÓRZ CERTYFIKAT](https://msdn.microsoft.com/library/ms187798.aspx)
* [UTWÓRZ KLUCZ SZYFROWANIA BAZY DANYCH](https://msdn.microsoft.com/library/bb677241.aspx)
* [UTWÓRZ NAZWĘ LOGOWANIA](https://msdn.microsoft.com/library/ms189751.aspx)
* [UTWÓRZ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms174382.aspx)
* [UTWÓRZ ROLĘ](https://msdn.microsoft.com/library/ms187936.aspx)
* [UTWÓRZ UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms173463.aspx)
* [USUŃ CERTYFIKAT](https://msdn.microsoft.com/library/ms179906.aspx)
* [USUŃ KLUCZ SZYFROWANIA BAZY DANYCH](https://msdn.microsoft.com/library/bb630256.aspx)
* [PORZUĆ DANE LOGOWANIA](https://msdn.microsoft.com/library/ms188012.aspx)
* [PORZUĆ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms180071.aspx)
* [USUŃ ROLĘ](https://msdn.microsoft.com/library/ms174988.aspx)
* [UPUŚĆ UŻYTKOWNIKA](https://msdn.microsoft.com/library/ms189438.aspx)
* [OTWÓRZ KLUCZ GŁÓWNY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [elementy języka T-SQL w Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-language-elements.md)i [widoki systemowe w programie Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-system-views.md).
