---
title: Informacje o wersji magazynu danych Azure SQL, może 2018 | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 843621a8f6e08b2b51d4b7abd05d0ae6c3393fe1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726037"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Nowości w systemie Azure SQL Data Warehouse (maj 2018)
Usługa Azure SQL Data Warehouse stale odbiera ulepszenia. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w 2018 maja. 

## <a name="gen-2-instances"></a>Gen 2 wystąpienia
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) warstwy usługi Azure SQL Data magazynu obliczeniowe zoptymalizowanych pod kątem Gen2 Ustawia nowe normy wydajności dla magazynu danych w chmurze. Klienci teraz uzyskać maksymalnie pięć razy lepszą wydajność zapytań, cztery razy więcej współbieżności i pięć razy wyższa mocy obliczeniowej w porównaniu do obecnej generacji. Może być teraz 128 zapytania jednoczesne z jednego klastra najwyższy chmury usługi magazynowania danych.

Zobacz [Turbocharge chmury analizy w usłudze Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) blogu anonsu z danych Azure Rohan Kumar, wiceprezes firmowych,.

## <a name="features"></a>Funkcje

### <a name="auto-statistics"></a>Statystyki automatycznie
Statystyki są krytyczne dla optymalizacji Generowanie planu zapytania w moderl opartych na kosztach optymalizatory takich jak aparatu w usłudze SQL Data Warehouse. Gdy wszystkie zapytania są znane z wyprzedzeniem, określania, jakie obiekty statystyki muszą zostać utworzone jest osiągalne zadaniem. Jednak system jest wobec ad hoc i losowe kwerendy, co jest typowe dla obciążeń magazynowania danych, Administratorzy systemu mogą mieć trudności z prognozowania co statystyki muszą zostać utworzone prowadzące do planów wykonywania kwerend potencjalnie nieoptymalne i dłuższy czas odpowiedzi na zapytania. Jest jednym ze sposobów rozwiązania problemu z wyprzedzeniem utworzyć obiektów statystyki dla wszystkich kolumn tabeli. Jednak ten proces jest dostarczany z kary jako obiekty statystyki mają zostać zachowane podczas tabeli proces ładowania, co powoduje wydłużenie czasu ładowania.

Magazyn danych SQL obsługuje teraz automatyczne tworzenie obiektów statystyk, zapewniając większą elastyczność, łatwość użycia i wydajności dla administratorów i deweloperów, przy jednoczesnym zapewnieniu, że system będzie kontynuował oferują jakości planów wykonania i najbardziej czas odpowiedzi.

Aby włączyć lub wyłączyć automatyczne statystyk tworzenia w usłudze SQL Data Warehouse, należy wykonać następującą instrukcję:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Jako najlepsze praktyki i wskazówki, firma Microsoft zaleca ustawienie `AUTO_CREATE_STATISTICS` opcji w celu `ON`.

> [!NOTE]
> Statystyka automatycznego tworzenia *domyślnie* dla wszystkich nowych magazynów danych.
>  

Zobacz [opcje ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) artykułu, aby uzyskać dodatkowe szczegóły.

### <a name="rejected-row-support"></a>Odrzucone obsługa wiersza
Klienci często używają [PolyBase (tabel zewnętrznych) można załadować danych](design-elt-data-loading.md) do magazynu danych SQL z powodu wysokiej wydajności, równoległe rodzaj ładowania danych. Program PolyBase jest domyślny model ładowania podczas ładowania danych za pośrednictwem [fabryki danych Azure](http://azure.com/adf) również. 

Usługa SQL Data Warehouse dodaje możliwość zdefiniować odrzucone wiersza lokalizację za pomocą `REJECTED_ROW_LOCATION` parametr o [Tworzenie tabeli zewnętrznej](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) instrukcji. Po wykonaniu [Tworzenie tabeli jako wybierz (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) z tabeli zewnętrznej wszystkie wiersze, które nie można załadować będą przechowywane w pliku blisko źródła dla dalszego postępowania. 

Zobacz [załadować bez obaw o lokalizacji odrzucone wiersza SQL danych magazynu PolyBase](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blogu uzyskać więcej informacji dotyczących zachowania odrzucone wiersza.

Poniższy przykład przedstawia nowej składni służący do określania odrzucone wierszy.

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

### <a name="alter-view"></a>ZMIENIĆ WIDOK
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) pozwala użytkownikowi na modyfikowanie wcześniej utworzony widok bez konieczności tworzenia/usuwania widoku i ponownie zastosuj uprawnienia. 

Poniższy przykład modyfikuje wcześniej utworzony widok.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

### <a name="concatws"></a>CONCAT_WS
[CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) funkcja zwraca ciąg wynikające z łączenia dwóch lub więcej wartości w sposób end-to-end. Umożliwia oddzielenie od wartości połączonych z ogranicznik określony w pierwszym argumencie. `CONCAT_WS` Funkcja jest przydatna podczas generowania danych wyjściowych Comma-Separated wartość (CSV).

W poniższym przykładzie przedstawiono łączenie zestaw wartości int przecinkami.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Instrukcja zwraca następujące wyniki:
```
result
---------
1,2,3
```
W poniższym przykładzie przedstawiono łączenie zestaw mieszane dane typu wartości przecinkami.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Instrukcja zwraca następujące wyniki:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```
### <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
[Sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) systemu procedury składowanej zwraca informacje na temat typy danych obsługiwane w bieżącym środowisku. Zazwyczaj jest używany przez narzędzia łączących się za pośrednictwem połączenia ODBC do badania typu danych.

Poniższy przykład pobiera szczegóły dla wszystkich typów danych obsługiwanych przez usługi SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="behavior-changes"></a>Zmiany sposobu działania
### <a name="select-into-with-order-by"></a>Polecenie SELECT INTO z listą ORDER BY
Usługi SQL Data Warehouse będzie teraz blokować `SELECT INTO` zapytania, które zawierają `ORDER BY` klauzuli. Wcześniej ta operacja powiedzie się przy pierwszym porządkowanie danych w pamięci, a następnie wstawianie do tabeli docelowej, ponowne porządkowanie danych odpowiadające kształtu tabeli.

#### <a name="previous-behavior"></a>Poprzednie działanie
Następująca instrukcja powiedzie się z dodatkowego przetwarzania.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

#### <a name="current-behavior"></a>Aktualny efekt
Następująca instrukcja zgłosi błąd wskazujący `ORDER BY` klauzula nie jest obsługiwana w `SELECT INTO` instrukcji.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Error — instrukcja zwrócił:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

### <a name="set-parseonly-on-query-status"></a>Ustaw PARSEONLY stanu zapytania
Przy użyciu `SET PARSEONLY ON` składni pozwala użytkownikowi aparatu SQL Data Warehouse, Sprawdź składnię każda instrukcja T-SQL i zwracają komunikaty o błędach bez kompilacji lub wykonywania instrukcji. Wcześniej w [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) widoku systemu stanu tych instrukcji pozostanie w `Running` stanu. `sys.dm_pdw_exec_requests` Widoku teraz zwraca stan jako `Complete`.