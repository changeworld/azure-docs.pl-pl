---
title: Wizualizuj dane z Eksploratora danych platformy Azure przy użyciu programu Sisense
description: W tym artykule dowiesz się, jak skonfigurować Usługę Azure Data Explorer jako źródło danych dla Sisense i wizualizować dane.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358186"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Wizualizuj dane z usługi Azure Data Explorer w Sisense

Sisense to platforma analizy biznesowej, która umożliwia tworzenie aplikacji analitycznych, które zapewniają wysoce interaktywne środowisko użytkownika. Oprogramowanie do analizy biznesowej i raportowania pulpitu nawigacyjnego umożliwia dostęp do danych i łączenie ich za pomocą kilku kliknięć. Można łączyć się ze strukturalnymi i nieustrukturyzowanymi źródłami danych, dołączać do tabel z wielu źródeł przy minimalnym skryptowaniu i kodowaniu oraz tworzyć interaktywne pulpity nawigacyjne i raporty sieci Web. W tym artykule dowiesz się, jak skonfigurować Eksploratora danych platformy Azure jako źródło danych dla Sisense i wizualizować dane z przykładowego klastra.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebujesz następujących czynności:

* [Pobieranie i instalowanie aplikacji Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Utwórz klaster i bazę danych zawierającą przykładowe dane StormEvents. Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md) oraz pozyskiwania [przykładowych danych do Eksploratora danych platformy Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Łączenie się z pulpitami nawigacyjnymi Sisense przy użyciu łącznika JDBC usługi Azure Data Explorer

1. Pobierz i skopiuj najnowsze wersje następujących plików jar do *.. \Sisense\Połączenia danych\jdbcdrivers\adx* 

    * aktywacja-1.1.słoik
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.słoik
    * jcip-adnotacje-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * poczta-1.4.7.słoik
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Otwórz aplikację **Sisense.**
1. Wybierz kartę **Dane** i wybierz **+ElastiCube,** aby utworzyć nowy model ElastiCube.
    
    ![Wybierz ElastiCube](media/sisense/data-select-elasticube.png)

1. W **obszarze Dodaj nowy model ElastiCube**nazwij model ElastiCube i **Zapisz**.
   
    ![Dodaj nowy model ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Wybierz **+ Dane**.

    ![Przycisk Wybierz dane](media/sisense/select-data.png)

1. Na karcie **Wybierz łącznik** wybierz ogólny łącznik **JDBC.**

    ![Wybieranie złącza JDBC](media/sisense/select-connector.png)

1. Na karcie **Połącz** wypełnij następujące pola dla ogólnego łącznika **JDBC** i wybierz pozycję **Dalej**.

    ![Ustawienia złącza JDBC](media/sisense/jdbc-connector.png)

    |Pole |Opis |
    |---------|---------|
    |Ciąg połączenia     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Folder JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nazwa klasy kierowcy    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Nazwa użytkownika   |    Nazwa użytkownika AAD     |
    |Hasło     |   Hasło użytkownika AAD      |

1. Na karcie **Wybieranie danych** wyszukaj **pozycję Wybierz bazę danych,** aby wybrać odpowiednią bazę danych, do której masz uprawnienia. W tym przykładzie wybierz *test1*.

    ![wybierz bazę danych](media/sisense/select-database.png)

1. W *okienku test* (nazwa bazy danych):
    1. Wybierz nazwę tabeli, aby wyświetlić podgląd tabeli i wyświetlić nazwy kolumn tabeli. Można usunąć niepotrzebne kolumny.
    1. Zaznacz pole wyboru odpowiedniej tabeli, aby zaznaczyć tę tabelę. 
    1. Wybierz pozycję **Done** (Gotowe).

    ![wybieranie tabeli](media/sisense/select-table-see-columns.png)    

1. Wybierz **pozycję Kompilacja,** aby utworzyć zestaw danych. 

    * W oknie **Kompilacja** wybierz pozycję **Buduj**.

      ![Okno kompilacji](media/sisense/build-window.png)

    * Poczekaj, aż proces kompilacji zostanie zakończony, a następnie wybierz **opcję Kompilacja powiodła się**.

      ![Kompilacja powiodła się](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Tworzenie pulpitów nawigacyjnych Sisense

1. Na karcie **Analytics** wybierz **+**  >  **pozycję Nowy pulpit nawigacyjny,** aby utworzyć pulpity nawigacyjne w tym zestawie danych.

    ![Nowy pulpit nawigacyjny](media/sisense/new-dashboard.png)

1. Wybierz pulpit nawigacyjny i wybierz pozycję **Utwórz**. 

    ![Tworzenie pulpitu nawigacyjnego](media/sisense/create-dashboard.png)

1. W obszarze **Nowy widżet**wybierz **pozycję + Wybierz dane,** aby utworzyć nowy widżet. 

    ![Dodawanie pól do pulpitu nawigacyjnego StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Wybierz **+ Dodaj więcej danych,** aby dodać dodatkowe kolumny do wykresu. 

    ![Dodawanie większej ilości danych do wykresu](media/sisense/add-more-data.png)

1. Wybierz **+ Widget,** aby utworzyć kolejny widżet. Przeciągnij i upuść widżety, aby zmienić rozmieszczenie pulpitu nawigacyjnego.

    ![Pulpit nawigacyjny Storm](media/sisense/final-dashboard.png)

Teraz możesz eksplorować swoje dane za pomocą analizy wizualnej, tworzyć dodatkowe pulpity nawigacyjne i przekształcać dane w użyteczne statystyki, aby mieć wpływ na twoją firmę.

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla Eksploratora danych platformy Azure](write-queries.md)

