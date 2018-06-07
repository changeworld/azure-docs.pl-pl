---
title: Powtarzalne kopiowania w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uniknąć duplikatów, nawet jeśli wycinek, który kopiuje dane jest uruchamiana w więcej niż raz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b1d1cf62dad94ca6141ce33783c0b16b50c931c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622639"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Kopiuj powtarzalne w fabryce danych Azure

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany.  
 
> [!NOTE]
> Poniższe przykłady są dla bazy danych SQL Azure, ale są stosowane do dowolnego magazynem danych, który obsługuje prostokątne zestawów danych. Może być konieczne dostosowanie **typu** źródła i **zapytania** właściwości (na przykład: zapytanie, zamiast sqlReaderQuery) dla danych przechowywania.   

Zazwyczaj podczas odczytu z relacyjnych magazynów, który chcesz odczytać tylko danych dotyczących tego wycinka. Jest sposób, aby to zrobić za pomocą WindowStart i WindowEnd zmienne systemu dostępne w fabryce danych Azure. Przeczytaj informacje o zmiennych i funkcji w fabryce danych Azure w tym miejscu w [fabryki danych Azure — funkcje i zmienne systemu](data-factory-functions-variables.md) artykułu. Przykład: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
To zapytanie odczytuje dane, która znajduje się w zakresie czasu trwania wycinek (WindowStart -> WindowEnd) z tabeli MyTable. Uruchom ponownie tego wycinka zawsze będzie upewnij się, że te same dane jest do odczytu. 

W pozostałych przypadkach możesz przeczytać całą tabelę i może zdefiniować sqlReaderQuery w następujący sposób:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Powtarzalne zapisu SqlSink
Podczas kopiowania danych do **Azure SQL/programu SQL Server** od innych magazynów danych należy do uwzględnienia w celu uniknięcia wyników niezamierzone powtarzalności. 

Podczas kopiowania danych do bazy danych serwera SQL/SQL Azure, działanie kopiowania dołącza dane do tabeli ujścia domyślnie. Powiedz, dane są kopiowane z pliku CSV (wartości rozdzielane przecinkami) zawierającego dwa rekordy z poniższej tabeli w bazie danych serwera SQL/SQL Azure. Po uruchomieniu wycinek dwa rekordy są kopiowane do tabeli SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Załóżmy, że znaleziono błędy w pliku źródłowym i zaktualizować ilość przewodu w dół od 2 do 4. Jeśli uruchomisz wycinek danych dla tego okresu ręcznie, znajdują się dwie nowe rekordy dołączane do bazy danych serwera SQL/SQL Azure. W tym przykładzie założono, że żaden z kolumn w tabeli nie ma ograniczenia klucza podstawowego.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Aby uniknąć tego zachowania, należy określić semantykę UPSERT przy użyciu jednej z dwóch następujących mechanizmów:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanizmu 1: sqlWriterCleanupScript przy użyciu
Można użyć **sqlWriterCleanupScript** właściwości, aby wyczyścić przed wstawieniem dane po uruchomieniu wycinek danych z tabeli ujścia. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Po uruchomieniu wycinek skrypt czyszczący jest najpierw uruchom, aby usunąć dane, które odpowiada wycinka z tabeli SQL. Działanie kopiowania Wstawianie danych do tabeli SQL. Jeśli ponownego uruchomienia wycinka jest aktualizowana ilość pożądane.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Załóżmy, że rekord płaskiej podkładka zostanie usunięte z oryginalnej csv. Ponowne uruchomienie wycinka dałby w efekcie następujące wyniki: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Działanie kopiowania uruchomiono skrypt czyszczący do usuwania odpowiednich danych dla tego wycinka. A następnie go odczytać dane wejściowe z plików csv (który następnie zawiera tylko jeden rekord) i dodaje go do tabeli. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanizmu 2: sliceIdentifierColumnName przy użyciu
> [!IMPORTANT]
> Obecnie sliceIdentifierColumnName nie jest obsługiwana dla usługi Azure SQL Data Warehouse. 

Drugi mechanizm do osiągnięcia powtarzalności jest wprowadzenie dedykowanego kolumny (sliceIdentifierColumnName) w tabeli docelowej. W tej kolumnie będzie służyć przez fabryki danych Azure, aby upewnić się, że na serwerze źródłowym i docelowym zachować synchronizację. Ta metoda działa po elastyczność zmiana lub definiowania schematu SQL tabeli docelowej. 

W tej kolumnie jest używany przez fabryki danych Azure do celów powtarzalność i w procesie fabryki danych Azure nie powoduje zmiany schematu do tabeli. Sposób użycia tej metody:

1. Zdefiniuj kolumny typu **danych binarnych (32)** w docelowej tabeli SQL. Nie powinno być nie ograniczeń dla tej kolumny. Teraz nazwę tej kolumny jako AdfSliceIdentifier w tym przykładzie.


    Tabela źródłowa:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Tabela docelowa: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

2. Należy użyć go w przypadku działania kopiowania w następujący sposób:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Fabryka danych Azure wypełnia tej kolumny, trzeba upewnić się, że na serwerze źródłowym i docelowym zachować synchronizację zgodnie z harmonogramem. Poza tym kontekście nie można używać wartości tej kolumny. 

Podobnie do mechanizmu 1, działanie kopiowania automatycznie oczyszcza dane wycinka danego przeznaczenia tabeli SQL. Następnie wstawia dane ze źródła w tabeli docelowej. 

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj następujące łącznika artykuły, które pełną przykłady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
