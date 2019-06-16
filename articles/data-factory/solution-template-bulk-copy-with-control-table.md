---
title: Zbiorcze kopiowanie danych z bazy danych przy użyciu tabeli kontroli przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kopiowanie zbiorcze danych z bazy danych przy użyciu tabeli zewnętrznej kontroli przechowywać listę tabel źródłowych w partycji za pomocą usługi Azure Data Factory za pomocą szablonu rozwiązania.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635449"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Zbiorcze kopiowanie danych z bazy danych z tabeli kontroli

Aby skopiować dane z magazynu danych, serwera Oracle, Netezza, Teradata lub programu SQL Server do usługi Azure SQL Data Warehouse, należy załadować bardzo duże ilości danych z wielu tabel. Zwykle dane muszą być podzielone na partycje w każdej tabeli, aby załadujesz wiersze, dla których wiele równoległych wątków z pojedynczej tabeli. W tym artykule opisano szablon do użycia w tych scenariuszach.

 >! Należy PAMIĘTAĆ, jeśli chcesz skopiować dane z niewielkiej liczbie tabel, z woluminem danych stosunkowo mały do usługi SQL Data Warehouse, jest bardziej wydajne, aby użyć [narzędzia Azure Data Factory kopiowania danych](copy-data-tool.md). Szablon, który jest opisany w tym artykule jest większa niż jest Ci potrzebne w przypadku tego scenariusza.

## <a name="about-this-solution-template"></a>Temat ten szablon rozwiązania

Ten szablon umożliwia pobranie listy partycji bazy danych źródłowych do skopiowania z tabeli zewnętrznej kontroli. Następnie wykonuje iterację na każdej partycji w bazie danych źródła i kopiuje dane do lokalizacji docelowej.

Szablon zawiera trzy czynności:
- **Wyszukiwanie** umożliwia pobranie listy się partycji bazy danych z tabeli zewnętrznej kontroli.
- **Instrukcja ForEach** pobiera listę partycji z działania Lookup i dokonuje iteracji każdej partycji do działania kopiowania.
- **Kopiuj** kopiuje każda partycja z magazynu bazy danych źródłowego do docelowego magazynu.

Szablon definiuje parametry pięć:
- *Control_Table_Name* jest tabela zewnętrznej kontroli, w której są przechowywane na liście partycji dla źródłowej bazy danych.
- *Control_Table_Schema_PartitionID* nazywa się nazwa kolumny w tabeli zewnętrznej kontroli przechowujący każdego identyfikatora partycji. Upewnij się, że identyfikator partycji jest unikatowy dla każdej partycji w źródłowej bazie danych.
- *Control_Table_Schema_SourceTableName* jest tabeli zewnętrznej kontroli przechowujący każdej nazwy tabeli ze źródłowej bazy danych.
- *Control_Table_Schema_FilterQuery* jest nazwą kolumny w tabeli zewnętrznej kontroli, która przechowuje zapytanie filtru, aby pobrać danych z każdej partycji w źródłowej bazie danych. Na przykład, jeśli dane są dzielone według roku, zapytanie, które są przechowywane w każdym wierszu może być podobny do "Wybierz * ze źródła danych gdzie LastModifytime > =" 2015-01-01-00:00:00 "i LastModifytime < ="2015-12-31 23:59:59.999'' ".
- *Data_Destination_Folder_Path* jest ścieżką, w którym dane są kopiowane do magazynu docelowego. Ten parametr jest widoczna tylko w przypadku miejsca docelowego, które możesz wybrać magazyn oparty na pliku. Jeśli usługa SQL Data Warehouse jako magazyn docelowy, ten parametr nie jest wymagane. Jednak nazwy tabel i schematu usługi SQL Data Warehouse musi być taka sama, jak te w źródłowej bazie danych.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Utwórz tabelę kontroli programu SQL Server lub usługi Azure SQL Database do przechowywania listy źródłowej bazy danych partycji z kopiowaniem masowym. W poniższym przykładzie istnieje pięć partycji w źródłowej bazie danych. Trzy partycje dotyczą *datasource_table*, a dwa są przeznaczone dla *tabeli project_table*. Kolumna *LastModifytime* służy do partycjonowania danych w tabeli *datasource_table* ze źródłowej bazy danych. Zapytanie, które służy do odczytywania pierwszej partycji jest "Wybierz * z datasource_table gdzie LastModifytime > =" 2015-01-01-00:00:00 "i LastModifytime < ="2015-12-31 23:59:59.999'' ". Podobne zapytanie służy do odczytywania danych z innych partycji.

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

2. Przejdź do **zbiorcze kopiowanie danych z bazy danych** szablonu. Tworzenie **New** połączenie tabeli zewnętrznej kontroli, który został utworzony w kroku 1.

    ![Utwórz nowe połączenie do tabeli kontroli](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Tworzenie **New** połączenia źródłowej bazy danych, aby skopiować dane z.

     ![Utwórz nowe połączenie do źródłowej bazy danych](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Tworzenie **New** połączenie z danymi miejsce docelowe przechowywania, kopiowane dane.

    ![Utwórz nowe połączenie do docelowego magazynu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Wybierz **za pomocą tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Zostanie wyświetlony potok, jak pokazano w poniższym przykładzie:

    ![Przejrzyj potoku](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Wybierz **debugowania**, wprowadź **parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Click **Debug**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Zostaną wyświetlone wyniki podobne do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcjonalnie) Wybrana usługa SQL Data Warehouse jako miejsca docelowego danych, należy wprowadzić połączenie do usługi Azure Blob storage, potrzeby wdrażania przejściowego, zgodnie z wymaganiami programu Polybase magazynu danych SQL. Upewnij się, że już utworzono kontener w usłudze Blob storage.
    
    ![Ustawienia programu Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
