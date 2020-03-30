---
title: Raport w powiększonych na poziomie bazach danych w chmurze
description: Użyj kwerend bazy danych między bazami danych do raportowania w wielu bazach danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: bad52b364dc83994e7985fc80b1b9f9e7f50481e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823775"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Raport w powiększonych na poziomie bazach danych w chmurze (wersja zapoznawcza)

Raporty można tworzyć z wielu baz danych SQL platformy Azure z jednego punktu połączenia za pomocą [zapytania elastycznego](sql-database-elastic-query-overview.md). Bazy danych muszą być podzielone poziomo na partycje (znane również jako "podzielone na fragmenty").

Jeśli masz istniejącą bazę danych, zobacz [Migrowanie istniejących baz danych do skalowane w poziomie baz danych](sql-database-elastic-convert-to-use-elastic-tools.md).

Aby zrozumieć obiekty SQL potrzebne do wykonywania zapytań, zobacz [Kwerenda w poziomych partycjonowanych bazach danych](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz i uruchom [przykład narzędzi Wprowadzenie do elastycznej bazy danych](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Tworzenie menedżera map fragmentów przy użyciu przykładowej aplikacji
W tym miejscu utworzysz menedżera mapy niezależnego fragmentu wraz z kilkoma fragmentami, a następnie wstawianie danych do fragmentów. Jeśli zdarzy ci się już skonfigurować fragmenty z podzielonymi danymi, możesz pominąć następujące kroki i przejść do następnej sekcji.

1. Tworzenie i uruchamianie przykładowej aplikacji **Wprowadzenie do narzędzi elastycznej bazy danych,** wykonując kroki opisane w sekcji Artykuł [Pobierz i uruchom przykładową aplikację](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app-1). Po zakończeniu wszystkich kroków zostanie wyświetlony następujący wiersz polecenia:

    ![wiersz polecenia][1]
2. W oknie polecenia wpisz "1" i naciśnij **klawisz Enter**. Spowoduje to utworzenie menedżera mapy niezależnego fragmentu i dodaje dwa fragmenty do serwera. Następnie wpisz "3" i naciśnij **klawisz Enter**; powtórz czynność cztery razy. Spowoduje to wstawienie przykładowych wierszy danych do fragmentów.
3. Portal [Azure](https://portal.azure.com) powinien wyświetlać trzy nowe bazy danych na serwerze:

   ![Potwierdzenie programu Visual Studio][2]

   W tym momencie kwerendy między bazami danych są obsługiwane za pośrednictwem bibliotek klienta elastycznej bazy danych. Na przykład użyj opcji 4 w oknie polecenia. Wyniki z kwerendy wielosprzężowego są zawsze **UNION ALL** wyników ze wszystkich fragmentów.

   W następnej sekcji tworzymy przykładowy punkt końcowy bazy danych, który obsługuje bogatsze zapytania danych między fragmentami.

## <a name="create-an-elastic-query-database"></a>Tworzenie elastycznej bazy danych zapytań
1. Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się.
2. Utwórz nową bazę danych SQL platformy Azure na tym samym serwerze co konfiguracja niezależnego fragmentu. Nazwij bazę danych "ElasticDBQuery".

    ![Witryna Portalu Platformy Azure i warstwa cenowa][3]

    > [!NOTE]
    > można użyć istniejącej bazy danych. Jeśli można to zrobić, nie może być jednym z fragmentów, które chcesz wykonać zapytania na. Ta baza danych będzie używana do tworzenia obiektów metadanych dla zapytania elastycznej bazy danych.
    >

## <a name="create-database-objects"></a>Tworzenie obiektów bazy danych
### <a name="database-scoped-master-key-and-credentials"></a>Klucz główny i poświadczenia o zakresie bazy danych
Są one używane do łączenia się z menedżerem mapy niezależnego fragmentu i fragmentami:

1. Otwórz program SQL Server Management Studio lub SQL Server Data Tools w programie Visual Studio.
2. Połącz się z bazą danych ElasticDBQuery i wykonaj następujące polecenia T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "nazwa użytkownika" i "hasło" powinny być takie same jak informacje logowania używane w kroku 3 sekcji [Pobierz i uruchom przykładową aplikację](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) w artykule Wprowadzenie do narzędzi **elastycznej bazy danych.**

### <a name="external-data-sources"></a>Zewnętrzne źródła danych
Aby utworzyć zewnętrzne źródło danych, wykonaj następujące polecenie w bazie danych ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" to nazwa mapy niezależnego fragmentu, jeśli utworzono mapę niezależnego fragmentu i menedżera map niezależnego fragmentu przy użyciu przykładu narzędzi elastycznej bazy danych. Jeśli jednak użyto konfiguracji niestandardowej dla tego przykładu, powinna to być nazwa mapy niezależnego fragmentu wybrana w aplikacji.

### <a name="external-tables"></a>Tabele zewnętrzne
Utwórz tabelę zewnętrzną, która pasuje do tabeli Klienci na fragmentach, wykonując następujące polecenie w bazie danych ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowej kwerendy T-SQL elastycznej bazy danych
Po zdefiniowaniu zewnętrznego źródła danych i tabel zewnętrznych można teraz używać pełnego języka T-SQL w tabelach zewnętrznych.

Wykonaj tę kwerendę w bazie danych ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Można zauważyć, że kwerenda agreguje wyniki ze wszystkich fragmentów i daje następujące dane wyjściowe:

![Szczegóły wydruku][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importowanie wyników kwerendy elastycznej bazy danych do programu Excel
 Wyniki kwerendy można zaimportować do pliku programu Excel.

1. Uruchamianie programu Excel 2013.
2. Przejdź do wstążki **Dane.**
3. Kliknij **pozycję Z innych źródeł** i kliknij pozycję Z programu SQL **Server**.

   ![Importowanie programu Excel z innych źródeł][5]
4. W **Kreatorze połączenia danych** wpisz nazwę serwera i dane logowania. Następnie kliknij przycisk **Dalej**.
5. W oknie dialogowym **Wybierz bazę danych zawierającą żądane dane**, wybierz bazę danych **ElasticDBQuery.**
6. Wybierz tabelę **Klienci** w widoku listy i kliknij przycisk **Dalej**. Następnie kliknij przycisk **Zakończ**.
7. W formularzu **Importowanie danych** w obszarze **Wybieranie sposobu wyświetlania tych danych w skoroszycie**wybierz pozycję **Tabela** i kliknij przycisk **OK**.

Wszystkie wiersze z tabeli **Klienci,** przechowywane w różnych fragmentach wypełniają arkusz programu Excel.

Teraz można korzystać z zaawansowanych funkcji wizualizacji danych programu Excel. Za pomocą ciągu połączenia z nazwą serwera, nazwą bazy danych i poświadczeniami można połączyć narzędzia analizy biznesowej i integracji danych z bazą danych zapytań elastycznych. Upewnij się, że program SQL Server jest obsługiwany jako źródło danych dla narzędzia. Można odwoływać się do bazy danych zapytań elastycznych i tabel zewnętrznych, podobnie jak do innych tabel bazy danych programu SQL Server i programu SQL Server, z którymi można się połączyć za pomocą narzędzia.

### <a name="cost"></a>Koszty
Nie ma żadnych dodatkowych opłat za korzystanie z funkcji zapytania elastycznej bazy danych.

Aby uzyskać informacje o cenach, zobacz [Szczegóły cen bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem kwerendy elastycznej, zobacz [omówienie kwerendy elastycznej](sql-database-elastic-query-overview.md).
* Aby uzyskać samouczek partycjonowania pionowego, zobacz [Wprowadzenie do kwerendy między bazami danych (partycjonowanie pionowe)](sql-database-elastic-query-getting-started-vertical.md).
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych pionowo, zobacz [Wyszukiwanie danych podzielonych pionowo na partycje)](sql-database-elastic-query-vertical-partitioning.md)
* Aby uzyskać składnię i przykładowe kwerendy dotyczące danych podzielonych poziomo na partycje, zobacz Wykonywanie zapytań o [dane podzielone na partycje w poziomie)](sql-database-elastic-query-horizontal-partitioning.md)
* Zobacz [\_sp \_wykonać zdalnego](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję Transact-SQL na jednej zdalnej bazy danych SQL azure lub zestaw baz danych służących jako fragmenty w schemat partycjonowania poziomego.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
