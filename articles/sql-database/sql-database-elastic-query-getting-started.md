---
title: Raport w bazach danych skalowanych w poziomie chmury (partycjonowanie poziome) | Dokumentacja firmy Microsoft
description: Użyj kwerend bazy danych obejmujące wiele baz danych do raportu w wielu bazach danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a73938c98ebaea310875f0db8b665d0f1aed55e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556276"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Raportowanie w bazach danych w chmurze skalowanych w poziomie (wersja zapoznawcza)

Możesz tworzyć raporty z wielu baz danych Azure SQL z punktu pojedynczego połączenia przy użyciu [zapytania elastycznego](sql-database-elastic-query-overview.md). Bazy danych muszą być partycjonowane w poziomie, (określana także jako "podzielonej na fragmenty").

Jeśli masz istniejącą bazę danych, zobacz [Migrowanie istniejących baz danych do baz danych skalowanych w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby dowiedzieć się obiekty SQL, wymagane do wykonywania zapytań, zobacz [zapytań w bazach danych partycjonowanej w poziomie](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz i uruchom [wprowadzenie do przykładowej narzędzi elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Tworzenie fragmentu manager mapy za pomocą przykładowej aplikacji
W tym miejscu utworzysz mapowania fragmentów w postaci manager oraz kilka fragmentów, a następnie wstawiania danych na fragmenty. Jeśli masz już skonfigurowano fragmentów danych podzielonych na fragmenty w nich, możesz pominąć poniższe kroki i przejść do następnej sekcji.

1. Kompilowanie i uruchamianie **rozpoczęcie korzystania z narzędzi elastycznych baz danych** przykładowej aplikacji. Postępuj zgodnie z instrukcjami aż do kroku 7 w sekcji [pobieranie i uruchamianie aplikacji przykładowej](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Na końcu krok 7 zostanie wyświetlony następujący wiersz polecenia:

    ![Wiersz polecenia][1]
2. W oknie wiersza polecenia wpisz "1", a następnie naciśnij klawisz **Enter**. Tworzy Menedżera mapowań fragmentów i dodaje dwie fragmentów do serwera. Następnie wpisz "3" i naciśnij klawisz **Enter**; powtórz akcję cztery razy. Wstawia Przykładowe wiersze danych z fragmentów.
3. [Witryny Azure portal](https://portal.azure.com) powinien być wyświetlony trzy nowe bazy danych na serwerze:

   ![Visual Studio potwierdzenia][2]

   W tym momencie zapytania wielu baz danych są obsługiwane za pomocą biblioteki klienta elastycznej bazy danych. Na przykład opcja 4 w oknie wiersza polecenia. Wyniki z wieloma fragmentami zapytania są zawsze **UNION ALL** wyników z wszystkimi fragmentami.

   W następnej sekcji zostanie utworzony punkt końcowy bazy danych przykładowych, który obsługuje bardziej zaawansowane przesyłanie zapytań dotyczących danych między fragmentami.

## <a name="create-an-elastic-query-database"></a>Utwórz bazę danych zapytania elastycznego
1. Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się.
2. Utwórz nową bazę danych Azure SQL, w tym samym serwerze, konfigurację fragmentu. Nazwa bazy danych "ElasticDBQuery."

    ![Witryna Azure portal i warstwy cenowej][3]

    > [!NOTE]
    > można użyć istniejącej bazy danych. Jeśli to zrobisz, nie może być jedną z fragmentów, które chcesz wykonać zapytania na. Ta baza danych będzie służyć do tworzenia obiektów metadanych zapytania elastycznej bazy danych.
    >

## <a name="create-database-objects"></a>Tworzenie obiektów bazy danych
### <a name="database-scoped-master-key-and-credentials"></a>W zakresie bazy danych — klucz główny i poświadczeń
Są one używane do nawiązania połączenia Menedżera mapowań fragmentów i fragmenty:

1. Otwórz program SQL Server Management Studio lub SQL Server Data Tools w programie Visual Studio.
2. Nawiązywanie połączenia ElasticDBQuery bazy danych i wykonaj następujące polecenia języka T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" i "password" powinna być taka sama jak informacje logowania, używane w kroku 6 procedury [pobieranie i uruchamianie aplikacji przykładowej](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) w [rozpoczęcie korzystania z narzędzi elastycznych baz danych](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Zewnętrzne źródła danych
Aby utworzyć zewnętrzne źródło danych, wykonaj następujące polecenie w bazie danych ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" jest nazwą mapy fragmentów, jeśli utworzono mapowania fragmentów i przy użyciu przykładu narzędzi elastycznej bazy danych Menedżera mapowań fragmentów. Jednak jeśli używasz niestandardowych ustawień dla tego przykładu, następnie należy nazwa mapy fragmentów, który został wybrany w aplikacji.

### <a name="external-tables"></a>Tabele zewnętrzne
Tworzenie tabeli zewnętrznej zgodną z tabeli Customers na fragmenty, wykonując następujące polecenie w bazie danych ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowego zapytania T-SQL elastycznej bazy danych
Po zdefiniowaniu w zewnętrznym źródle danych i tabel zewnętrznych można teraz używać pełnego języka T-SQL za pośrednictwem tabel zewnętrznych.

Wykonaj tę kwerendę w bazie danych ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Można zauważyć, że zapytanie agreguje wyniki z wszystkich fragmentów i daje następujące wyniki:

![Szczegóły danych wyjściowych][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importuj wyniki zapytania elastycznej bazy danych do programu Excel
 Można zaimportować wyników z zapytania do pliku programu Excel.

1. Launch Excel 2013.
2. Przejdź do **danych** wstążki.
3. Kliknij przycisk **z innych źródeł** i kliknij przycisk **z programu SQL Server**.

   ![Importowania z programu Excel z innych źródeł][5]
4. W **Kreatora połączenia danych** wpisz poświadczenia nazwy i nazwy logowania serwera. Następnie kliknij przycisk **Next** (Dalej).
5. W oknie dialogowym **wybierz bazę danych, która zawiera dane, które mają**, wybierz opcję **ElasticDBQuery** bazy danych.
6. Wybierz **klientów** tabeli w widoku listy, a następnie kliknij przycisk **dalej**. Następnie kliknij przycisk **Zakończ**.
7. W **importu danych** formularza, w obszarze **wybierz sposób wyświetlania tych danych w skoroszycie**, wybierz opcję **tabeli** i kliknij przycisk **OK**.

Wszystkie wiersze z **klientów** tabeli, przechowywane w różnych fragmentach wypełnianie arkusza programu Excel.

Można teraz używać funkcji wizualizacji zaawansowanych danych programu Excel. Parametry połączenia z nazwą serwera, nazwa bazy danych i poświadczeń służy do łączenia z narzędzi integracji danych i analizy Biznesowej w bazie danych zapytania elastycznego. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla swojego narzędzia. Może dotyczyć zapytanie elastyczne bazy danych i tabel zewnętrznych, podobnie jak każdej innej bazy danych programu SQL Server i tabel programu SQL Server, które można połączyć się z narzędziem.

### <a name="cost"></a>Koszty
Nie ma żadnych dodatkowych opłat za używanie funkcji zapytanie Elastic Database.

Aby uzyskać informacje o cenach, zobacz [SQL Database — szczegóły cennika](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Kolejne kroki

* Omówienie zapytanie elastyczne, zobacz [omówienie zapytania elastycznego](sql-database-elastic-query-overview.md).
* Pionowe samouczek partycjonowania, zobacz [rozpoczęcie pracy z bazami danych zapytań (partycje pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Składnia i przykładowe zapytania dotyczące danych partycjonowanych w pionie, można zobaczyć [zapytań w pionie partycjonowania danych)](sql-database-elastic-query-vertical-partitioning.md)
* Aby uzyskać składnię i przykładowe zapytania dotyczące danych partycjonowanej w poziomie, zobacz [zapytań w poziomie partycjonowania danych)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [sp\_wykonania \_zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury przechowywanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej usługi Azure SQL Database lub zestawu baz danych, służąc jako fragmentów w poziomie schematu partycjonowania.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
