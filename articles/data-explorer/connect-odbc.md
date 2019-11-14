---
title: Nawiązywanie połączenia z usługą Azure Eksplorator danych za pomocą ODBC
description: W tym artykule dowiesz się, jak skonfigurować połączenie Open Database Connectivity (ODBC) z usługą Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034033"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Nawiązywanie połączenia z usługą Azure Eksplorator danych za pomocą ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) to powszechnie akceptowany interfejs programowania aplikacji (API) na potrzeby dostępu do bazy danych. Użyj ODBC, aby nawiązać połączenie z usługą Azure Eksplorator danych z aplikacji, które nie mają dedykowanego łącznika.

W tle aplikacje wywołują funkcje w interfejsie ODBC, które są zaimplementowane w modułach specyficznych dla bazy danych o nazwie *Drivers*. Usługa Azure Eksplorator danych obsługuje podzestaw protokołu komunikacyjnego SQL Server ([MS-TDS](/azure/kusto/api/tds/)), dlatego można użyć sterownika ODBC do SQL Server.

Korzystając z następującego wideo, możesz nauczyć się, jak utworzyć połączenie ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Alternatywnie można [skonfigurować źródło danych ODBC](#configure-the-odbc-data-source) , jak opisano poniżej. 

W tym artykule dowiesz się, jak używać sterownika SQL Server ODBC, dzięki czemu możesz łączyć się z usługą Azure Eksplorator danych z dowolnej aplikacji obsługującej ODBC. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

* [Microsoft ODBC Driver for SQL Server wersję 17.2.0.1 lub nowszą](/sql/connect/odbc/download-odbc-driver-for-sql-server) dla danego systemu operacyjnego.

## <a name="configure-the-odbc-data-source"></a>Konfigurowanie źródła danych ODBC

Wykonaj następujące kroki, aby skonfigurować źródło danych ODBC przy użyciu sterownika ODBC dla SQL Server.

1. W systemie Windows wyszukaj pozycję *źródła danych ODBC*i Otwórz aplikację klasyczne źródła danych ODBC.

1. Wybierz pozycję **Dodaj**.

    ![Dodawanie źródła danych](media/connect-odbc/add-data-source.png)

1. Wybierz pozycję **sterownik ODBC 17 dla SQL Server** następnie kliknij przycisk **Zakończ**.

    ![Wybierz sterownik](media/connect-odbc/select-driver.png)

1. Wprowadź nazwę i opis połączenia oraz klaster, z którym chcesz się połączyć, a następnie wybierz przycisk **dalej**. Adres URL klastra powinien mieć postać *\<clustername\>.\<Region\>. Kusto.Windows.NET*.

    ![Wybierz serwer](media/connect-odbc/select-server.png)

1. Wybierz pozycję **Active Directory zintegrowane** , a następnie kliknij przycisk **dalej**.

    ![Active Directory zintegrowany](media/connect-odbc/active-directory-integrated.png)

1. Wybierz bazę danych z przykładowymi danymi, a następnie kliknij przycisk **dalej**.

    ![Zmień domyślną bazę danych](media/connect-odbc/change-default-database.png)

1. Na następnym ekranie pozostaw wszystkie opcje jako domyślne, a następnie wybierz pozycję **Zakończ**.

1. Wybierz **test źródło danych**.

    ![Test źródła danych](media/connect-odbc/test-data-source.png)

1. Sprawdź, czy test zakończył się pomyślnie, a następnie wybierz przycisk **OK**. Jeśli test nie powiódł się, sprawdź wartości określone w poprzednich krokach i upewnij się, że masz wystarczające uprawnienia do nawiązania połączenia z klastrem.

    ![Test powiódł się](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z usługą Eksplorator danych Azure z usługi Tableau](tableau.md)