---
title: Powtarzalna kopia w fabryce danych platformy Azure
description: Dowiedz się, jak uniknąć duplikatów, nawet jeśli plasterek, który kopiuje dane, jest uruchamiany więcej niż jeden raz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7188cb5774699fc6e31fc3b8c78068bb33c6f552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281147"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Powtarzalna kopia w fabryce danych platformy Azure

## <a name="repeatable-read-from-relational-sources"></a>Powtarzalny odczyt ze źródeł relacyjnych
Podczas kopiowania danych z relacyjnych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. W usłudze Azure Data Factory można ponownie uruchomić plasterek ręcznie. Można również skonfigurować zasady ponawiania dla zestawu danych, tak aby plasterek został ponownie uruchomny w przypadku wystąpienia błędu. Gdy plasterek jest uruchamiany ponownie w obu przypadkach, należy upewnić się, że te same dane są odczytywane bez względu na to, ile razy jest uruchamiany plasterek.  
 
> [!NOTE]
> Poniższe przykłady są dla usługi Azure SQL, ale mają zastosowanie do dowolnego magazynu danych, który obsługuje prostokątne zestawy danych. Może być wymagane dostosowanie **typu** źródła i właściwości **kwerendy** (na przykład: kwerenda zamiast sqlReaderQuery) dla magazynu danych.   

Zazwyczaj podczas odczytu ze sklepów relacyjnych chcesz odczytać tylko dane odpowiadające tego wycinka. Sposobem na to byłoby przy użyciu windowstart i WindowEnd zmiennych systemowych dostępnych w usłudze Azure Data Factory. Przeczytaj o zmiennych i funkcji w usłudze Azure Data Factory tutaj w [fabryce danych platformy Azure — funkcje i zmienne systemowe](data-factory-functions-variables.md) artykułu. Przykład: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Ta kwerenda odczytuje dane, które mieści się w zakresie czasu trwania plasterka (WindowStart -> WindowEnd) z tabeli MyTable. Ponowne uruchomienie tego plasterka również zawsze upewnij się, że te same dane są odczytywane. 

W innych przypadkach można przeczytać całą tabelę i można zdefiniować sqlReaderQuery w następujący sposób:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Powtarzalny zapis do SqlSink
Podczas kopiowania danych do **programu Azure SQL/SQL Server** z innych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. 

Podczas kopiowania danych do bazy danych programu Azure SQL/SQL Server działanie dołącza dane do tabeli ujścia domyślnie. Załóżmy, że kopiujesz dane z pliku CSV (wartości oddzielone przecinkami) zawierającego dwa rekordy do poniższej tabeli w bazie danych programu Azure SQL/SQL Server. Po uruchomieniu plasterka dwa rekordy są kopiowane do tabeli SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Załóżmy, że znaleziono błędy w pliku źródłowym i zaktualizowano ilość Down Tube z 2 do 4. Po ponownym uruchomienia plasterka danych dla tego okresu ręcznie, znajdziesz dwa nowe rekordy dołączone do bazy danych programu Azure SQL/SQL Server. W tym przykładzie przyjęto założenie, że żadna z kolumn w tabeli nie ma ograniczenia klucza podstawowego.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Aby uniknąć tego zachowania, należy określić semantycę UPSERT przy użyciu jednego z następujących dwóch mechanizmów:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanizm 1: używanie sqlWriterCleanupScript
Za pomocą właściwości **sqlWriterCleanupScript** można oczyścić dane z tabeli ujścia przed wstawieniem danych po uruchomieniu plasterka. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Po uruchomieniu plasterka skrypt oczyszczania jest uruchamiany najpierw w celu usunięcia danych odpowiadających wycinku z tabeli SQL. Działanie kopiowania następnie wstawia dane do tabeli SQL. Jeśli plasterek zostanie ponownie uruchomienia, ilość zostanie zaktualizowana zgodnie z potrzebami.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Załóżmy, że rekord podkładki płaskiej zostanie usunięty z oryginalnego pliku csv. Następnie ponowne uruchomienie plasterka dałoby następujący wynik: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Działanie kopiowania uruchomiło skrypt oczyszczania, aby usunąć odpowiednie dane dla tego plasterka. Następnie odczytuje dane wejściowe z pliku csv (który następnie zawierał tylko jeden rekord) i wstawił go do tabeli. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanizm 2: przy użyciu plasterkaIdentifierColumnName
> [!IMPORTANT]
> Obecnie sliceIdentifierColumnName nie jest obsługiwany dla usługi Azure SQL Data Warehouse. 

Drugi mechanizm do osiągnięcia powtarzalności jest poprzez dedykowane kolumny (sliceIdentifierColumnName) w tabeli docelowej. Ta kolumna będzie używana przez usługę Azure Data Factory, aby zapewnić synchronizację miejsca docelowego ze źródłem i miejscem docelowym. To podejście działa, gdy istnieje elastyczność w zmienianiu lub definiowaniu docelowego schematu tabeli SQL. 

Ta kolumna jest używana przez usługę Azure Data Factory do celów powtarzalności, a w procesie usługi Azure Data Factory nie wprowadza żadnych zmian schematu w tabeli. Sposób korzystania z tego podejścia:

1. Zdefiniuj kolumnę typu **binarny (32)** w docelowej tabeli SQL. Nie powinno być żadnych ograniczeń w tej kolumnie. Nazwijmy tę kolumnę jako AdfSliceIdentifier w tym przykładzie.


    Tabela źródło:

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

1. Użyj go w działaniu kopiowania w następujący sposób:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Usługa Azure Data Factory wypełnia tę kolumnę zgodnie z potrzebą zapewnienia synchronizacji miejsca docelowego ze źródłem i miejscem docelowym. Wartości tej kolumny nie powinny być używane poza tym kontekstem. 

Podobnie jak w przypadku mechanizmu 1, działanie kopiowania automatycznie czyści dane dla danego plasterka z docelowej tabeli SQL. Następnie wstawia dane ze źródła do tabeli docelowej. 

## <a name="next-steps"></a>Następne kroki
Przejrzyj następujące artykuły łącznika, które dla kompletnych przykładów JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Magazyn danych SQL platformy Azure](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
