---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183698"
---
## <a name="repeatability-during-copy"></a>Powtarzalność podczas kopiowania
Podczas kopiowania danych do usługi Azure SQL/SQL Server z innych magazynów danych należy pamiętać o powtarzalności, aby uniknąć niezamierzonych wyników. 

Podczas kopiowania danych do bazy danych programu Azure SQL/SQL Server, aktywność kopiowania domyślnie dołączy zestaw danych do tabeli ujścia domyślnie. Na przykład podczas kopiowania danych ze źródła pliku CSV (dane wartości oddzielonych przecinkami) zawierającego dwa rekordy do bazy danych programu Azure SQL/SQL Server, tak wygląda tabela:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Załóżmy, że znaleziono błędy w pliku źródłowym i zaktualizowano ilość Down Tube z 2 do 4 w pliku źródłowym. Po ponownym uruchomieniu plasterek danych dla tego okresu, znajdziesz dwa nowe rekordy dołączone do bazy danych programu Azure SQL/SQL Server. Poniżej założono, że żadna z kolumn w tabeli nie ma ograniczenia klucza podstawowego.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Aby tego uniknąć, należy określić semantycę UPSERT, korzystając z jednego z poniższych mechanizmów 2 podanych poniżej.

> [!NOTE]
> Plasterek można ponownie uruchomić automatycznie w usłudze Azure Data Factory zgodnie z określonymi zasadami ponawiania.
> 
> 

### <a name="mechanism-1"></a>Mechanizm 1
Można wykorzystać **właściwość sqlWriterCleanupScript,** aby najpierw wykonać akcję oczyszczania po uruchomieniu plasterka. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Skrypt oczyszczania zostanie wykonany najpierw podczas kopiowania dla danego plasterka, który usunie dane z tabeli SQL odpowiadające tego wycinka. Działanie zostanie następnie wstawione dane do tabeli SQL. 

Jeśli plasterek jest teraz ponownie uruchomiony, okaże się, że ilość jest aktualizowana zgodnie z potrzebami.

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
Nic nowego nie trzeba było robić. Działanie kopiowania uruchomiło skrypt oczyszczania, aby usunąć odpowiednie dane dla tego plasterka. Następnie odczytuje dane wejściowe z csv (który następnie zawierał tylko 1 rekord) i wstawił go do tabeli. 

### <a name="mechanism-2"></a>Mechanizm 2
> [!IMPORTANT]
> sliceIdentifierColumnName nie jest obecnie obsługiwana dla usługi Azure SQL Data Warehouse. 

Innym mechanizmem do osiągnięcia powtarzalności jest posiadanie dedykowanej kolumny (**sliceIdentifierColumnName**) w tabeli docelowej. Ta kolumna będzie używana przez usługę Azure Data Factory, aby zapewnić synchronizację miejsca docelowego ze źródłem i miejscem docelowym. To podejście działa, gdy istnieje elastyczność w zmienianiu lub definiowaniu docelowego schematu tabeli SQL. 

Ta kolumna będzie używana przez usługę Azure Data Factory do celów powtarzalności, a w procesie usługi Azure Data Factory nie będzie wprowadzać żadnych zmian schematu w tabeli. Sposób korzystania z tego podejścia:

1. Zdefiniuj kolumnę typu binarny (32) w docelowej tabeli SQL. Nie powinno być żadnych ograniczeń w tej kolumnie. Nazwijmy tę kolumnę jako "ColumnForADFuseOnly" w tym przykładzie.
2. Użyj go w działaniu kopiowania w następujący sposób:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Usługa Azure Data Factory wypełni tę kolumnę zgodnie z potrzebą zapewnienia synchronizacji miejsca docelowego ze źródłem i miejscem docelowym. Wartości tej kolumny nie powinny być używane poza tym kontekście przez użytkownika. 

Podobnie jak w przypadku mechanizmu 1, działanie kopiowania automatycznie najpierw oczyści dane dla danego plasterka z docelowej tabeli SQL, a następnie uruchomi działanie kopiowania normalnie, aby wstawić dane ze źródła do miejsca docelowego dla tego plasterka. 

