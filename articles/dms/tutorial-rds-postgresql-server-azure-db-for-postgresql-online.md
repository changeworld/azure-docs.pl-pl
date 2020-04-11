---
title: 'Samouczek: Migrowanie usługi RDS PostgreSQL w trybie online do bazy danych platformy Azure dla postgreSQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z usługi RDS PostgreSQL do bazy danych Platformy Azure dla postgreSQL przy użyciu usługi migracji bazy danych azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: be6f0cd734d31f43557b49f8e9314e925b383899
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113946"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Samouczek: Migrowanie usługi RDS PostgreSQL do bazy danych platformy Azure dla postgreSQL w trybie online przy użyciu usługi DMS

Usługi migracji usługi Azure Database można użyć do migracji baz danych z wystąpienia postgreSQL usługi RDS do [bazy danych Azure Database dla postgreSQL,](https://docs.microsoft.com/azure/postgresql/) podczas gdy źródłowa baza danych pozostaje w trybie online podczas migracji. Innymi słowy migracji można osiągnąć przy minimalnym przestoju do aplikacji. W tym samouczku należy przeprowadzić migrację przykładowej bazy danych **usługi DVD Rental** z wystąpienia usługi RDS PostgreSQL 9.6 do bazy danych Azure database for PostgreSQL przy użyciu działania migracji online w usłudze migracji bazy danych platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migrowanie przykładowego schematu przy użyciu narzędzia pg_dump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu usługi migracji bazy danych platformy Azure.
> * Uruchamianie migracji.
> * Monitorowanie migracji.
> * Wykonaj cutover migracji.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service. Szyfrujemy dysk, aby zapobiec kradzieży danych podczas procesu migracji.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób przeprowadzania migracji online z lokalnego wystąpienia PostgreSQL do usługi Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj program [PostgreSQL Community Edition](https://www.postgresql.org/download/) w wersji 9.5, 9.6 lub 10. Źródłowy serwer PostgreSQL Server musi być w wersji 9.5.11, 9.6.7, 10 lub nowszej. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

   Należy również zauważyć, że docelowa usługa Azure Database dla wersji PostgreSQL musi być równa lub późniejsza niż wersja RDS PostgreSQL. Na przykład rds PostgreSQL 9.6 można migrować tylko do usługi Azure Database dla PostgreSQL 9.6, 10 lub 11, ale nie do bazy danych Azure dla PostgreSQL 9.5.

* Tworzenie wystąpienia [usługi Azure Database dla postgreSql](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) lub [usługi Azure Database dla postgreSQL — hiperskala (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal). Szczegółowe informacje na temat łączenia się z serwerem PostgreSQL przy użyciu programu pgAdmin można znaleźć w tej [sekcji](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) dokumentu.
* Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.
* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieciowej sieci nie blokują następujących portów komunikacji przychodzącej do usługi migracji bazy danych Azure: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę systemu Windows, aby zezwolić usłudze migracji bazy danych platformy Azure na dostęp do źródłowego serwera PostgreSQL, który domyślnie jest portem TCP 5432.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla serwera Usługi Azure Database for PostgreSQL, aby umożliwić dostęp usługi migracji do docelowych baz danych usługi Azure Database. Podaj zakres podsieci sieci wirtualnej używanej dla usługi migracji bazy danych Azure.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Konfigurowanie usługi AWS RDS PostgreSQL dla replikacji

1. Aby utworzyć nową grupę parametrów, postępuj zgodnie z instrukcjami dostarczonymi przez AWS w artykule [Praca z grupami parametrów bazy danych](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Główna nazwa użytkownika służy do łączenia się ze źródłem usługi migracji bazy danych Azure. Jeśli używasz konta innego niż główne konto użytkownika, konto musi mieć rolę rds_superuser i rds_replication rolę. Rola rds_replication udziela uprawnień do zarządzania logicznymi szczelinami i przesyłania strumieniowego danych przy użyciu gniazd logicznych.
3. Utwórz nową grupę parametrów z następującą konfiguracją:

    a. Ustaw parametr rds.logical_replication w grupie parametrów bazy danych na 1.

    b. max_wal_senders =[liczba równoczesnych zadań] — parametr max_wal_senders określa liczbę równoczesnych zadań, które można uruchamiać, zaleca 10 zadań.

    d. max_replication_slots – = [liczba slotów], polecam ustawić pięć slotów.

4. Skojarzyć utworzoną grupę parametrów z wystąpieniem RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Migrowanie schematu

1. Wyodrębnij schemat ze źródłowej bazy danych i zastosuj do docelowej bazy danych, aby zakończyć migrację wszystkich obiektów bazy danych, takich jak schematy tabeli, indeksy i procedury przechowywane.

    Najprostszym sposobem migracji tylko schematu jest użycie pg_dump z opcją -s. Aby uzyskać więcej informacji, zobacz [przykłady](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) w postgres pg_dump samouczek.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Na przykład, aby zrzucić plik schematu dla bazy danych **dvdrental,** należy użyć następującego polecenia:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Utwórz pustą bazę danych w usłudze docelowej, która jest usługą Azure Database for PostgreSQL. Aby połączyć się i utworzyć bazę danych, zapoznaj się z jednym z następujących artykułów:

    * [Tworzenie usługi Azure Database dla serwera PostgreSQL przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Tworzenie usługi Azure Database dla serwera PostgreSQL — hiperskali (Citus) przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. Zaimportuj schemat do usługi docelowej, która jest usługą Azure Database for PostgreSQL. Aby przywrócić plik zrzutu schematu, uruchom następujące polecenie:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Przykład:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem. Aby wyodrębnić skrypt klucza obcego upuszczenia i dodać skrypt klucza obcego w miejscu docelowym (Usługa Azure Database for PostgreSQL), uruchom następujący skrypt w PgAdmin lub psql:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Uruchom klucz obcy drop (który jest drugą kolumną) w wyniku kwerendy, aby usunąć klucz obcy.

6. Jeśli masz wyzwalacze (wstawić lub zaktualizować wyzwalacz) w danych, będzie wymusić integralność danych w miejscu docelowym przed replikowaniem danych ze źródła. Zalecenie jest, aby wyłączyć wyzwalacze we wszystkich tabelach *na miejsce docelowe* podczas migracji, a następnie włączyć wyzwalacze po zakończeniu migracji.

    Aby wyłączyć wyzwalacze w docelowej bazie danych:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi migracji bazy danych platformy Azure

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure.

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego wystąpienia PostgreSQL i docelowej bazy danych platformy Azure dla wystąpienia PostgreSQL.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową; Dla tej migracji online, należy wybrać warstwę cenową Premium: 4vCores.

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Na ekranie **Usługi migracji bazy danych Platformy Azure** wyszukaj nazwę utworzonego wystąpienia usługi migracji bazy danych Azure, wybierz wystąpienie, a następnie wybierz pozycję + Nowy projekt **migracji**.
3. Na ekranie **Nowy projekt migracji** określ nazwę projektu w polu **tekstowym Typ serwera źródłowego** wybierz pozycję **AWS RDS for PostgreSQL**, a następnie w polu **tekstowym Typ serwera docelowego** wybierz pozycję **Azure Database for PostgreSQL**.
4. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych online**.

    > [!IMPORTANT]
    > Pamiętaj, aby wybrać **migrację danych online;** Migracje w trybie offline nie są obsługiwane w tym scenariuszu.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternatywnie można wybrać **utwórz projekt tylko** do utworzenia projektu migracji teraz i wykonać migrację później.

5. Wybierz **pozycję Zapisz**.

6. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    > [!NOTE]
    > Proszę zanotować wymagania wstępne potrzebne do skonfigurowania migracji online w bloku tworzenia projektu.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na ekranie **Dodawanie szczegółów źródła** określ szczegóły połączenia dla źródłowego wystąpienia PostgreSQL.

   ![Szczegóły źródła](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz **pozycję Zapisz**, a następnie na ekranie Szczegóły **docelowe** określ szczegóły połączenia dla docelowej bazy danych platformy Azure dla serwera PostgreSQL, który jest wstępnie aprowizowany i ma schemat **wypożyczeń dysków DVD wdrożony** przy użyciu pg_dump.

    ![Szczegóły celu](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłowa baza danych, usługa migracji bazy danych Azure Database domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

    ![Stan działania — bieganie](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. W obszarze **NAZWA BAZY DANYCH**wybierz określoną bazę danych, aby uzyskać dostęp do stanu migracji dla operacji pełnego ładowania **danych** i **przyrostowej synchronizacji danych.**

    **Pełne obciążenie danych** pokazuje stan migracji obciążenia początkowego, podczas gdy **przyrostowa synchronizacja danych** pokazuje stan przechwytywania danych zmian (CDC).

    ![Ekran zapasów — pełne obciążenie danych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Ekran zapasów — przyrostowa synchronizacja danych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego obciążenia bazy danych są oznaczone **jako Gotowe do przecięcia.**

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Poczekaj, aż licznik **Oczekujące zmiany** pojawi się **0,** aby upewnić się, że wszystkie transakcje przychodzące do źródłowej bazy danych zostaną zatrzymane, zaznacz pole wyboru **Potwierdź,** a następnie wybierz pozycję **Zastosuj**.

    ![Kompletny ekran cutover](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Gdy stan migracji bazy danych pokazuje **zakończone,** połącz aplikacje z nową docelową bazą danych platformy Azure dla bazy danych PostgreSQL.

Migracja online lokalnego wystąpienia usługi RDS PostgreSQL do usługi Azure Database for PostgreSQL została zakończona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat usługi Azure Database for PostgreSQL, zobacz artykuł [Co to jest usługa Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* W przypadku innych pytań wyślij wiadomość e-mail [z aliasem Zapytaj o migracje bazy danych platformy Azure.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
