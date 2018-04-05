---
title: Ładowanie danych do usługi SQL Data Warehouse przy użyciu programu bcp | Microsoft Docs
description: Dowiedz się, czym jest program bcp i jak z niego korzystać w scenariuszach dotyczących magazynów danych.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: ''
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 752a2f0d7b49f9e053788b26e3df389246c72a73
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="load-data-with-bcp"></a>Ładowanie danych za pomocą narzędzia bcp

**[bcp](/sql/tools/bcp-utility.md)** to narzędzie wiersza polecenia do ładowania zbiorczego, które umożliwia kopiowanie danych między programem SQL Server, plikami danych i usługą SQL Data Warehouse. Narzędzie bcp służy do importowania dużej liczby wierszy do tabel usługi SQL Data Warehouse lub do eksportowania danych z tabel programu SQL Server do plików danych. Z wyjątkiem przypadków obejmujących użycie opcji queryout program bcp nie wymaga żadnej znajomości języka Transact-SQL.

Za pomocą programu bcp można szybko i łatwo przenosić mniejsze zestawy danych do i z bazy danych usługi SQL Data Warehouse. Dokładne zalecenia dotyczące ilości danych do ładowania/wyodrębniania za pomocą narzędzia bcp zależą od połączenia sieciowego z platformą Azure.  Niewielkie tabele wymiarów można łatwo załadować i wyodrębnić za pomocą narzędzia bcp. Jednak w przypadku ładowania i wyodrębniania dużych ilości danych zalecane jest narzędzie Polybase, a nie bcp. Narzędzie PolyBase zostało zaprojektowane do obsługi architektury masowego przetwarzania równoległego w usłudze SQL Data Warehouse.

Narzędzie bcp umożliwia:

* Ładowanie danych do usługi SQL Data Warehouse przy użyciu narzędzia wiersza polecenia.
* Wyodrębnianie danych z usługi SQL Data Warehouse przy użyciu narzędzia wiersza polecenia.

Ten samouczek umożliwia opanowanie następujących czynności:

* Importowanie danych do tabeli za pomocą polecenia bcp in
* Eksportowanie danych z tabeli za pomocą polecenia bcp out

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych w tym samouczku potrzebne są:

* Baza danych usługi SQL Data Warehouse
* Narzędzia wiersza polecenia bcp i sqlcmd. Można je pobrać w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Dane w formacie ASCII lub UTF-16
Jeśli próbujesz wykonać kroki tego samouczka z użyciem własnych danych, musisz zastosować do danych kodowanie ASCII lub UTF-16, ponieważ narzędzie bcp nie obsługuje formatu UTF-8. 

Aparat PolyBase obsługuje format UTF-8, ale nie obsługuje jeszcze formatu UTF-16. Aby eksportować dane za pomocą narzędzia bcp i ładować dane za pomocą narzędzia PolyBase, konieczna będzie konwersja danych do formatu UTF-8 po ich wyeksportowaniu z programu SQL Server. 

## <a name="import-data-into-sql-data-warehouse"></a>Importowanie danych do usługi SQL Data Warehouse
W tym samouczku utworzysz tabelę w usłudze Azure SQL Data Warehouse i zaimportujesz dane do tej tabeli.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Krok 1: tworzenie tabeli w usłudze Azure SQL Data Warehouse
W wierszu polecenia użyj polecenia sqlcmd, aby uruchomić następujące zapytanie w celu utworzenia tabeli w wystąpieniu:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

Aby uzyskać więcej informacji dotyczących tworzenia tabel, zapoznaj się z artykułem [Omówienie tabel](sql-data-warehouse-tables-overview.md) lub składnią polecenia [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

### <a name="step-2-create-a-source-data-file"></a>Krok 2: tworzenie źródłowego pliku danych
Otwórz program Notatnik i skopiuj następujące wiersze danych do nowego pliku tekstowego, a następnie zapisz ten plik w lokalnym katalogu tymczasowym: C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Należy pamiętać, że program bcp.exe nie obsługuje kodowania pliku w formacie UTF-8. Podczas korzystania z programu bcp.exe używaj plików kodowanych w formacie ASCII lub UTF-16.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Krok 3: nawiązywanie połączenia i importowanie danych
Przy użyciu narzędzia bcp można nawiązać połączenie i zaimportować dane, stosując następujące polecenie z odpowiednio zastąpionymi wartościami:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Poprawność załadowania danych można sprawdzić za pomocą następującego zapytania uruchomionego przy użyciu narzędzia sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Zapytanie powinno zwrócić następujące wyniki:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Krok 4: tworzenie statystyk na podstawie nowo załadowanych danych
Po załadowaniu danych ostatnim krokiem jest utworzenie lub zaktualizowanie statystyk. Pomaga to poprawić wydajność zapytań. Aby uzyskać więcej informacji, zobacz [Statystyka](sql-data-warehouse-tables-statistics.md). W poniższym przykładzie narzędzie sqlcmd powoduje utworzenie statystyki dotyczącej tabeli, która zawiera nowo załadowane dane.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Eksportowanie danych z usługi SQL Data Warehouse
Ten samouczek przedstawia sposób tworzenia pliku danych z tabeli w usłudze SQL Data Warehouse. Polega to między innymi na wyeksportowaniu danych, które zostały zaimportowane w poprzedniej sekcji. Wyniki znajdują się w pliku o nazwie DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Krok 1: eksportowanie danych
Przy użyciu narzędzia bcp można nawiązać połączenie i wyeksportować dane za pomocą następującego polecenia z odpowiednio zastąpionymi wartościami:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Możesz sprawdzić, czy dane zostały poprawnie wyeksportowane, otwierając nowy plik. Dane w pliku powinny pasować do poniższego tekstu:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Ze względu na specyfikę systemów rozproszonych kolejność danych w bazach danych usługi SQL Data Warehouse może się różnić. Innym rozwiązaniem jest użycie funkcji **queryout** programu bcp, aby napisać zapytanie wyodrębniające, zamiast eksportowania całej tabeli.
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby zaprojektować proces ładowania, zobacz [najlepsze rozwiązania dotyczące ładowania danych do usługi Azure SQL Data Warehouse](guidance-for-loading-data.md).  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
