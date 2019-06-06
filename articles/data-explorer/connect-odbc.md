---
title: Łączenie do Eksploratora danych platformy Azure z ODBC
description: W tym artykule dowiesz się, jak skonfigurować połączenie Open Database Connectivity (ODBC) do Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494843"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Łączenie do Eksploratora danych platformy Azure z ODBC

Otwórz połączenie z bazą danych ([ODBC](/sql/odbc/reference/odbc-overview)) są powszechnie akceptowane interfejsu programowania aplikacji (API) dla dostępu do bazy danych. Używanie ODBC nawiązać połączenia z Eksploratora danych platformy Azure z poziomu aplikacji, które nie mają dedykowanych łącznika.

W tle aplikacji wywołania funkcji w interfejsie ODBC, które są implementowane w modułach określonej bazy danych o nazwie *sterowniki*. Eksplorator danych usługi Azure obsługuje protokół komunikacyjny serwera SQL ([MS TDS](/azure/kusto/api/tds/)), dzięki czemu można użyć sterownika ODBC dla programu SQL Server.

W tym artykule dowiesz się, jak używać sterownika SQL Server ODBC do połączenia się z Eksploratora danych platformy Azure z poziomu dowolnej aplikacji, która obsługuje ODBC.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

* [Sterownik Microsoft ODBC dla programu SQL Server w wersji 17.2.0.1 lub nowszej](/sql/connect/odbc/download-odbc-driver-for-sql-server) systemu operacyjnego.

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

    ![Zintegrowane z usługą Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Następnie wybierz bazę danych z przykładowymi danymi **dalej**.

    ![Zmień domyślną bazę danych](media/connect-odbc/change-default-database.png)

1. Na następnym ekranie pozostawić wszystkie opcje domyślne wybierz **Zakończ**.

1. Wybierz **Testuj źródło danych**.

    ![Testuj źródło danych](media/connect-odbc/test-data-source.png)

1. Sprawdź, czy test powiodło się. następnie wybierz pozycję **OK**. Jeśli test nie powiedzie się, sprawdź wartości określone w poprzednich krokach, a następnie upewnij się, że masz wystarczające uprawnienia do łączenia z klastrem.

    ![Test zakończony pomyślnie](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Kolejne kroki

* [Nawiązywanie połączenia Eksploratora danych platformy Azure z Tableau](tableau.md)