---
title: Kopiowanie masowe z bazy danych przy użyciu tabeli formantów
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania danych zbiorczych z bazy danych przy użyciu tabeli formantów zewnętrznych do przechowywania listy partycji tabel źródłowych przy użyciu Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3063767c73f4639e667d5f64b0563f1da396cfbf
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927305"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Kopiowanie masowe z bazy danych za pomocą tabeli formantów

Aby skopiować dane z magazynu danych programu Oracle Server, Netezza, Teradata lub SQL Server do Azure SQL Data Warehouse, należy załadować ogromne ilości danych z wielu tabel. Zwykle dane należy podzielić na partycje w każdej tabeli, aby można było załadować wiersze z wieloma wątkami równolegle z pojedynczej tabeli. W tym artykule opisano szablon, który ma być używany w tych scenariuszach.

 >! Uwaga Jeśli chcesz skopiować dane z niewielkiej liczby tabel z stosunkowo małą ilością danych do SQL Data Warehouse, możesz użyć [narzędzia Kopiowanie danych Azure Data Factory](copy-data-tool.md). Szablon opisany w tym artykule jest bardziej niezbędny w przypadku tego scenariusza.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon pobiera listę źródłowych partycji baz danych do skopiowania z zewnętrznej tabeli formantów. Następnie wykonuje iterację dla każdej partycji w źródłowej bazie danych i kopiuje dane do miejsca docelowego.

Szablon zawiera trzy działania:
- Funkcja **Lookup** pobiera listę baz danych z tabeli formantów zewnętrznych.
- Instrukcja **foreach** pobiera listę partycji z działania Lookup i iteruje każdą partycję w działaniu kopiowania.
- **Kopiuj** kopiuje każdą partycję ze źródłowego magazynu bazy danych do magazynu docelowego.

Szablon definiuje pięć parametrów:
- *Control_Table_Name* jest tabelą formantów zewnętrznych, która przechowuje listę partycji dla źródłowej bazy danych.
- *Control_Table_Schema_PartitionID* to nazwa kolumny w tabeli formantów zewnętrznych, która przechowuje każdy identyfikator partycji. Upewnij się, że identyfikator partycji jest unikatowy dla każdej partycji w źródłowej bazie danych.
- *Control_Table_Schema_SourceTableName* jest tabelą formantów zewnętrznych, która przechowuje każdą nazwę tabeli ze źródłowej bazy danych.
- *Control_Table_Schema_FilterQuery* to nazwa kolumny w tabeli formantów zewnętrznych, która przechowuje zapytanie filtru, aby pobrać dane z każdej partycji w źródłowej bazie danych. Na przykład w przypadku partycjonowania danych według roku zapytanie przechowywane w każdym wierszu może być podobne do elementu "select * from DataSource, gdzie LastModifytime > =" "2015-01-01 00:00:00" "i LastModifytime < =" "2015-12-31 23:59:59.999" "".
- *Data_Destination_Folder_Path* to ścieżka, w której dane są kopiowane do magazynu docelowego. Ten parametr jest widoczny tylko w przypadku, gdy wybrane miejsce docelowe jest magazynem opartym na plikach. W przypadku wybrania SQL Data Warehouse jako magazynu docelowego ten parametr nie jest wymagany. Nazwy tabel i schematu w SQL Data Warehouse muszą być takie same jak te w źródłowej bazie danych.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Utwórz tabelę formantów w SQL Server lub Azure SQL Database do przechowywania listy partycji źródłowej bazy danych na potrzeby kopiowania zbiorczego. W poniższym przykładzie istnieje pięć partycji w źródłowej bazie danych. Trzy partycje są dla *datasource_table*, a dwa są dla *project_table*. Kolumna *LastModifytime* służy do partycjonowania danych w tabeli *datasource_table* ze źródłowej bazy danych. Zapytanie, które jest używane do odczytywania pierwszej partycji, to "select * from datasource_table, gdzie LastModifytime > =" "2015-01-01 00:00:00" "i LastModifytime < =" "2015-12-31 23:59:59.999" "". Możesz użyć podobnego zapytania do odczytywania danych z innych partycji.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Przejdź do szablonu **kopiowania zbiorczego z bazy danych** . Utwórz **nowe** połączenie z tabelą formantów zewnętrznych utworzoną w kroku 1.

    ![Utwórz nowe połączenie z tabelą formantów](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Utwórz **nowe** połączenie z źródłową bazą danych, z której są kopiowane dane.

     ![Utwórz nowe połączenie ze źródłową bazą danych](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Utwórz **nowe** połączenie z docelowym magazynem danych, do którego chcesz skopiować dane.

    ![Utwórz nowe połączenie z magazynem docelowym](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Wybierz przycisk **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Zostanie wyświetlony potok, jak pokazano w następującym przykładzie:

    ![Przeglądanie potoku](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Kliknij pozycję * * Debuguj * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Zobaczysz wyniki podobne do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Obowiązkowe W przypadku wybrania SQL Data Warehouse jako miejsca docelowego danych należy wprowadzić połączenie z usługą Azure Blob Storage na potrzeby przemieszczania, zgodnie z wymaganiami SQL Data Warehouse Base. Upewnij się, że kontener w usłudze BLOB Storage został już utworzony.
    
    ![Ustawienie bazy](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
