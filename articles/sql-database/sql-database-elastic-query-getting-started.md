---
title: Raport dla baz danych chmury skalowalnych w poziomie (poziomy podziału) | Dokumentacja firmy Microsoft
description: Użyj zapytań bazy danych między bazami danych do raportu na wielu baz danych.
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: e9e0588cc38bc7c29e81a1ae288ae116e3d50279
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Raport w chmurze skalowalnych w poziomie bazy danych (wersja zapoznawcza)
Możesz utworzyć raporty z wielu baz danych Azure SQL z punktu pojedynczego połączenia przy użyciu [elastycznej zapytania](sql-database-elastic-query-overview.md). Bazy danych musi być podzielony w poziomie, (określane również jako "podzielonej").

Jeśli masz istniejącą bazę danych, zobacz [Migrowanie istniejących baz danych do baz danych skalowalnych w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby poznać obiektów SQL potrzebne do zapytania, zobacz [zapytanie na poziomie partycjonowanej baz danych](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne
Pobierz i uruchom [wprowadzenie próbki narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Utwórz identyfikator niezależnego fragmentu mapy manager za pomocą przykładowej aplikacji
W tym polu spowoduje utworzenie mapy niezależnego fragmentu manager oraz kilka fragmentów, następuje wstawiania danych do fragmentów. Jeśli możesz już mają ustawienia fragmentów danych podzielonej w nich, możesz pominąć następujące kroki i przejść do następnej sekcji.

1. Tworzenie i uruchamianie **wprowadzenie do korzystania z narzędzi elastycznej bazy danych** przykładowej aplikacji. Postępuj zgodnie z instrukcjami aż do kroku 7 w sekcji [pobieranie i uruchamianie aplikacji przykładowej](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Po zakończeniu kroku 7 zostanie wyświetlony następujący wiersz polecenia:

    ![Wiersz polecenia][1]
2. W oknie wiersza polecenia wpisz "1" i naciśnij klawisz **Enter**. Tworzy identyfikator niezależnego fragmentu menedżera map i dodaje dwa niezależne do serwera. Następnie wpisz "3" i naciśnij klawisz **Enter**; powtórzenie akcji cztery razy. Wstawia Przykładowe wiersze danych z fragmentów.
3. [Portalu Azure](https://portal.azure.com) powinny być widoczne trzech nowych baz danych na serwerze:

   ![Visual Studio potwierdzenia][2]

   W tym momencie między bazami danych zapytania są obsługiwane za pomocą biblioteki klienta elastycznej bazy danych. Na przykład opcja 4 w oknie wiersza polecenia. Wyniki zapytania wielu niezależnych są zawsze **UNION ALL** wyników z wszystkich fragmentów.

   W następnej sekcji utworzymy punktu końcowego bazy danych przykładowych obsługującego bardziej zaawansowane funkcje zapytań danych w liczbie fragmentów.

## <a name="create-an-elastic-query-database"></a>Tworzenie elastycznej kwerendy bazy danych
1. Otwórz [portalu Azure](https://portal.azure.com) i zaloguj się.
2. Utwórz nową bazę danych Azure SQL, w tym samym serwerze co konfigurację niezależnego fragmentu. Nazwa bazy danych "ElasticDBQuery."

    ![Portalu Azure i warstwę cenową][3]

    > [!NOTE]
    > można użyć istniejącej bazy danych. Jeśli to zrobisz, nie może być jednym z fragmentów, które chcesz wykonać zapytania na. Ta baza danych będzie służyć do tworzenia obiektów metadanych dla zapytania elastycznej bazy danych.
    >

## <a name="create-database-objects"></a>Tworzenie obiektów bazy danych
### <a name="database-scoped-master-key-and-credentials"></a>Klucz główny o zakresie bazy danych i poświadczeń
Są one używane do nawiązania połączenia Menedżera mapy niezależnego fragmentu i odłamków:

1. Otwórz program SQL Server Management Studio lub SQL Server Data Tools w programie Visual Studio.
2. Łączenia z bazą danych ElasticDBQuery i wykonaj następujące polecenia T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" i "password" powinny być takie same jak informacje logowania używany w kroku 6 [pobieranie i uruchamianie aplikacji przykładowej](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) w [wprowadzenie do korzystania z narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Zewnętrzne źródła danych
Aby utworzyć zewnętrznego źródła danych, uruchom następujące polecenie w bazie danych ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" jest nazwą mapy niezależnego fragmentu, jeśli utworzono mapy niezależnego fragmentu i Menedżera mapy niezależnego fragmentu przy użyciu przykładowych narzędzi elastycznej bazy danych. Jednak jeśli używasz niestandardowych ustawień dla tego przykładu, następnie należy wybrana w aplikacji Nazwa mapy niezależnego fragmentu.

### <a name="external-tables"></a>Tabele zewnętrzne
Tworzenie tabeli zewnętrznej zgodny tabeli Klienci na odłamków, wykonując następujące polecenie na ElasticDBQuery bazy danych:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowego zapytania T-SQL elastycznej bazy danych
Po zdefiniowaniu zewnętrznym źródle danych i tabele zewnętrzne mogą teraz używać pełnej T-SQL w tabelach zewnętrznych.

Wykonaj tę kwerendę w bazie danych ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Można zauważyć, że zapytanie agreguje wyniki wszystkich odłamków i zapewnia następujące dane wyjściowe:

![Szczegóły danych wyjściowych][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importuj wyniki zapytania elastycznej bazy danych do programu Excel
 Możesz zaimportować wyników zapytania do pliku programu Excel.

1. Launch Excel 2013.
2. Przejdź do **danych** wstążki.
3. Kliknij przycisk **z innych źródeł** i kliknij przycisk **z programu SQL Server**.

   ![Importowania z programu Excel z innych źródeł][5]
4. W **Kreator połączenia danych** wpisz poświadczenia, a nazwa logowania serwera. Następnie kliknij przycisk **Next** (Dalej).
5. W oknie dialogowym **wybierz bazę danych, która zawiera dane, które mają**, wybierz pozycję **ElasticDBQuery** bazy danych.
6. Wybierz **klientów** tabeli w widoku listy, a następnie kliknij przycisk **dalej**. Następnie kliknij przycisk **Zakończ**.
7. W **i zaimportuj dane** formularza, w obszarze **wybierz sposób wyświetlania tych danych w skoroszycie**, wybierz pozycję **tabeli** i kliknij przycisk **OK**.

Wszystkie wiersze z **klientów** tabeli, przechowywane w różnych odłamków wypełnić arkuszu programu Excel.

Możesz teraz użyć funkcji wizualizacji zaawansowanych danych programu Excel. Parametry połączenia z nazwą serwera, nazwa bazy danych i poświadczeń służy do nawiązania narzędzi integracji danych i analizy Biznesowej kwerendy elastycznej bazy danych. Upewnij się, czy program SQL Server jest obsługiwana jako źródło danych dla własnych narzędzi. Może się odwoływać do kwerendy elastycznej bazy danych i tabele zewnętrzne, podobnie jak wszystkie inne bazy danych programu SQL Server i tabel programu SQL Server, które można połączyć się z narzędziem.

### <a name="cost"></a>Koszty
Brak bez dodatkowych opłat dla funkcji elastycznej kwerendy bazy danych.

Aby uzyskać informacje o cenach zobacz [szczegóły cennika bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Kolejne kroki

* Omówienie elastycznej zapytania, zobacz [elastycznej zapytań — omówienie](sql-database-elastic-query-overview.md).
* Samouczek partycjonowania pionowego, zobacz [wprowadzenie do korzystania z bazy danych między kwerendy (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Dla zapytań dotyczących danych pionowo podzielonym na partycje i składnię i przykład, zobacz [zapytań w pionie na partycje danych)](sql-database-elastic-query-vertical-partitioning.md)
* Dla zapytań dotyczących danych poziomie podzielonym na partycje i składnię i przykład, zobacz [zapytań w poziomie na partycje danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL w jednym zdalnej bazy danych SQL Azure lub zestaw baz danych, służąc jako odłamków w poziomie schemat partycjonowania.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
