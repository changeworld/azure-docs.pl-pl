---
title: Wizualizowanie danych przy użyciu Sisense Eksploratora danych usługi Azure
description: W tym artykule Dowiedz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Sisense i wizualizacji danych.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358186"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Wizualizuj dane z Eksploratora danych platformy Azure w Sisense

Sisense jest platformę analizy biznesowej analizy, która umożliwia tworzenie aplikacji analizy, które zapewniają środowiska użytkowników wysoce interakcyjny. Analiza biznesowa i pulpitów nawigacyjnych, raportów oprogramowania pozwala na dostęp i łączenie danych za pomocą kilku kliknięć. Można połączyć z danych strukturyzowanych i niestrukturyzowanych źródeł, dołączanie tabel z wielu źródeł, z minimalnym skryptów i kodu i utworzyć web interaktywnych pulpitów nawigacyjnych i raportów. W tym artykule dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Sisense i wizualizuj dane z klastra z próbki.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące polecenie, aby zakończyć w tym artykule:

* [Pobierz i zainstaluj aplikację Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Tworzenie klastra i baza danych zawierająca StormEvents przykładowych danych. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](create-cluster-database-portal.md) i [pozyskiwanie danych przykładowych do Eksploratora danych usługi Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Nawiązać połączenie z Sisense pulpitów nawigacyjnych przy użyciu łącznika JDBC Eksploratora danych platformy Azure

1. Pobierz i skopiuj następujące pliki jar do najnowszych wersji *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * Commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip adnotacje — 1.0 — 1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Otwórz **Sisense** aplikacji.
1. Wybierz **danych** kartę, a następnie wybierz pozycję **+ ElastiCube** do utworzenia nowego modelu ElastiCube.
    
    ![Wybierz ElastiCube](media/sisense/data-select-elasticube.png)

1. W **Dodaj nowy ElastiCube Model**, nazwę modelu ElastiCube i **Zapisz**.
   
    ![Dodaj nowy model ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Wybierz **+ dane**.

    ![Wybierz przycisk danych](media/sisense/select-data.png)

1. W **wybierz łącznik** , a następnie wybierz **ogólny JDBC** łącznika.

    ![Wybierz łącznik JDBC](media/sisense/select-connector.png)

1. W **Connect** karcie, wykonaj następujące pola **ogólny JDBC** łącznika, a następnie wybierz pozycję **dalej**.

    ![Ustawienia łącznika JDBC](media/sisense/jdbc-connector.png)

    |Pole |Opis |
    |---------|---------|
    |Parametry połączenia     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Folder JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nazwa klasy sterownika    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Nazwa użytkownika   |    Nazwa użytkownika usługi AAD     |
    |Hasło     |   Hasło użytkownika usługi AAD      |

1. W **wybierz dane** pozycję Wyszukaj **wybierz bazę danych** do wybrania odpowiedniej bazy danych, do których masz uprawnienia. W tym przykładzie wybierz *test1*.

    ![Wybierz bazę danych](media/sisense/select-database.png)

1. W *test* okienko (nazwa bazy danych):
    1. Wybierz nazwę tabeli, aby wyświetlić podgląd tabeli i wyświetlić nazwy kolumn w tabeli. Możesz usunąć zbędne kolumny.
    1. Zaznacz pole wyboru odpowiedniej tabeli, aby wybrać tej tabeli. 
    1. Wybierz pozycję **Done** (Gotowe).

    ![Wybierz tabelę](media/sisense/select-table-see-columns.png)    

1. Wybierz **kompilacji** do tworzenia zestawu danych. 

    * W **kompilacji** wybierz **kompilacji**.

      ![Tworzenie okien](media/sisense/build-window.png)

    * Poczekaj, aż proces kompilacji jest pełna, a następnie wybierz pozycję **kompilacji zakończyło się pomyślnie**.

      ![Kompilacja powiodła się](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Tworzenie pulpitów nawigacyjnych Sisense

1. W **Analytics** zaznacz **+**  >  **nowy pulpit nawigacyjny** tworzyć pulpity nawigacyjne w tym zestawie danych.

    ![Nowy pulpit nawigacyjny](media/sisense/new-dashboard.png)

1. Wybierz pulpit nawigacyjny i wybierz **Utwórz**. 

    ![Tworzenie pulpitu nawigacyjnego](media/sisense/create-dashboard.png)

1. W obszarze **nowy widżet**, wybierz opcję **+ wybierz danych** do utworzenia nowego elementu widget. 

    ![Dodawanie pól do pulpitu nawigacyjnego StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Wybierz **+ Dodaj więcej danych** można dodać dodatkowe kolumny do grafu. 

    ![Dodawanie większej ilości danych do wykresu](media/sisense/add-more-data.png)

1. Wybierz **+ widżet** do utworzenia w innym elemencie widget. Przeciągnij i upuść elementy widget służące do zmiany rozmieszczenia pulpitu nawigacyjnego.

    ![Pulpit nawigacyjny Storm](media/sisense/final-dashboard.png)

Można teraz Eksplorowanie danych za pomocą analizę graficzną, tworzyć dodatkowe pulpity nawigacyjne i przekształcić dane w szczegółowe informacje, aby wpływ na działalność.

## <a name="next-steps"></a>Kolejne kroki

* [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)

