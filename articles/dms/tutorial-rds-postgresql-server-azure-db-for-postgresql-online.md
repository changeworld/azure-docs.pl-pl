---
title: 'Samouczek: Na użytek Azure Database Migration Service migracji do usługi online postgresql usług pulpitu zdalnego do usługi Azure Database for PostgreSQL | Dokumentacja firmy Microsoft'
description: Dowiedz się przeprowadzić online migrację z postgresql w warstwie usług pulpitu zdalnego do usługi Azure Database for PostgreSQL za pomocą usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/26/2019
ms.openlocfilehash: 71610aa9916519338c564127616f4569aff70aaa
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925357"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Samouczek: Migrowanie postgresql w warstwie usług pulpitu zdalnego do usługi Azure Database for PostgreSQL online przy użyciu usługi DMS

Azure Database Migration Service można użyć do migracji baz danych z wystąpienia postgresql w warstwie usług pulpitu zdalnego w celu [— Azure Database for postgresql w warstwie](https://docs.microsoft.com/azure/postgresql/) podczas źródłowa baza danych pozostaje w trybie online podczas migracji. Innymi słowy migracja może odbyć się przy minimalnych przestojach w aplikacji. W tym samouczku, wykonywana jest migracja **wypożyczeń DVD** przykładowej bazy danych z wystąpienia 9.6 postgresql w warstwie usług pulpitu zdalnego do usługi Azure Database for PostgreSQL za pomocą działania migracji w trybie online w usłudze Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migrowanie schematu próbki, przy użyciu narzędzia pg_dump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób wykonywania migracji do usługi online z lokalnego wystąpienia programu PostgreSQL do usługi Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj program [PostgreSQL Community Edition](https://www.postgresql.org/download/) w wersji 9.5, 9.6 lub 10. Źródłowy serwer PostgreSQL Server musi być w wersji 9.5.11, 9.6.7, 10 lub nowszej. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Ponadto wersja postgresql w warstwie usług pulpitu zdalnego musi być zgodna usługi Azure Database for postgresql w warstwie wersji. Na przykład postgresql w warstwie usług pulpitu zdalnego 9.5.11.5 można wykonywać migrację tylko — Azure Database for postgresql w warstwie 9.5.11, a nie wersja 9.6.7.

    > [!NOTE]
    > Database for PostgreSQL w wersji 10 aktualnie DMS obsługuje tylko migrację w wersji 10.3 do usługi Azure Database for PostgreSQL. Planujemy do obsługi nowszych wersji PostgreSQL bardzo szybko.

* Utwórz wystąpienie obiektu [— Azure Database for postgresql w warstwie](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal). Skontaktuj się z tym [sekcji](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) dokumentu, aby uzyskać szczegóły na temat sposobu łączenia się z serwerem PostgreSQL za pomocą narzędzia pgAdmin.
* Tworzenie usługi Azure Virtual Network (VNet) dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [usługiExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Upewnij się, że reguły sieci wirtualnej, sieciowej grupy zabezpieczeń nie blokują następujących portów komunikacji przychodzącej do usługi Azure Database Migration Service: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę Windows, aby umożliwić usłudze Azure Database Migration Service dostęp do serwera PostgreSQL źródła, która domyślnie jest TCP port 5432.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
* Tworzenie z poziomu serwera [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) dla usługi Azure Database for postgresql w warstwie serwera umożliwia Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używane dla usługi Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Konfigurowanie usługi AWS RDS PostgreSQL dla replikacji

1. Aby utworzyć nową grupę parametrów, postępuj zgodnie z instrukcjami podanymi w artykule w usłudze AWS [Praca z grupami parametr DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Użyj nazwy głównej użytkownika nawiązać połączenie ze źródłem z usługi Azure Database Migration Service. Jeśli używasz innego konta niż konto użytkownika głównego, konto musi mieć rolę rds_superuser i rolę rds_replication. Rds_replication rola przyznaje uprawnienia do zarządzania miejsca logiczne i przesyłanie strumieniowe danych za pomocą miejsc logiczne.
3. Utwórz nową grupę parametrów o następującej konfiguracji:. Ustaw parametr rds.logical_replication w grupie parametr bazy danych do 1.
    b. max_wal_senders = [liczbę współbieżnych zadań] — parametr max_wal_senders Ustawia liczbę współbieżnych zadań, które można uruchomić, zaleca się 10 zadań.
    c. max_replication_slots — = [Liczba gniazd], zaleca się Ustaw 5 gniazd.
4. Skojarz grupy parametrów, utworzonego wystąpienia postgresql w warstwie usług pulpitu zdalnego.

## <a name="migrate-the-schema"></a>Migrowanie schematu

1. Prowadzenie schematu źródłowej bazy danych i zastosować do docelowej bazy danych, aby ukończyć migrację wszystkich obiektów bazy danych, takich jak schematów tabel, indeksy i procedur składowanych.

    Najprostszym sposobem, aby przeprowadzić migrację tylko schemat jest pg_dump za pomocą opcji -s. Aby uzyskać więcej informacji, zobacz [przykłady](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) w samouczku pg_dump Postgres.
    
    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```
    
    Na przykład zrzutu pliku schematu dla **dvdrental** bazy danych, użyj następującego polecenia:
    
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Utwórz pustą bazę danych w usłudze docelowym jest usługa Azure Database for PostgreSQL. Aby nawiązać połączenie i utworzyć bazę danych, można skorzystać z jednego z następujących artykułów:

    * [Tworzenie usługi Azure Database for postgresql w warstwie serwera za pomocą witryny Azure portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Tworzenie serwera usługi Azure Database for PostgreSQL za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli)

3. Zaimportuj schemat do docelowej usługi, która jest usługa Azure Database for PostgreSQL. Aby przywrócić plik zrzutu schematu, uruchom następujące polecenie:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Na przykład:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem. Aby wyodrębnić skryptu klucza obcego listy i Dodaj skrypt klucza obcego w miejscu docelowym (— Azure Database for postgresql w warstwie), uruchom następujący skrypt, w narzędziu PgAdmin lub wersja narzędzia psql:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT 
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,    
        KCU.TABLE_NAME,
        KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'sakila') Queries
      GROUP BY SchemaName;
    ```
        
5. Uruchom klucz obcy docelowej (czyli drugiej kolumny) w wyniku zapytania, można usunąć klucza obcego.

6. Jeśli masz wyzwalaczy (insert nebo update, Wyzwól) w danych wymusi integralność danych w elemencie docelowym przed replikacji danych ze źródła. Zaleca się wyłączenie wyzwalaczy we wszystkich tabelach *w docelowej* podczas migracji, a następnie włączyć je ukończyć wyzwalaczy po migracji.

    Aby wyłączyć wyzwalaczy w docelowej bazie danych:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz **dostawców zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Utwórz wystąpienie usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w którym można utworzyć wystąpienia usługi Azure Database Migration Service.

5. Wybierz istniejącej sieci wirtualnej lub utworzyć nową.

    Sieć wirtualna zapewnia Azure Database Migration Service dostęp do źródłowe wystąpienie PostgreSQL i docelową usługę Azure Database for postgresql w warstwie wystąpienia.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową; dla tej migracji online należy wybrać Premium: Warstwa cenowa 4vCores.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing). Jeśli potrzebujesz pomocy dotyczącej wybierając odpowiednie usługi Azure Database Migration Service warstwy cenowej, zapoznaj się zalecenia przedstawione w publikowanie [tutaj](https://go.microsoft.com/fwlink/?linkid=861067).

     ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na **nowy projekt migracji** ekranu, określ nazwę dla projektu, w **typ serwera źródła** pola tekstowego, wybierz opcję **AWS RDS database for PostgreSQL**, a następnie w polu **Typ serwera docelowego** pola tekstowego, wybierz opcję **— Azure Database for postgresql w warstwie**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    > [!IMPORTANT]
    > Pamiętaj o wybraniu **migracji danych w trybie Online**; jest w trybie offline nie są obsługiwane migracje dla tego scenariusza.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternatywnie można wybrać **Tworzenie projektu** można teraz utworzyć projekt migracji i wykonaj migrację później.

6. Wybierz pozycję **Zapisz**.

7. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    > [!NOTE]
    > Należy zwrócić uwagę, wstępne niezbędne do skonfigurowania migracja online w bloku tworzenia projektu.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na **szczegóły źródła migracji** ekranu, określ szczegóły połączenia dla wystąpienia PostgreSQL źródła.

   ![Szczegóły źródła](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz **Zapisz**, a następnie na **docelowe szczegóły** ekranu, określ szczegóły połączenia z docelową usługę Azure Database for postgresql w warstwie serwera, który jest wstępnie przygotowany i ma **dysków DVD Wypożyczenia** wdrażane za pomocą pg_dump schematu.

    ![Wybieranie obiektu docelowego](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych, co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelowej bazy danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

    ![Stan działania — uruchamianie](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. W obszarze **Nazwa bazy danych**, wybierz konkretnej bazy danych, aby przejść do stanu w ramach migracji **pełne ładowanie danych** i **Przyrostowa synchronizacja danych** operacji.

    **Pełne ładowanie danych** pokazuje stan migracji ładowania początkowego, podczas gdy **Przyrostowa synchronizacja danych** pokazuje zmiany stanu (CDC) przechwytywania danych.

    ![Ładowanie danych pełnego ekranu spisu —](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Spis ekranu — Przyrostowa synchronizacja danych](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowej pełne ładowanie baz danych są oznaczone **gotowy do Cutover**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Start jednorazową](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)
 
2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych jest wyświetlany **Ukończono**, łączenie aplikacji z nowy obiekt docelowy bazy danych Azure for PostgreSQL database.

Migrację online lokalne wystąpienie programu PostgreSQL do usługi Azure Database for PostgreSQL jest teraz ukończona.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Aby uzyskać informacje o usłudze Azure Database for PostgreSQL, zobacz artykuł [co to jest Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
- Inne pytania, Wyślij wiadomość e-mail [poproś migracje baz danych Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliasu.
