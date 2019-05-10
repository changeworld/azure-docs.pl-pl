---
title: 'Samouczek: Użyj usługi Azure Database Migration Service online migracji RDS MySQL do usługi Azure Database for MySQL | Dokumentacja firmy Microsoft'
description: Dowiedz się wykonać migracji do usługi online z RDS MySQL do usługi Azure Database for MySQL za pomocą usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: e971fd160a43be088f6d3c4a9fb6fddc7dd769b0
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415668"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Samouczek: Migrowanie RDS MySQL do usługi Azure Database for MySQL online przy użyciu usługi DMS

Azure Database Migration Service można użyć do migracji baz danych z wystąpienia usługi RDS MySQL na [— Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) podczas źródłowa baza danych pozostaje w trybie online podczas migracji. Innymi słowy migracja może odbyć się przy minimalnych przestojach w aplikacji. W tym samouczku, wykonywana jest migracja **pracowników** przykładowej bazy danych z wystąpienia RDS MySQL do usługi Azure Database for MySQL za pomocą działania migracji w trybie online w usłudze Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Migrowanie schematu próbki, przy użyciu narzędzia polecenia mysqldump i bazy danych mysql.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano, jak można wykonać migracji do usługi online z wystąpienia RDS MySQL do usługi Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Upewnij się, że źródłowy serwer MySQL działa obsługiwane MySQL community edition. Aby ustalić wersji wystąpienia usługi MySQL, narzędzie mysql lub połączenia aplikacji MySQL Workbench, uruchom polecenie:

    ```
    SELECT @@version;
    ```

    Aby uzyskać więcej informacji, zobacz artykuł [obsługiwane — Azure Database for MySQL — wersje](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Pobierz i zainstaluj [MySQL **pracowników** przykładowej bazy danych](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Utwórz wystąpienie obiektu [— Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Tworzenie usługi Azure Virtual Network (VNet) dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [usługiExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentacja usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a szczególnie artykułów Szybki Start krok po kroku szczegółowe informacje.
* Upewnij się, że reguły sieci wirtualnej, sieciowej grupy zabezpieczeń nie blokują następujących portów komunikacji przychodzącej do usługi Azure Database Migration Service: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę Windows, aby umożliwić usłudze Azure Database Migration Service dostęp do serwera MySQL źródła, która domyślnie jest TCP port 3306.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
* Tworzenie z poziomu serwera [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) dla usługi Azure Database dla serwera MySQL zezwolić na dostęp usługi Azure Database Migration Service do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używane dla usługi Azure Database Migration Service.

> [!NOTE]
> Usługa Azure Database for MySQL obsługuje tylko aparatu InnoDB tabel. Aby przekonwertować tabele MyISAM aparatu InnoDB, zobacz artykuł [Konwertowanie tabel z MyISAM aparatu InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Konfigurowanie usługi AWS RDS MySQL dla replikacji

1. Aby utworzyć nową grupę parametrów, postępuj zgodnie z instrukcjami podanymi w artykule w usłudze AWS [pliki dziennika bazy danych MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)w **formatu rejestrowania binarnego** sekcji.
2. Utwórz nową grupę parametrów o następującej konfiguracji:
    * binlog_format = wiersz
    * binlog_checksum = NONE
3. Zapisz nową grupę parametrów.

## <a name="migrate-the-schema"></a>Migrowanie schematu

1. Prowadzenie schematu źródłowej bazy danych i zastosować do docelowej bazy danych, aby ukończyć migrację wszystkich obiektów bazy danych, takich jak schematów tabel, indeksy i procedur składowanych.

    Najprostszym sposobem, aby przeprowadzić migrację tylko schemat jest użycie polecenia mysqldump z parametrem--nie-data. Polecenie w celu migracji schematu jest następujące:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Na przykład zrzutu pliku schematu dla **pracowników** bazy danych, użyj następującego polecenia:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Zaimportuj schemat do docelowej usługi, która jest usługa Azure Database for MySQL. Aby przywrócić plik zrzutu schematu, uruchom następujące polecenie:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Na przykład, aby zaimportować schematu dla **pracowników** bazy danych:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem. Aby wyodrębnić skryptu klucza obcego listy i Dodaj skrypt klucza obcego w miejscu docelowym (— Azure Database for MySQL), uruchom następujący skrypt w aplikacji MySQL Workbench:

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
      AND KCU.REFERENCED_TABLE_SCHEMA = ['SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Uruchom klucz obcy docelowej (czyli drugiej kolumny) w wyniku zapytania, można usunąć klucza obcego.

5. Jeśli masz wyzwalaczy (insert nebo update, Wyzwól) w danych wymusi integralność danych w elemencie docelowym przed replikacji danych ze źródła. Zaleca się wyłączenie wyzwalaczy we wszystkich tabelach *w docelowej* podczas migracji, a następnie włączyć je ukończyć wyzwalaczy po migracji.

    Aby wyłączyć wyzwalaczy w docelowej bazie danych:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. W przypadku wystąpienia typu danych Wyliczenia w żadnych tabel, zaleca się tymczasowo aktualizacji do typu danych "zmieniającego się znakiem" w tabeli docelowej. Po zakończeniu replikacji danych, następnie przywrócić typ danych do Wyliczenia.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz **dostawców zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Utwórz wystąpienie usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w którym można utworzyć wystąpienia usługi Azure Database Migration Service.

5. Wybierz istniejącej sieci wirtualnej lub utworzyć nową.

    Sieć wirtualna zapewnia Azure Database Migration Service dostęp do wystąpienia MySQL źródłowej i docelowej bazy danych Azure MySQL wystąpienia.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową; dla tej migracji online należy wybrać Premium: Warstwa cenowa 4vCores.

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na **nowy projekt migracji** ekranu, określ nazwę dla projektu, w **typ serwera źródła** pola tekstowego, wybierz opcję **MySQL**, a następnie w polu **docelowej Typ serwera** pola tekstowego, wybierz opcję **AzureDbForMySQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    > [!IMPORTANT]
    > Pamiętaj o wybraniu **migracji danych w trybie Online**; jest w trybie offline nie są obsługiwane migracje dla tego scenariusza.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternatywnie można wybrać **Tworzenie projektu** można teraz utworzyć projekt migracji i wykonaj migrację później.

6. Wybierz pozycję **Zapisz**.

7. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    > [!NOTE]
    > Należy zwrócić uwagę, wstępne niezbędne do skonfigurowania migracja online w bloku tworzenia projektu.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na **szczegóły źródła migracji** ekranu, określ szczegóły połączenia dla wystąpienia MySQL źródła.

   ![Szczegóły źródła](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz **Zapisz**, a następnie na **docelowe szczegóły** ekranu, określ szczegóły połączenia z docelową usługę Azure Database dla serwera MySQL, który jest wstępnie przygotowany i ma **pracowników** wdrożeniu przy użyciu polecenia MySQLDump schematu.

    ![Wybieranie obiektu docelowego](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych, co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelowej bazy danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

    ![Stan działania — uruchamianie](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. W obszarze **Nazwa bazy danych**, wybierz konkretnej bazy danych, aby przejść do stanu w ramach migracji **pełne ładowanie danych** i **Przyrostowa synchronizacja danych** operacji.

    **Pełne ładowanie danych** pokazuje stan migracji ładowania początkowego, podczas gdy **Przyrostowa synchronizacja danych** pokazuje zmiany stanu (CDC) przechwytywania danych.

    ![Ładowanie danych pełnego ekranu spisu —](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Spis ekranu — Przyrostowa synchronizacja danych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowej pełne ładowanie baz danych są oznaczone **gotowy do Cutover**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Start jednorazową](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych jest wyświetlany **Ukończono**, łączenie aplikacji z nowy obiekt docelowy bazy danych Azure for MySQL database.

Online migracji środowiska lokalnego wystąpienia programu MySQL do usługi Azure Database for MySQL została zakończona.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje o usłudze Azure Database for MySQL, zobacz artykuł [co to jest Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Inne pytania, Wyślij wiadomość e-mail [poproś migracje baz danych Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliasu.
