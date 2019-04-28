---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335393"
---
## <a name="repeatability-during-copy"></a>Powtarzalności podczas kopiowania
Podczas kopiowania danych do usługi Azure SQL i SQL Server z innymi danymi przechowuje należy zapewnić powtarzalność należy pamiętać, aby uniknąć niezamierzonego wyników. 

Podczas kopiowania danych do bazy danych Azure SQL i SQL Server, działanie kopiowania będzie domyślnie DOŁĄCZANIA zestawu danych do tabeli ujścia domyślnie. Na przykład podczas kopiowania danych ze źródła pliku CSV (dane wartości rozdzielane przecinkami) zawierający dwa rekordy do bazy danych Azure SQL i SQL Server, jest to tabela wygląda następująco:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Załóżmy, że znaleziono błędy w pliku źródłowym i zaktualizować ilość rury w dół od 2 do 4 w pliku źródłowym. Jeśli ponownie uruchomisz wycinka danych dla tego okresu, znajdziesz dwóch nowych rekordów do bazy danych Azure SQL i SQL Server. Poniżej przyjęto założenie, żadna z tych kolumn w tabeli ograniczenia klucza podstawowego.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Aby tego uniknąć, konieczne będzie określić semantyki UPSERT przy użyciu jednej z poniższych 2 mechanizmów podaną poniżej.

> [!NOTE]
> Wycinek mogą być ponownie uruchamiane automatycznie w usłudze Azure Data Factory zgodnie z zasady ponawiania określone.
> 
> 

### <a name="mechanism-1"></a>Mechanizm 1
Możesz wykorzystać **sqlWriterCleanupScript** właściwość, aby najpierw wykonać akcji oczyszczania, gdy wycinek jest uruchamiana. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Czy można wykonać skrypt czyszczący pierwszy podczas kopiowania dla danego wycinka, co spowoduje usunięcie danych z tabeli SQL odpowiadający tego wycinka. Działanie zostanie następnie wstawianie do tabeli SQL. 

Jeśli wycinek jest teraz ponownie uruchomić, a następnie można tam znaleźć ilość jest aktualizowana jako żądana.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Załóżmy, że rekord prostego podkładka zostanie usunięty z oryginalnego pliku csv. Ponowne uruchomienie wycinek dałby w efekcie następujący wynik: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nic nowego musiało być przeprowadzane. Działanie kopiowania uruchomiono skrypt czyszczący do usunięcia odpowiednich danych dla tego wycinka. A następnie go odczytać dane wejściowe z pliku csv, (które następnie zawiera tylko 1 rekord) i dodaje je do tabeli. 

### <a name="mechanism-2"></a>Mechanizm 2
> [!IMPORTANT]
> w tej chwili sliceIdentifierColumnName nie jest obsługiwana dla usługi Azure SQL Data Warehouse. 

Inny mechanizm do osiągnięcia powtarzalności jest posiadanie dedykowanego kolumny (**sliceIdentifierColumnName**) w tabeli docelowej. Ta kolumna może służyć przez usługę Azure Data Factory do upewnij się, że źródłowe i docelowe pozostają zsynchronizowane. Ta metoda działa, gdy pewien stopień elastyczności, zmienianie lub definiowania schematu SQL tabeli docelowej. 

Ta kolumna będzie używana przez usługę Azure Data Factory dla celów powtarzalność i procesu usługi Azure Data Factory nie wprowadzi zmiany schematu tabeli. Sposób korzystania z tej metody:

1. Zdefiniuj kolumny typu binarnego (32) w docelowej tabeli SQL. Powinna istnieć bez ograniczeń w tej kolumnie. Nadajmy nazwę tej kolumny jako "ColumnForADFuseOnly" w tym przykładzie.
2. Należy użyć go w działaniu kopiowania w następujący sposób:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Usługa Azure Data Factory, zostaną wyświetlone w tej kolumnie, zgodnie z konieczności upewnij się, że źródłowe i docelowe pozostają zsynchronizowane. Wartości tej kolumny nie powinno być używane poza tym kontekstem przez użytkownika. 

Podobnie jak mechanizm 1, działanie kopiowania zostanie automatycznie najpierw wyczyścić dane dla danego wycinka z docelowego tabelę SQL, a następnie uruchomić działanie kopiowania, zwykle po to, aby wstawić dane ze źródła do miejsca docelowego dla tego wycinka. 

