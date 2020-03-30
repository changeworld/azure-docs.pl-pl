---
title: Łączenie się z Eksploratorem danych platformy Azure za pomocą usługi ODBC
description: W tym artykule dowiesz się, jak skonfigurować połączenie open database connectivity (ODBC) z Eksploratorem danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034033"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Łączenie się z Eksploratorem danych platformy Azure za pomocą usługi ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) jest powszechnie akceptowanym interfejsem programowania aplikacji (API) dla dostępu do bazy danych. Użyj odbc, aby połączyć się z Eksploratorem danych platformy Azure z aplikacji, które nie mają dedykowanego łącznika.

Za kulisami aplikacje wywołują funkcje w interfejsie ODBC, które są implementowane w modułach specyficznych dla bazy danych zwanych *sterownikami*. Usługa Azure Data Explorer obsługuje podzbiór protokołu komunikacyjnego programu SQL Server[(MS-TDS),](/azure/kusto/api/tds/)dzięki czemu może używać sterownika ODBC dla programu SQL Server.

Korzystając z poniższego wideo, możesz nauczyć się tworzyć połączenie ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Alternatywnie można [skonfigurować źródło danych ODBC,](#configure-the-odbc-data-source) jak opisano poniżej. 

W tym artykule dowiesz się, jak używać sterownika ODBC programu SQL Server, dzięki czemu można połączyć się z Eksploratorem danych platformy Azure z dowolnej aplikacji obsługującej odbc. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

* [Sterownik odbc firmy Microsoft dla programu SQL Server w wersji 17.2.0.1 lub nowszej](/sql/connect/odbc/download-odbc-driver-for-sql-server) dla systemu operacyjnego.

## <a name="configure-the-odbc-data-source"></a>Konfigurowanie źródła danych ODBC

Wykonaj następujące kroki, aby skonfigurować źródło danych ODBC przy użyciu sterownika ODBC dla programu SQL Server.

1. W systemie Windows wyszukaj *pozycję Źródła danych ODBC*i otwórz aplikację klasyczną Źródła danych ODBC.

1. Wybierz pozycję **Dodaj**.

    ![Dodawanie źródła danych](media/connect-odbc/add-data-source.png)

1. Wybierz **sterownik ODBC 17 dla programu SQL Server,** a następnie **zakończ**.

    ![Wybierz sterownik](media/connect-odbc/select-driver.png)

1. Wprowadź nazwę i opis połączenia i klastra, z którego chcesz się połączyć, a następnie wybierz pozycję **Dalej**. Adres URL klastra powinien znajdować się w postaci * \<ClusterName\>.\< Region\>.kusto.windows.net*.

    ![Wybierz serwer](media/connect-odbc/select-server.png)

1. Wybierz **pozycję Zintegrowana z usługą Active Directory,** a następnie **dalej**.

    ![Zintegrowana usługa Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Wybierz bazę danych z przykładowymi danymi, a następnie **przycisk Dalej**.

    ![Zmienianie domyślnej bazy danych](media/connect-odbc/change-default-database.png)

1. Na następnym ekranie pozostaw wszystkie opcje jako domyślne, a następnie wybierz pozycję **Zakończ**.

1. Wybierz **pozycję Test Data Source**.

    ![Testowanie źródła danych](media/connect-odbc/test-data-source.png)

1. Sprawdź, czy test się powiódł, a następnie wybierz **przycisk OK**. Jeśli test nie powiódł się, sprawdź wartości określone w poprzednich krokach i upewnij się, że masz wystarczające uprawnienia do łączenia się z klastrem.

    ![Test zakończył się pomyślnie](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Następne kroki

* [Łączenie się z Eksploratorem danych platformy Azure z tableau](tableau.md)