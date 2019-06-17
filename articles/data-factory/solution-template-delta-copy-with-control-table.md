---
title: Kopiowanie danych różnicowych z bazy danych przy użyciu tabeli kontroli przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przyrostowo kopiować nowych lub zaktualizowanych wierszy tylko z bazy danych za pomocą usługi Azure Data Factory za pomocą szablonu rozwiązania.
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
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312511"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Kopiowanie zmian z bazy danych za pomocą kontrolki tabeli

W tym artykule opisano szablon, który jest dostępny dla przyrostowe ładowanie nowych lub zaktualizowanych wierszy z tabeli bazy danych na platformie Azure przy użyciu tabeli zewnętrznej kontroli, który przechowuje wartości limitu wysokiej.

Ten szablon wymaga, że schematu źródłowej bazy danych zawiera znacznik czasu: kolumny lub inkrementacji klucz do identyfikowania nowych lub zaktualizowanych wierszy.

>[!NOTE]
> Jeśli masz kolumnę sygnatur czasowych w źródłowej bazy danych do identyfikowania nowych lub zaktualizowanych wierszy, ale nie chcesz do utworzenia tabeli zewnętrznej kontroli do użycia dla kopii delta, należy użyć [narzędzia Azure Data Factory kopiowania danych](copy-data-tool.md) można pobrać potoku. Tego narzędzia używa wyzwalacz zaplanowany czas jako zmienną do odczytania nowe wiersze ze źródłowej bazy danych.

## <a name="about-this-solution-template"></a>Temat ten szablon rozwiązania

Ten szablon najpierw pobiera starej wartości limitu i porównuje go z bieżącą wartością limitu. Następnie kopiuje tylko zmiany ze źródłowej bazy danych, na podstawie porównania między dwiema wartościami limitu. Na koniec przechowuje nową wartość limitu wysokiej tabeli zewnętrznej kontroli następnym ładowanie danych różnicowych.

Szablon zawiera cztery czynności:
- **Wyszukiwanie** pobiera starej wartości limitu wysoka, który jest przechowywany w tabeli zewnętrznej kontroli.
- Inny **wyszukiwania** działanie pobiera bieżącą wartość wysokiej limitu ze źródłowej bazy danych.
- **Kopiuj** kopiuje tylko zmiany ze źródłowej bazy danych do docelowego magazynu. Zapytanie, które identyfikuje zmiany w źródłowej bazie danych jest podobny do "Wybierz * z Data_Source_Table gdzie TIMESTAMP_Column >"ostatni górnym limitem"i TIMESTAMP_Column < ="bieżący górnym limitem"".
- **SqlServerStoredProcedure** zapisuje bieżącą wartość znaku wodnego wysokiej do tabeli zewnętrznej kontroli dla kopii delta następnym razem.

Szablon definiuje parametry pięć:
- *Data_Source_Table_Name* to tabela w źródłowej bazy danych, który chcesz załadować dane.
- *Data_Source_WaterMarkColumn* jest nazwą kolumny w tabeli źródłowej, który jest używany do identyfikowania nowych lub zaktualizowano wierszy. Typ tej kolumny jest zazwyczaj *daty/godziny*, *INT*, lub podobne.
- *Data_Destination_Folder_Path* lub *Data_Destination_Table_Name* jest miejscem, gdzie dane są kopiowane do w magazynie docelowym.
- *Control_Table_Table_Name* jest tabeli zewnętrznej kontroli, która przechowuje wartość w górnym limitem.
- *Control_Table_Column_Name* jest kolumną w tabeli zewnętrznej kontroli, która przechowuje wartość w górnym limitem.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Zapoznaj się z źródło tabeli możesz chcesz załadować i zdefiniować kolumnę wysoki limit, który może służyć do identyfikowania nowych lub zaktualizowanych wierszy. Typ tej kolumny mogą być *daty/godziny*, *INT*, lub podobne. Ta kolumna wartość zwiększa się po dodaniu nowych wierszy. Z poniższej przykładowej tabeli źródłowej (data_source_table), możemy użyć *LastModifytime* kolumny jako kolumny limitu wysoka.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Utwórz tabelę kontroli programu SQL Server lub usługi Azure SQL Database do przechowywania wartości limitu wysokiej do ładowania danych różnicowych. W poniższym przykładzie nazwa tabeli kontroli jest *watermarktable*. W tej tabeli *WatermarkValue* to kolumna, która przechowuje wartość limitu o wysokiej i jej typ jest *daty/godziny*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Utwórz procedurę przechowywaną, w tym samym wystąpieniu programu SQL Server lub usługi Azure SQL Database, które zostało użyte do utworzenia tabeli kontroli. Procedura składowana używany do zapisywania nowej wartości limitu wysokiej tabeli zewnętrznej kontroli następnym ładowanie danych różnicowych.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Przejdź do **kopii Delta z bazy danych** szablonu. Tworzenie **New** połączenie przewidzianą do kopiowania danych ze źródłowej bazy danych.

    ![Utwórz nowe połączenie do tabeli źródłowej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Tworzenie **New** połączenia do docelowego magazynu danych, który chcesz skopiować dane.

    ![Utwórz nowe połączenie do tabeli docelowej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Tworzenie **New** połączenie tabeli zewnętrznej kontroli i procedury składowanej, który został utworzony w kroku 2 i 3.

    ![Tworzenie nowego połączenia z magazynem danych tabeli kontroli](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Wybierz **za pomocą tego szablonu**.

     ![Użyj tego szablonu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Potok jest dostępna, jest wyświetlany, jak pokazano w poniższym przykładzie:

     ![Przejrzyj potoku](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Wybierz **procedury składowanej**. Aby uzyskać **Nazwa procedury składowanej**, wybierz **[update_watermark]** . Wybierz **Importuj parametr**, a następnie wybierz pozycję **Dodaj zawartość dynamiczną**.  

     ![Ustaw działania procedury składowanej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Zapisać zawartość  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** , a następnie wybierz pozycję **Zakończ**.  

     ![Zapisanie zawartości dla parametrów procedury składowanej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Wybierz **debugowania**, wprowadź **parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Wybierz pozycję ** debugowania **](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Wyświetlane są wyniki podobne do poniższego przykładu:

    ![Przejrzyj wynik](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Można utworzyć nowych wierszy w tabeli źródłowej. Poniżej przedstawiono przykładowy języka SQL, aby utworzyć nowe wiersze:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Aby ponownie uruchomić potok, wybierz pozycję **debugowania**, wprowadź **parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Wybierz pozycję ** debugowania **](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Zobaczysz, że tylko nowe wiersze zostały skopiowane do lokalizacji docelowej.

15. (Opcjonalne:) Zaznaczenie SQL Data Warehouse jako miejsca docelowego danych, możesz także podać połączenia z usługą Azure Blob storage potrzeby wdrażania przejściowego, która jest wymagana przez program Polybase magazynu danych SQL. Upewnij się, że kontener został już utworzony w usłudze Blob storage.
    
    ![Konfigurowanie funkcji Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Kolejne kroki

- [Zbiorcze kopiowanie danych z bazy danych przy użyciu tabeli kontroli przy użyciu usługi Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopiuj pliki z wielu kontenerów przy użyciu usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)
