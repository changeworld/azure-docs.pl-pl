---
title: 'Samouczek: Migrowanie postgreSQL do bazy danych usługi Azure dla postgreSQL online za pośrednictwem witryny Azure portal'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z postgreSQL lokalnie do bazy danych Platformy Azure dla postgreSQL przy użyciu usługi migracji bazy danych Azure za pośrednictwem witryny Azure portal.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 4985c492c8ca71da87cf1a519ebc658c203d3952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246980"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Samouczek: Migrowanie postgreSQL do bazy danych usługi Azure dla usługi PostgreSQL w trybie online przy użyciu usługi DMS za pośrednictwem portalu Azure

Usługi migracji bazy danych platformy Azure można użyć do migracji baz danych z lokalnego wystąpienia PostgreSQL do [bazy danych Azure Database dla postgreSQL](https://docs.microsoft.com/azure/postgresql/) przy minimalnym przestoju do aplikacji. W tym samouczku należy przeprowadzić migrację przykładowej bazy danych **usługi DVD Rental** z lokalnego wystąpienia PostgreSQL 9.6 do bazy danych Azure Database for PostgreSQL przy użyciu działania migracji online w usłudze migracji bazy danych platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migrowanie przykładowego schematu przy użyciu narzędzia pg_dump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji w usłudze migracji bazy danych platformy Azure.
> * Uruchamianie migracji.
> * Monitorowanie migracji.
> * Wykonaj cutover migracji.

> [!NOTE]
> Korzystanie z usługi migracji bazy danych Azure do wykonywania migracji online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Szyfrujemy dysk, aby zapobiec kradzieży danych podczas procesu migracji

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca utworzenie wystąpienia usługi migracji bazy danych platformy Azure w tym samym regionie platformy Azure co docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj [wersję społeczności PostgreSQL](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 lub 10. Źródło postgreSql wersja serwera musi być 9.4, 9.5, 9.6, 10 lub 11. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Ponadto lokalna wersja programu PostgreSQL musi być zgodna z wersją usługi Azure Database for PostgreSQL. Na przykład PostgreSQL 9.6 można migrować tylko do usługi Azure Database dla PostgreSQL 9.6, 10 lub 11, ale nie do bazy danych Azure dla PostgreSQL 9.5.

* [Utwórz usługę Azure Database dla serwera PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) lub [utwórz usługę Azure Database dla serwera PostgreSQL — hiperskali (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.

    > [!NOTE]
    > Podczas konfigurowania sieci wirtualnej, jeśli używasz usługi ExpressRoute z komunikacją równorzędną sieciową do firmy Microsoft, dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usługi do podsieci, w której usługa będzie aprowizowana:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, punkt końcowy usługi Cosmos DB itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy magistrali usług
    >
    > Ta konfiguracja jest konieczna, ponieważ usługa migracji bazy danych platformy Azure nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń (NSG) dla sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do usługi migracji bazy danych Azure: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę systemu Windows, aby zezwolić usłudze migracji bazy danych platformy Azure na dostęp do źródła Serwera PostgreSQL, który domyślnie jest portem TCP 5432.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla usługi Azure Database for PostgreSQL, aby umożliwić usłudze migracji bazy danych platformy Azure dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi migracji bazy danych Azure.
* Włącz replikację logiczną w pliku postgresql.config i ustaw następujące parametry:

  * wal_level = **logical**
  * max_replication_slots = [liczba slotów], zaleca ustawienie **na pięć slotów**
  * max_wal_senders = [liczba współbieżnych zadań] — parametr max_wal_senders ustawia liczbę współbieżnych zadań, które można uruchomić, przy czym zalecane ustawienie to **10 zadań**

> [!IMPORTANT]
> Wszystkie tabele w istniejącej bazie danych potrzebują klucza podstawowego, aby upewnić się, że zmiany mogą być synchronizowane z docelową bazą danych.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych.

1. Użyj polecenia -s pg_dump, aby utworzyć plik zrzutu schematu dla bazy danych.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Na przykład, aby utworzyć plik zrzutu schematu dla bazy danych **dvdrental:**

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Aby uzyskać więcej informacji na temat korzystania z narzędzia pg_dump, zobacz przykłady w samouczku narzędzia [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Utwórz pustą bazę danych w swoim środowisku docelowym, czyli w usłudze Azure Database for PostgreSQL.

    Aby uzyskać szczegółowe informacje na temat łączenia i tworzenia bazy danych, zobacz artykuł [Tworzenie usługi Azure Database dla serwera PostgreSQL w witrynie Azure portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) lub Tworzenie bazy danych platformy Azure dla serwera [PostgreSQL — Hiperskala (Citus) w witrynie Azure portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Wystąpienie usługi Azure Database for PostgreSQL — Hiperskala (Citus) ma tylko jedną bazę danych: **citus**.

3. Zaimportuj schemat do docelowej bazy danych utworzonej przez przywrócenie pliku zrzutu schematu.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Przykład:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Aby wyodrębnić skrypt klucza obcego upuszczania i dodać go w miejscu docelowym (Usługa Azure Database for PostgreSQL), w PgAdmin lub psql, uruchom następujący skrypt.

   > [!IMPORTANT]
   > Klucze obce w schemacie spowoduje początkowe obciążenie i ciągłej synchronizacji migracji zakończyć się niepowodzeniem.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.

6. Aby wyłączyć wyzwalacze w docelowej bazie danych, uruchom poniższy skrypt.

   > [!IMPORTANT]
   > Wyzwalacze (wstawianie lub aktualizowanie) w danych wymuszają integralność danych w docelowych przed dane są replikowane ze źródła. W związku z tym zaleca się wyłączenie wyzwalaczy we wszystkich tabelach **w miejscu docelowym** podczas migracji, a następnie ponowne włączenie wyzwalaczy po zakończeniu migracji.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na ekranie **Utwórz usługę migracji** określ nazwę, subskrypcję, nową lub istniejącą grupę zasobów oraz lokalizację usługi.

4. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego serwera PostgreSQL i docelowej bazy danych platformy Azure dla wystąpienia PostgreSQL.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Wybierz **opcję Przejrzyj + utwórz,** aby utworzyć usługę.

   Tworzenie usługi zakończy się w ciągu około 10 do 15 minut.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdź wszystkie wystąpienia usługi migracji bazy danych platformy Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na ekranie **Usługi migracji bazy danych Azure** wyszukaj nazwę utworzonego wystąpienia usługi migracji bazy danych Azure, wybierz wystąpienie, a następnie wybierz + Nowy projekt **migracji**.

3. Na ekranie **Nowy projekt migracji** określ nazwę projektu w polu **tekstowym Typ serwera źródłowego** wybierz pozycję **PostgresSQL**, w polu **tekstowym Typ serwera docelowego** wybierz pozycję **Azure Database for PostgreSQL**.

4. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych online**.

    ![Tworzenie projektu usługi migracji bazy danych platformy Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternatywnie można wybrać **utwórz projekt tylko** do utworzenia projektu migracji teraz i wykonać migrację później.

5. Wybierz **pozycję Zapisz**, zwróć uwagę na wymagania dotyczące pomyślnego użycia usługi migracji bazy danych Azure do migracji danych, a następnie wybierz pozycję **Utwórz i uruchom aktywność**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Dodawanie szczegółów źródła** określ szczegóły połączenia dla źródłowego wystąpienia PostgreSQL.

    ![Ekran Dodawanie szczegółów źródła](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Wybierz **pozycję Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły obiektu docelowego** określ szczegóły połączenia docelowego serwera hiperskali (Citus), który jest wstępnie aprowizowanym wystąpieniem hiperskali (Citus), do którego wdrożono schemat **wypożyczeń dysków DVD** przy użyciu pg_dump.

    ![Ekran Szczegóły elementu docelowego](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłowa baza danych, usługa migracji bazy danych Azure Database domyślnie wybiera docelową bazę danych.

    ![Ekran Mapowanie do docelowych baz danych](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Wybierz **pozycję Zapisz**, a następnie na ekranie Ustawienia **migracji** zaakceptuj wartości domyślne.

    ![Ekran ustawień migracji](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Ekran podsumowania migracji](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlenie okna aktywności migracji, a **stan** działania powinien zostać zaktualizowany, aby był wyświetlany jako **kopia zapasowa w toku**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

     ![Monitorowanie procesu migracji](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Po zakończeniu migracji w obszarze **Nazwa bazy danych**wybierz określoną bazę danych, aby uzyskać dostęp do stanu migracji dla operacji pełnego ładowania **danych** i **przyrostowej synchronizacji danych.**

   > [!NOTE]
   > **Pełne obciążenie danych** pokazuje stan migracji obciążenia początkowego, podczas gdy **przyrostowa synchronizacja danych** pokazuje stan przechwytywania danych zmian (CDC).

     ![Szczegóły pełnego obciążenia danych](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Szczegóły przyrostowej synchronizacji danych](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Poczekaj, aż licznik **Oczekujące zmiany** pojawi się **0,** aby upewnić się, że wszystkie transakcje przychodzące do źródłowej bazy danych zostaną zatrzymane, zaznacz pole wyboru **Potwierdź,** a następnie wybierz pozycję **Zastosuj**.

    ![Kompletny ekran cutover](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Gdy stan migracji bazy danych pokazuje **zakończone,** połącz aplikacje z nowym wystąpieniem docelowym usługi Azure Database for PostgreSQL.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat usługi Azure Database for PostgreSQL, zobacz artykuł [Co to jest usługa Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
