---
title: Łączenie do Eksploratora danych platformy Azure z ODBC
description: W tym instruktażu dowiesz się, jak skonfigurować połączenia ODBC do Eksploratora danych platformy Azure, a następnie wizualizowanie danych za pomocą Tableau za pomocą tego połączenia.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: d01c825e50e30e3545a0d47e432835c658d677af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60448456"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Łączenie do Eksploratora danych platformy Azure z ODBC

Otwórz połączenie z bazą danych ([ODBC](/sql/odbc/reference/odbc-overview)) są powszechnie akceptowane interfejsu programowania aplikacji (API) dla dostępu do bazy danych. Używanie ODBC nawiązać połączenia z Eksploratora danych platformy Azure z poziomu aplikacji, które nie mają dedykowanych łącznika.

W tle aplikacji wywołania funkcji w interfejsie ODBC, które są implementowane w modułach określonej bazy danych o nazwie *sterowniki*. Eksplorator danych usługi Azure obsługuje protokół komunikacyjny serwera SQL ([MS TDS](/azure/kusto/api/tds/)); w związku z tym może użyć sterownika ODBC dla programu SQL Server.

W tym artykule dowiesz się, jak używać sterownika SQL Server ODBC do połączenia się z Eksploratora danych platformy Azure z poziomu dowolnej aplikacji, która obsługuje ODBC. Można opcjonalnie połączyć do Eksploratora danych platformy Azure z Tableau i wyświetlić dane z klastra z próbki.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące polecenie, aby ukończyć ten sposób:

* [Sterownik Microsoft ODBC dla programu SQL Server w wersji 17.2.0.1 lub nowszej](/sql/connect/odbc/download-odbc-driver-for-sql-server) systemu operacyjnego.

* Jeśli chcesz postępuj zgodnie z naszym przykładzie Tableau, należy również:

  * TABLEAU pulpitu, pełny lub [wersji próbnej](https://www.tableau.com/products/desktop/download) wersji.

  * Klastra, który zawiera StormEvents przykładowych danych. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](create-cluster-database-portal.md) i [pozyskiwanie danych przykładowych do Eksploratora danych usługi Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Konfigurowanie źródła danych ODBC

Wykonaj następujące kroki, aby skonfigurować źródła danych ODBC, za pomocą sterownika ODBC dla programu SQL Server.

1. W Windows, wyszukaj *źródeł danych ODBC*, a następnie otwórz aplikację klasyczną źródeł danych ODBC.

1. Wybierz pozycję **Dodaj**.

    ![Dodawanie źródła danych](media/connect-odbc/add-data-source.png)

1. Wybierz **ODBC Driver 17 dla programu SQL Server** następnie **Zakończ**.

    ![Wybierz sterownik](media/connect-odbc/select-driver.png)

1. Wprowadź nazwę i opis dla połączenia i klaster, który ma się połączyć, następnie wybierz **dalej**. Klaster, adres URL powinien mieć postać  *\<ClusterName\>.\< Region\>. kusto.windows.net*.

    ![Wybierz serwer](media/connect-odbc/select-server.png)

1. Wybierz **zintegrowane usługi Active Directory** następnie **dalej**.

    ![Active Directory — zintegrowana](media/connect-odbc/active-directory-integrated.png)

1. Następnie wybierz bazę danych z przykładowymi danymi **dalej**.

    ![Zmień domyślną bazę danych](media/connect-odbc/change-default-database.png)

1. Na następnym ekranie pozostawić wszystkie opcje domyślne wybierz **Zakończ**.

1. Wybierz **Testuj źródło danych**.

    ![Testuj źródło danych](media/connect-odbc/test-data-source.png)

1. Sprawdź, czy test powiodło się. następnie wybierz pozycję **OK**. Jeśli test nie powiedzie się, sprawdź wartości określone w poprzednich krokach, a następnie upewnij się, że masz wystarczające uprawnienia do łączenia z klastrem.

    ![Test zakończony pomyślnie](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Wizualizowanie danych w Tableau (opcjonalnie)

Po zakończeniu konfigurowania ODBC, przykładowe dane można zapewnić Tableau.

1. Na pulpicie Tableau, w menu po lewej stronie wybierz **inne bazy danych (ODBC)**.

    ![Łączenie z bazą danych ODBC](media/connect-odbc/connect-odbc.png)

1. Aby uzyskać **DSN**, wybierz źródło danych utworzone dla ODBC, a następnie wybierz **Sign In**.

    ![Logowanie w ODBC](media/connect-odbc/odbc-sign-in.png)

1. Aby uzyskać **bazy danych**, wybierz bazę danych w klastrze przykładowe, takie jak *TestDatabase*. Dla **schematu**, wybierz opcję *dbo*oraz **tabeli**, wybierz opcję *StormEvents* przykładowej tabeli.

    ![Wybierz bazę danych i tabelę](media/connect-odbc/select-database-table.png)

1. TABLEAU są teraz wyświetlane schematu dla przykładowych danych. Wybierz **Aktualizuj teraz** do przenoszenia danych do firmy Tableau.

    ![Aktualizowanie danych](media/connect-odbc/update-data.png)

    Po zaimportowaniu danych Tableau zawiera wiersze danych podobny do poniższej ilustracji.

    ![Zestaw wyników](media/connect-odbc/result-set.png)

1. Teraz możesz utworzyć wizualizacje w Tableau, w oparciu o dane pochodzące z Eksploratora danych usługi Azure. Aby uzyskać więcej informacji, zobacz [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Kolejne kroki

[Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)

[Samouczek: Wizualizuj dane z Eksploratora danych usługi Azure w usłudze Power BI](visualize-power-bi.md)