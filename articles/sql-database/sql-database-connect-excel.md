---
title: Łączenie programu Excel z bazą danych SQL Database | Microsoft Docs
description: Dowiedz się, jak połączyć program Microsoft Excel z bazą danych Azure SQL w chmurze. Importowanie danych do programu Excel, raportowanie i eksploracja danych.
services: sql-database
keywords: łączenie programu excel z sql, importowanie danych do programu excel
author: joseidz
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 03/10/2017
ms.author: craigg
ms.openlocfilehash: a87dd484b5931292fafd7f8b28d31f0f7f775a40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645671"
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Łączenie programu Excel z bazą danych Azure SQL i utworzyć raport

Łączenie programu Excel z bazą danych SQL w chmurze i importowanie danych i tworzenie tabel i wykresów na podstawie wartości w bazie danych. W tym samouczku skonfigurujesz połączenie między programem Excel i tabelą bazy danych, zapiszesz plik przechowujący dane oraz informacje o połączeniu dla programu Excel, a następnie utworzysz wykres przestawny z wartościami bazy danych.

Przed rozpoczęciem pracy będziesz potrzebować bazy danych SQL na platformie Azure. Jeśli jej nie masz, zobacz artykuł [Create your first SQL database](sql-database-get-started-portal.md) (Tworzenie pierwszej bazy danych SQL), aby w ciągu kilku minut uzyskać gotową do pracy bazę danych z przykładowymi danymi. W tym artykule będzie importować przykładowe dane do programu Excel z tego artykułu, ale można wykonać podobne kroki z użyciem własnych danych.

Potrzebna będzie również kopia programu Excel. W tym artykule wykorzystano program [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Łączenie programu Excel z bazą danych i ładowanie danych SQL
1. Aby połączyć program Excel z bazą danych SQL, otwórz program Excel, a następnie utwórz nowy skoroszyt lub otwórz istniejący skoroszyt programu Excel.
2. Na pasku menu w górnej części strony, wybierz **danych** wybierz opcję **Pobierz dane**, wybierz z platformy Azure, a następnie wybierz **z bazy danych SQL Azure**. 
   
   ![Wybór źródła danych: połącz program Excel z bazą danych SQL.](./media/sql-database-connect-excel/excel_data_source.png)
   
   Zostanie otwarty Kreator połączenia danych.
3. W oknie dialogowym **Łączenie z serwerem bazy danych** wpisz **nazwę serwera** usługi SQL Database, z którym chcesz nawiązać połączenie w następującej formie: <*nazwaserwera*>**. database.windows.net**. Na przykład **msftestserver.database.windows.net**. Opcjonalnie wprowadź nazwę bazy danych. Wybierz **OK** można otworzyć okna poświadczeń. 

   ![name.png serwera](media/sql-database-connect-excel/server-name.png)

1. W **bazy danych programu SQL Server** okno dialogowe, wybierz opcję **bazy danych** po lewej stronie po stronie, a następnie wprowadź w Twojej **nazwy użytkownika** i **hasło** dla Serwer bazy danych SQL, który chcesz nawiązać połączenie. Wybierz **Connect** otworzyć **Nawigator**. 

  ![Wpisywanie nazwy serwera i poświadczeń logowania](./media/sql-database-connect-excel/connect-to-server.png)
   
  > [!TIP]
  > W zależności od środowiska sieciowego może nie być możliwe nawiązanie połączenia lub można utracić połączenie, jeśli serwer usługi SQL Database nie zezwala na ruch z adresu IP klienta. Przejdź do witryny [Azure Portal](https://portal.azure.com/), kliknij serwery SQL, kliknij serwer, którego używasz, kliknij zaporę systemu w ustawieniach i dodaj swój adres IP klienta. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to configure firewall settings](sql-database-configure-firewall-settings.md) (Jak skonfigurować ustawienia zapory).
   
   
5. W **Nawigator**, wybierz bazy danych chcesz współpracować z listy, wybierz tabele lub widoki, które mają być używane (Wybraliśmy **vGetAllCategories**), a następnie wybierz **obciążenia**może przenieść dane z bazy danych SQL Azure do Twojej arkusz kalkulacyjny programu excel.
   
    ![Wybierz bazę danych i tabelę.](./media/sql-database-connect-excel/select-database-and-table.png)
   

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importowanie danych do programu Excel i tworzenie wykresu przestawnego
Teraz, po nawiązaniu połączenia, dostępnych jest kilka różnych jak do ładowania danych. Na przykład poniższe kroki tworzenia wykresu przestawnego na podstawie danych w bazie danych SQL. 

1. Wykonaj kroki opisane w poprzedniej sekcji, ale tym razem, zamiast zaznaczania **obciążenia**, wybierz pozycję **ładowania do** z **obciążenia** listy rozwijanej.
2. Następnie wybierz sposób wyświetlania tych danych w skoroszycie. Wybrano **Wykres przestawny**. Można również utworzyć **Nowy arkusz** lub wybrać opcję **Dodaj te dane do modelu danych**. Więcej informacji o modelach danych można znaleźć w temacie [Tworzenie modelu danych w programie Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 
   
    ![Wybieranie formatu danych w programie Excel](./media/sql-database-connect-excel/import-data.png)
   
    Arkusz zawiera teraz pustą tabelę przestawną i wykres.
2. W obszarze **Pola tabeli przestawnej** zaznacz wszystkie pola wyboru dla pól do wyświetlenia.
   
    ![Skonfiguruj raport bazy danych.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Jeśli chcesz się połączyć inne skoroszyty programu Excel i arkuszy w bazie danych, wybierz **danych** , a następnie wybierz **ostatnio używanymi źródłami** można uruchomić **ostatnio używanymi źródłami** okno dialogowe. Z tego miejsca, wybierz z listy utworzone połączenie, a następnie kliknij przycisk **Otwórz**.
> ![Najnowsze połączenia](media/sql-database-connect-excel/recent-connections.png)
 
## <a name="create-a-permanent-connection-using-odc-file"></a>Utwórz połączenie trwałe przy użyciu pliku odc
Aby trwale zapisać informacje dotyczące połączenia, można utworzyć plik odc i upewnij to połączenie wybieranych opcji w **istniejących połączeń** okno dialogowe. 

1. Na pasku menu w górnej części strony, wybierz **danych** , a następnie wybierz **istniejących połączeń** można uruchomić **istniejących połączeń** okno dialogowe. 
    1. Wybierz **Wyszukaj więcej** otworzyć **wybierz źródło danych** okno dialogowe.   
    2. Wybierz **+NewSqlServerConnection.odc** pliku, a następnie wybierz **Otwórz** otworzyć **Kreator połączenia danych**.

    ![Nowe połączenie](media/sql-database-connect-excel/new-connection.png)

2. W **Kreator połączenia danych**, wpisz nazwę serwera i poświadczenia bazy danych SQL. Wybierz opcję **Dalej**. 
    1. Wybierz bazę danych, która zawiera dane z listy rozwijanej. 
    2. Wybierz tabelę lub widok Cię interesują. Wybraliśmy vGetAllCategories.
    3. Wybierz opcję **Dalej**. 

    ![Kreator połączenia danych](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Wybierz lokalizację pliku, **nazwę pliku**i **przyjazną nazwę** na następnym ekranie Kreatora połączenia danych. Również można zapisać hasło w pliku, chociaż to narażając dane nieautoryzowanemu dostępowi. Wybierz **Zakończ** gdy będzie gotowe. 

    ![Zapisz połączenie danych](media/sql-database-connect-excel/save-data-connection.png)

4. Wybierz jak chcesz zaimportować dane. Wybraliśmy czy tabelę przestawną. Można również zmodyfikować właściwości połączenia wybierz **właściwości**. Wybierz **OK** gdy będzie gotowe. Jeśli nie wybrano Zapisz hasło z plikiem, następnie pojawi się monit o podanie poświadczeń. 

    ![Importowanie danych](media/sql-database-connect-excel/import-data2.png)

5. Sprawdź, czy nowe połączenie został zapisany rozwijając **danych** , a następnie wybierając **istniejących połączeń**. 

    ![Istniejące połączenie](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby wykonywać zaawansowane zapytania i analizy, zobacz temat [Connect to SQL Database with SQL Server Management Studio](sql-database-connect-query-ssms.md) (Nawiązywanie połączenia z usługą SQL Database za pomocą programu SQL Server Management Studio).
* Dowiedz się, jakie zalety mają [pule elastyczne](sql-database-elastic-pool.md).
* Dowiedz się, jak [utworzyć aplikację sieci Web, która łączy się z bazą danych SQL Database zaplecza](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

