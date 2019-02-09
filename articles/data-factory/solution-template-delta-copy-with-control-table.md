---
title: Kopiowanie danych różnicowych z bazy danych za pomocą kontrolki tabeli za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967497"
---
# <a name="delta-copy-from-database-with-control-table"></a>Kopiowanie danych różnicowych z bazy danych za pomocą kontrolki tabeli

Kiedy chcesz przyrostowe ładowanie zmiany (nowych lub zaktualizowanych wierszy) tylko z tabeli w bazie danych na platformę Azure za pomocą tabeli zewnętrznej kontroli przechowywania wartości limitu wysoka.  Obecne szablon jest przeznaczony dla tej sprawy. 

Ten szablon wymaga schematu źródłowej bazy danych musi zawierający znacznik czasu: kolumny lub o wartości klucza do identyfikowania nowych lub zaktualizowanych wierszy.

Jeśli masz kolumnę sygnatur czasowych w źródłowej bazy danych do identyfikowania nowych lub zaktualizowanych wierszy, ale nie powinien być utworzenia tabeli zewnętrznej kontroli umożliwiające kopii delta, można pobrać potok, który używa wyzwalacz zaplanowany czas jako zmienną do odczytu, można użyć narzędzia do kopiowania danych nowe wiersze tylko ze źródłowej bazy danych.

## <a name="about-this-solution-template"></a>Temat ten szablon rozwiązania

Ten szablon jest zawsze najpierw pobiera starej wartości limitu i porównuje go z bieżącą wartością limitu. Po tym tylko kopiuje zmiany ze źródłowej bazy danych na podstawie porównania między 2 wartości limitu.  Po zakończeniu przechowuje nową wartość limitu wysokiej tabeli zewnętrznej kontroli następnym ładowanie danych różnicowych.

Szablon zawiera cztery czynności:
-   A **wyszukiwania** działania w celu pobrania starej wartości limitu wysokiej przechowywane w tabeli zewnętrznej kontroli.
-   A **wyszukiwania** działanie, aby pobrać bieżącą wartość w górnym limitem ze źródłowej bazy danych.
-   A **kopiowania** działanie w celu kopiowania zmiany tylko w bazie danych źródła do docelowego magazynu. Zapytania używane do identyfikowania zmian ze źródłowej bazy danych w działaniu kopiowania jest podobny jako "Wybierz * z Data_Source_Table gdzie TIMESTAMP_Column >"ostatni górnym limitem"i TIMESTAMP_Column < ="bieżący górnym limitem"".
-   A **SqlServerStoredProcedure** działanie, aby zapisać bieżącą wartość znaku wodnego wysokiej do tabeli zewnętrznej kontroli dla kopii delta następnym razem.

Szablon definiuje parametry pięć:
-   Parametr *Data_Source_Table_Name* jest nazwa tabeli ze źródłowej bazy danych, które chcesz załadować dane z.
-   Parametr *Data_Source_WaterMarkColumn* jest nazwą kolumny w tabeli źródłowej, która może służyć do identyfikowania nowych lub zaktualizowanych wierszy. Typ tej kolumny można zwykle daty/godziny lub INT itp.
-   Parametr *Data_Destination_Folder_Path* lub *Data_Destination_Table_Name* jest miejscem, gdzie dane są kopiowane do magazynu docelowego.
-   Parametr *Control_Table_Table_Name* jest nazwą tabeli zewnętrznej kontroli do przechowywania wartości limitu wysoka.
-   Parametr *Control_Table_Column_Name* jest nazwą kolumny w tabeli zewnętrznej kontroli do przechowywania wartości limitu wysoka.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Zapoznaj się z tabeli źródłowej, które chcesz załadować i Zdefiniuj kolumny limitu wysokiej, która może służyć do tworzenia wycinków nowych lub zaktualizowanych wierszy. Typ tej kolumny można zwykle daty/godziny lub INT itp., a jej dane, utrzymywanie zwiększenie przy dodawaniu nowych wierszy.  Z przykładowej tabeli źródłowej (nazwa tabeli: data_source_table) poniżej, można użyć kolumny *LastModifytime* jako kolumny limitu wysoka.

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
    
2. Utwórz tabelę kontroli programu SQL server lub SQL Azure do przechowywania wartości limitu wysokiej do ładowania danych różnicowych. W przykładzie poniżej widać, to nazwa tabeli kontroli *watermarktable*. To jest nazwa kolumny do przechowywania wartości limitu wysokiej *WatermarkValue* a jej typ jest *daty/godziny*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Utwórz procedurę składowaną w tym samym programie SQL server lub SQL Azure użyty do utworzenia tabeli kontroli. Procedura składowana używany do zapisywania nowej wartości limitu wysokiej do tabeli zewnętrznej kontroli dla następnego ładowanie danych różnicowych.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Przejdź do szablonu **kopii Delta z bazy danych**i Utwórz **nowe połączenie** do której kopiowania danych ze źródłowej bazy danych.

    ![Utwórz nowe połączenie do tabeli źródłowej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Tworzenie **nowe połączenie** do Twojej gdzie kopiowania danych do docelowego magazynu danych.

    ![Utwórz nowe połączenie do tabeli docelowej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Tworzenie **nowe połączenie** do tabeli zewnętrznej kontroli i procedury składowanej.  Nawiązuje połączenie z bazą danych, gdzie został utworzona tabela kontroli i procedury składowanej w kroku #2 i #3.

    ![Tworzenie nowego połączenia z magazynem danych tabeli kontroli](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Kliknij przycisk **za pomocą tego szablonu**.

     ![Użyj tego szablonu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Zostanie wyświetlony potok, które są dostępne w panelu, jak pokazano w poniższym przykładzie:

     ![Przegląd potoku](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Kliknij działanie Stored Procedure, wybierz **Nazwa procedury składowanej**, kliknij przycisk **parametr importu** i kliknij przycisk **Dodaj zawartość dynamiczną**.  

     ![Działanie procedura składowana zestawu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Zapisać zawartość **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** i kliknij przycisk **Zakończ**.  

     ![Pisanie zawartości parametru dla procedury składowanej](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Kliknij przycisk **debugowania**, parametry wejściowe i kliknij przycisk **Zakończ**.

    ![Kliknij przycisk debugowania](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Zostanie wyświetlony wynik dostępne w panelu, jak pokazano w poniższym przykładzie:

    ![Przejrzyj wynik](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Można utworzyć nowych wierszy w tabeli źródłowej.  Przykładowe sql do tworzenia nowych wierszy może być następujący:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. Ponowne uruchamianie potoku, klikając **debugowania**, parametry wejściowe i kliknij przycisk **Zakończ**.

    ![Kliknij przycisk debugowania](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. Zobaczysz, że tylko nowe wiersze zostały skopiowane do miejsca docelowego.

15. (Opcjonalnie) Jeśli wybierzesz SQL Data Warehouse jako miejsca docelowego danych, trzeba będzie również dane wejściowe połączenia usługi Azure blob storage jako przejściowe, która jest wymagana przez program Polybase magazynu danych SQL.  Upewnij się, że kontenera w usłudze blob storage został już utworzony.  
    
    ![Konfigurowanie funkcji Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)