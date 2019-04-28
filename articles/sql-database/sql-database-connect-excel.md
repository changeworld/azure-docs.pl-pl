---
title: Łączenie programu Excel z pojedynczej bazy danych w usłudze Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć program Microsoft Excel z pojedynczej bazy danych w bazie danych Azure SQL. Importowanie danych do programu Excel, raportowanie i eksploracja danych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: e1cd0d8462c31c8b843f7962f923accc6b63ae00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414374"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Łączenie programu Excel z pojedynczej bazy danych w bazie danych Azure SQL i utworzenie raportu

Łączenie programu Excel z pojedynczej bazy danych w usłudze Azure SQL Database i importowanie danych i tworzenie tabel i wykresów na podstawie wartości w bazie danych. W tym samouczku skonfigurujesz połączenie między programem Excel i tabelą bazy danych, zapiszesz plik przechowujący dane oraz informacje o połączeniu dla programu Excel, a następnie utworzysz wykres przestawny z wartościami bazy danych.

Przed rozpoczęciem pracy należy pojedynczej bazy danych. Jeśli nie masz, zobacz [tworzenie pojedynczej bazy danych](sql-database-single-database-get-started.md) i [utworzyć zaporę na poziomie serwera IP](sql-database-server-level-firewall-rule.md) można pobrać pojedynczej bazy danych z przykładowymi danymi w ciągu kilku minut.

W tym artykule szybko importować przykładowe dane do programu Excel z tego artykułu, ale można wykonać podobne kroki z użyciem własnych danych.

Potrzebna będzie również kopia programu Excel. W tym artykule wykorzystano program [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Łączenie programu Excel z bazą danych i ładowanie danych programu SQL

1. Aby połączyć program Excel z bazą danych SQL, otwórz program Excel, a następnie utwórz nowy skoroszyt lub otwórz istniejący skoroszyt programu Excel.
2. Na pasku menu w górnej części strony wybierz **danych** zaznacz **Pobierz dane**, wybierz z platformy Azure, a następnie wybierz **z usługi Azure SQL Database**. 

   ![Wybierz źródło danych: Łączenie programu Excel do usługi SQL database.](./media/sql-database-connect-excel/excel_data_source.png)

   Zostanie otwarty Kreator połączenia danych.
3. W oknie dialogowym **Łączenie z serwerem bazy danych** wpisz **nazwę serwera** usługi SQL Database, z którym chcesz nawiązać połączenie w następującej formie: <*nazwaserwera*>**. database.windows.net**. Na przykład **msftestserver.database.windows.net**. Opcjonalnie wprowadź nazwę bazy danych. Wybierz **OK** aby otworzyć okno poświadczeń. 

   ![server-name.png](media/sql-database-connect-excel/server-name.png)

4. W **bazy danych SQL Server** okno dialogowe, wybierz opcję **bazy danych** po lewej stronie strony, a następnie wprowadź w Twojej **nazwa_użytkownika** i **hasło** dla Serwer SQL Database, którą chcesz się połączyć. Wybierz **Connect** otworzyć **Nawigator**. 

   ![Wpisywanie nazwy serwera i poświadczeń logowania](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > W zależności od środowiska sieciowego może nie być możliwe nawiązanie połączenia lub można utracić połączenie, jeśli serwer usługi SQL Database nie zezwala na ruch z adresu IP klienta. Przejdź do witryny [Azure Portal](https://portal.azure.com/), kliknij serwery SQL, kliknij serwer, którego używasz, kliknij zaporę systemu w ustawieniach i dodaj swój adres IP klienta. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to configure firewall settings](sql-database-configure-firewall-settings.md) (Jak skonfigurować ustawienia zapory).

5. W **Nawigator**, wybierz bazę danych, którą chcesz pracować z listy, wybierz tabele lub widoki, które chcesz pracować (Wybraliśmy **vGetAllCategories**), a następnie wybierz pozycję **obciążenia**przenoszenia danych z bazy danych w arkuszu kalkulacyjnym programu Excel.

    ![Wybierz bazę danych i tabelę.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importowanie danych do programu Excel i tworzenie wykresu przestawnego

Teraz, po nawiązaniu połączenia, masz kilka różnych opcji, jak załadować dane. Na przykład poniższe kroki tworzenia wykresu przestawnego na podstawie danych w bazie danych SQL. 

1. Postępuj zgodnie z instrukcjami w poprzedniej sekcji, ale tym razem, zamiast zaznaczania **obciążenia**, wybierz opcję **ładowanie do** z **obciążenia** listy rozwijanej.
2. Następnie wybierz sposób wyświetlania tych danych w skoroszycie. Wybrano **Wykres przestawny**. Można również utworzyć **Nowy arkusz** lub wybrać opcję **Dodaj te dane do modelu danych**. Więcej informacji o modelach danych można znaleźć w temacie [Tworzenie modelu danych w programie Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Wybieranie formatu danych w programie Excel](./media/sql-database-connect-excel/import-data.png)

    Arkusz zawiera teraz pustą tabelę przestawną i wykres.
3. W obszarze **Pola tabeli przestawnej** zaznacz wszystkie pola wyboru dla pól do wyświetlenia.

    ![Skonfiguruj raport bazy danych.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Aby połączyć inne skoroszyty programu Excel i arkuszy z bazą danych, należy zaznaczyć **danych** , a następnie wybierz pozycję **ostatnio używane źródła** można uruchomić **ostatnio używane źródła** okno dialogowe. Z tego miejsca wybierz połączenie, utworzonym na podstawie listy, a następnie kliknij przycisk **Otwórz**.
> ![Najnowsze połączenia](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Utwórz trwałe połączenie przy użyciu pliku odc

Aby trwale zapisać informacje dotyczące połączenia, możesz utworzyć plik odc i wprowadzić to połączenie można wybierać opcji w **istniejące połączenia** okno dialogowe. 

1. Na pasku menu w górnej części strony wybierz **danych** , a następnie wybierz pozycję **istniejące połączenia** można uruchomić **istniejące połączenia** okno dialogowe. 
   1. Wybierz **Przeglądaj w poszukiwaniu kolejnych** otworzyć **wybierz źródło danych** okno dialogowe.   
   2. Wybierz **+NewSqlServerConnection.odc** pliku, a następnie wybierz pozycję **Otwórz** otworzyć **Kreatora połączenia danych**.

      ![Nowe połączenie](media/sql-database-connect-excel/new-connection.png)

2. W **Kreatora połączenia danych**, wpisz nazwę serwera i poświadczenia bazy danych SQL. Wybierz opcję **Dalej**. 
   1. Wybierz bazę danych, która zawiera dane z listy rozwijanej. 
   2. Wybierz tabelę lub widok, który chcesz wziąć. Wybraliśmy vGetAllCategories.
   3. Wybierz opcję **Dalej**. 

      ![Kreator połączenia danych](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Wybierz lokalizację pliku, **nazwy pliku**i **przyjazną nazwę** na następnym ekranie Kreatora połączenia danych. Możesz również zapisać hasło w pliku, chociaż jest to potencjalnie może narazić dane na niepożądany dostęp. Wybierz **Zakończ** gdy będzie gotowe. 

    ![Zapisz połączenie danych](media/sql-database-connect-excel/save-data-connection.png)

4. Wybierz, jak chcesz zaimportować dane. Firma Microsoft wybrała w tabeli przestawnej. Właściwości połączenia można również zmodyfikować, wybierając **właściwości**. Wybierz **OK** gdy będzie gotowe. Jeśli nie wybrano zapisanie hasła z plikiem, następnie można będzie zostanie wyświetlony monit o podanie poświadczeń. 

    ![Importowanie danych](media/sql-database-connect-excel/import-data2.png)

5. Sprawdź, czy nowe połączenie została zapisana, rozwijając **danych** kartę, a następnie wybierając **istniejące połączenia**. 

    ![Istniejące połączenie](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby wykonywać zaawansowane zapytania i analizy, zobacz temat [Connect to SQL Database with SQL Server Management Studio](sql-database-connect-query-ssms.md) (Nawiązywanie połączenia z usługą SQL Database za pomocą programu SQL Server Management Studio).
* Dowiedz się, jakie zalety mają [pule elastyczne](sql-database-elastic-pool.md).
* Dowiedz się, jak [utworzyć aplikację internetową, która łączy się z bazą danych SQL Database zaplecza](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
