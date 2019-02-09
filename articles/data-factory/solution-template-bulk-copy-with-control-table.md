---
title: Zbiorcze kopiowanie danych z bazy danych za pomocą kontrolki tabeli za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą szablonu rozwiązania pełni zbiorcze kopiowanie danych z bazy danych, przechowywanie partition listę tabel źródłowych za pomocą usługi Azure Data Factory przy użyciu tabeli zewnętrznej kontroli.
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
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967563"
---
# <a name="bulk-copy-from-database-with-control-table"></a>Zbiorcze kopiowanie danych z bazy danych za pomocą kontrolki tabeli

Jeśli chcesz skopiować dane z magazynu danych, takich jak Oracle server, serwera Netezza, Teradata serwera lub programu SQL Server na platformie Azure, musisz załadować ogromna ilość danych z wielu tabel w źródłach danych. W większości przypadków dane muszą tworzyć dodatkowe partycje w każdej tabeli, tak aby możesz załadować wiersze, dla których wiele równoległych wątków z pojedynczej tabeli. Obecne szablon jest przeznaczony dla tej sprawy. 

Jeśli chcesz skopiować dane z niewielkiej liczbie tabel z niewielki rozmiar danych jest bardziej wydajne dla przejścia do "Narzędzie do kopiowania danych" mieć jeden jednego z kopiowaniem działania lub działanie foreach i działania kopiowania w potoku. Ten szablon jest większa niż użytkownik należy uzyskać prosty przypadek użycia.

## <a name="about-this-solution-template"></a>Temat ten szablon rozwiązania

Ten szablon umożliwia pobranie listy partycji źródłowej bazy danych z tabeli zewnętrznej kontroli, który ma być kopiowane do docelowego magazynu i następnie wykonuje iterację na każdej partycji w źródłowej bazie danych i wykonuje operację kopiowania danych.

Szablon zawiera trzy czynności:
-   A **wyszukiwania** działanie, aby pobrać listę partycji źródłowej bazy danych z tabeli zewnętrznej kontroli.
-   A **ForEach** działanie, aby pobrać listę partycji z działanie lookup i wejść każdego z nich do działania kopiowania.
-   A **kopiowania** działanie, aby skopiować każda partycja z magazynu bazy danych źródłowych do docelowego magazynu.

Szablon definiuje parametry pięć:
-   Parametr *Control_Table_Name* jest nazwa tabeli dla tabeli zewnętrznej kontroli. Tabela kontroli jest używana do przechowywania na liście partycji dla źródłowej bazy danych.
-   Parametr *Control_Table_Schema_PartitionID* jest nazwą kolumny w tabeli zewnętrznej kontroli do przechowywania każdego identyfikatora partycji. Upewnij się, że identyfikator partycji jest unikatowy dla każdej partycji w źródłowej bazie danych.
-   Parametr *Control_Table_Schema_SourceTableName* jest nazwą kolumny w tabeli zewnętrznej kontroli do przechowywania każdej nazwy tabeli ze źródłowej bazy danych.
-   Parametr *Control_Table_Schema_FilterQuery* jest nazwą kolumny w tabeli zewnętrznej kontroli, aby zapisać zapytanie filtru, aby pobrać danych z każdej partycji w źródłowej bazie danych. Na przykład, jeśli dane są dzielone według roku, zapytanie przechowywane w każdym wierszu może być podobna "Wybierz * ze źródła danych gdzie LastModifytime > =" 2015-01-01-00:00:00 "i LastModifytime < ="2015-12-31 23:59:59.999'' "
-   Parametr *Data_Destination_Folder_Path* jest ścieżka folderu, w którym dane są kopiowane do magazynu docelowego.  Ten parametr jest widoczne tylko w przypadku, gdy w wybranej lokalizacji docelowej jest magazynem magazynu opartego na pliku.  Jeśli usługa SQL Data Warehouse jako magazyn docelowy, nie ma parametru musieli wprowadzona w tym miejscu:. Jednak nazwy tabel i schematu usługi SQL data warehouse musi być taka sama, jak te w źródłowej bazie danych.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Utwórz tabelę kontroli programu SQL server lub SQL Azure do przechowywania listy partycji źródłowej bazy danych z kopiowaniem masowym.  W przykładzie poniżej widać istnieje pięć partycji w źródłowej bazie danych, których trzy partycje dla jednej tabeli:*datasource_table* i dwie partycje związanych z innej tabeli:*tabeli project_table*. Kolumna *LastModifytime* służy do partycjonowania danych w tabeli *datasource_table* ze źródłowej bazy danych. Zapytanie używane do odczytywania pierwszej partycji jest "Wybierz * z datasource_table gdzie LastModifytime > =" 2015-01-01-00:00:00 "i LastModifytime < ="2015-12-31 23:59:59.999'' ".  Widać również zapytania podobne do odczytywania danych z innych partycji. 

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

2. Przejdź do szablonu **zbiorcze kopiowanie danych z bazy danych**i Utwórz **nowe połączenie** do tabeli zewnętrznej kontroli.  To połączenie nawiązuje połączenie z bazy danych, w której została utworzona w kroku #1 tabeli kontroli.

    ![Utwórz nowe połączenie do tabeli kontroli](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Tworzenie **nowe połączenie** do której kopiowania danych ze źródłowej bazy danych.

     ![Utwórz nowe połączenie do źródłowej bazy danych](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Tworzenie **nowe połączenie** do Twojej gdzie kopiowania danych do docelowego magazynu danych.

    ![Utwórz nowe połączenie do docelowego magazynu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Kliknij przycisk **za pomocą tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Zostanie wyświetlony potok, które są dostępne w panelu, jak pokazano w poniższym przykładzie:

    ![Przejrzyj potoku](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Kliknij przycisk **debugowania**, parametr wejściowy, a następnie kliknij przycisk **Zakończ**

    ![Kliknij przycisk debugowania](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Zostanie wyświetlony wynik dostępne w panelu, jak pokazano w poniższym przykładzie:

    ![Przejrzyj wynik](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcjonalnie) Jeśli wybierzesz SQL Data Warehouse jako miejsca docelowego danych, trzeba będzie również dane wejściowe połączenia usługi Azure blob storage jako przejściowe, która jest wymagana przez program Polybase magazynu danych SQL.  Upewnij się, że kontener w usłudze blob storage został już utworzony.  
    
    ![Ustawienia programu Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)