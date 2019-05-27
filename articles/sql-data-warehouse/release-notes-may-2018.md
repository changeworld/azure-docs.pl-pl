---
title: Usługa Azure SQL Data Warehouse — informacje o wersji maja 2018 r. | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: eb933e8085ec956be713a18602b8baff9c87084b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912202"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Maj 2018 r. 
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w maju 2018 r. 

## <a name="gen-2-instances"></a>Wystąpienia generowania 2
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) warstwy usługi Azure SQL Data magazynu obliczenia zoptymalizowane pod kątem Gen2 Ustawia nowe normy wydajności dla magazynowania danych w chmurze. Klienci obecnie uzyskać maksymalnie pięć razy lepszą wydajność zapytań, cztery razy więcej współbieżność i pięć razy większą moc obliczeniową w porównaniu do bieżącej generacji. Teraz może być nawet 128 zapytań jednoczesnych z jednego klastra z najwyższą dowolnej danych magazynowania usługi w chmurze.

Zobacz [Turbocharge analizy chmur przy użyciu usługi Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) ogłoszenia na blogu z Rohana Kumara, wiceprezes, Azure Data.

## <a name="auto-statistics"></a>Statystyka automatyczna
Statystyki są kluczowe do optymalizacji generowania planu zapytania w nowoczesnych optymalizatory opartych na kosztach, takich jak aparat w usłudze SQL Data Warehouse. Gdy wszystkie zapytania są znane z wyprzedzeniem, określanie, które muszą zostać utworzony statystyk obiektów jest osiągalna zadaniem. Jednak gdy system jest muszą stawiać ad hoc i losowe zapytań, co jest typowe dla obciążeń magazynowania danych, Administratorzy systemu mogą mieć trudności z przewidzieć statystyk, które muszą zostać utworzone początkowe do zapytania potencjalnie nieoptymalne wykonywanie planów i dłużej czas odpowiedzi na zapytania. Jednym ze sposobów, aby uniknąć tego problemu jest tworzenie statystyk obiektów dla wszystkich kolumn z tabeli z wyprzedzeniem. Jednak ten proces jest powiązana z kary zgodnie z statystyk obiektów muszą być przechowywane w tabeli proces ładowania, co powoduje dłuższe czasy ładowania.

Usługa SQL Data Warehouse obsługuje teraz automatyczne tworzenie obiektów statystyki, zapewniając większą elastyczność, wydajność i łatwość użycia dla administratorów i deweloperów, przy jednoczesnym zapewnieniu, że system będzie nadal oferują plany wykonywania jakości i najlepsze czasy odpowiedzi.

Aby włączyć lub wyłączyć automatyczne statystyki tworzenia w usłudze SQL Data Warehouse, wykonaj następującą instrukcję:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Jako najlepsze praktyki i wskazówki, firma Microsoft zaleca ustawienie `AUTO_CREATE_STATISTICS` opcję `ON`.

> [!NOTE]
> Statystyki automatycznego tworzenia *domyślnie* dla wszystkich nowych magazynów danych.
>  

Zobacz [opcje ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) artykuł, aby uzyskać więcej informacji.

## <a name="rejected-row-support"></a>Odrzucony wiersz pomocy technicznej
Klienci często używają [PolyBase (tabele zewnętrzne) w celu załadowania danych](design-elt-data-loading.md) do usługi SQL Data Warehouse ze względu na wysoką wydajność, równoległe charakter ładowania danych. Program PolyBase jest domyślny model ładowania podczas ładowania danych przy użyciu [usługi Azure Data Factory](https://azure.com/adf) także. 

Usługa SQL Data Warehouse dodaje możliwość definiowania lokalizacja odrzuconych wierszy, za pośrednictwem `REJECTED_ROW_LOCATION` parametrem [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) instrukcji. Po wykonaniu [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) z tabeli zewnętrznej wszystkie wiersze, których nie można załadować będą przechowywane w pliku w źródle bliższe zbadanie tej sprawy. 

Zobacz [pewnie załadować dane przy użyciu lokalizacji odrzucony wiersz SQL dane magazynu PolyBase](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blog, aby uzyskać szczegółowe informacje na temat zachowania odrzucony wiersz.

Poniższy przykład pokazuje nowe składnia określająca odrzuconych wierszy.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>INSTRUKCJA ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) pozwala użytkownikowi na modyfikowanie wcześniej utworzony widok bez konieczności tworzenia/usuwania widoku i ponownie zastosować uprawnienia. 

Poniższy przykład modyfikuje wcześniej utworzony widok.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
[CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) funkcja zwraca ciąg wynikające z łączenia dwóch lub więcej wartości w sposób end-to-end. Rozdziela wartości łączonych z ogranicznika określone w pierwszym argumencie. `CONCAT_WS` Funkcja jest przydatna do generowania danych wyjściowych Comma-Separated wartość (CSV).

Poniższy przykład pokazuje, złączenie zbiór wartości int za pomocą przecinka.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Instrukcja zwraca następujące wyniki:
```
result
---------
1,2,3
```
Poniższy przykład pokazuje, złączenie zestaw wartości typu mieszanego danych za pomocą przecinka.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Instrukcja zwraca następujące wyniki:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
[Sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) procedura składowana ma zwracać informacje o typach danych obsługiwanych przez bieżącego środowiska systemu. Często jest używany przez narzędzia, łącząc się za pośrednictwem połączenia ODBC w celu badania typu danych.

Poniższy przykład pobiera szczegółowe informacje dla wszystkich typów danych obsługiwanych przez Magazyn danych SQL.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Wybierz INTO za pomocą ORDER BY Zmiana zachowania
Usługa SQL Data Warehouse teraz spowoduje zablokowanie `SELECT INTO` zapytania, które zawierają `ORDER BY` klauzuli. Wcześniej ta operacja powiedzie się przy pierwszym kolejność danych w pamięci, a następnie wstawianie do tabeli docelowej zmiany kolejności danych w celu dostosowania kształt tabeli.

### <a name="previous-behavior"></a>Poprzednie zachowanie
Poniższa instrukcja powiedzie się przy użyciu dodatkowego przetwarzania.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Aktualny efekt
Poniższa instrukcja zgłosi błąd wskazujący `ORDER BY` klauzula nie jest obsługiwana w `SELECT INTO` instrukcji.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Error — instrukcja zwrócone:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>Ustaw PARSEONLY na stan zapytania (Zmiana zachowania)
Za pomocą `SET PARSEONLY ON` składni umożliwia użytkownikowi ma zbadać składni każdej instrukcji języka T-SQL i zwraca komunikaty o błędach bez konieczności kompilowania lub wykonując instrukcję aparat SQL Data Warehouse. Wcześniej w [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) widoku systemu stanu w ramach tych instrukcji pozostać w `Running` stanu. `sys.dm_pdw_exec_requests` Widoku teraz zwróci stan jako `Complete`.

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Forum Stack Overflow]
* [Twitter]


[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
