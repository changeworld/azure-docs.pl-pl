---
title: 'Samouczek: Migrowanie usługi RDS MySQL w trybie online do bazy danych platformy Azure dla mysql'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z usługi RDS MySQL do bazy danych platformy Azure dla mysql przy użyciu usługi migracji bazy danych Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255160"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Samouczek: Migrowanie usługi RDS MySQL do bazy danych platformy Azure dla mysql online przy użyciu usługi DMS

Usługi migracji usługi Azure Database można użyć do migracji baz danych z wystąpienia MySQL usługi RDS do [usługi Azure Database dla MySQL,](https://docs.microsoft.com/azure/mysql/) podczas gdy źródłowa baza danych pozostaje w trybie online podczas migracji. Innymi słowy migracji można osiągnąć przy minimalnym przestoju do aplikacji. W tym samouczku należy przeprowadzić migrację przykładowej bazy danych **employees** z wystąpienia usługi RDS MySQL do bazy danych platformy Azure dla mysql przy użyciu działania migracji online w usłudze migracji bazy danych Azure Database.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migrowanie przykładowego schematu przy użyciu narzędzi mysqldump i mysql.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu usługi migracji bazy danych platformy Azure.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

> [!NOTE]
> Korzystanie z usługi migracji bazy danych Azure do wykonywania migracji online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób przeprowadzania migracji online z wystąpienia usługi RDS MySQL do usługi Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Upewnij się, że źródłowy serwer MySQL jest uruchomiony obsługiwany program społeczności MySQL. Aby określić wersję wystąpienia MySQL, w narzędziu mysql lub mysql Workbench uruchom polecenie:

    ```
    SELECT @@version;
    ```

    Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane usługi Azure Database dla wersji MySQL](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Pobierz i zainstaluj [przykładową bazę danych **Pracowników** MySQL](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Utwórz wystąpienie [usługi Azure Database dla mysql](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.
* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieciowej sieci nie blokują następujących portów komunikacji przychodzącej do usługi migracji bazy danych Azure: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Skonfiguruj [Zaporę systemu Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (lub zaporę systemu Linux), aby umożliwić dostęp do aparatu bazy danych. W przypadku serwera MySQL zezwalaj na łączność z portem 3306.

> [!NOTE]
> Usługa Azure Database for MySQL obsługuje tylko tabele Usługi InnoDB. Aby przekonwertować tabele MyISAM na InnoDB, zapoznaj się z artykułem [Konwertowanie tabel z MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Konfigurowanie usługi AWS RDS MySQL dla replikacji

1. Aby utworzyć nową grupę parametrów, postępuj zgodnie z instrukcjami dostarczonymi przez AWS w artykule [MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), w sekcji **Format rejestrowania binarnego.**
2. Utwórz nową grupę parametrów z następującą konfiguracją:
    * binlog_format = row
    * binlog_checksum = BRAK
3. Zapisz nową grupę parametrów.
4. Skojarz nową grupę parametrów z wystąpieniem RDS MySQL. Może być wymagane ponowne uruchomienie komputera.

## <a name="migrate-the-schema"></a>Migrowanie schematu

1. Wyodrębnij schemat ze źródłowej bazy danych i zastosuj do docelowej bazy danych, aby zakończyć migrację wszystkich obiektów bazy danych, takich jak schematy tabeli, indeksy i procedury przechowywane.

    Najprostszym sposobem migracji tylko schematu jest użycie mysqldump z parametrem --no-data. Polecenie migracji schematu jest następujące:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Na przykład, aby zrzucić plik schematu dla bazy danych **Employees,** użyj następującego polecenia:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Zaimportuj schemat do usługi docelowej, która jest usługą Azure Database for MySQL. Aby przywrócić plik zrzutu schematu, uruchom następujące polecenie:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Na przykład, aby zaimportować schemat dla bazy danych **Pracownicy:**

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem. Aby wyodrębnić skrypt klucza obcego upuszczenia i dodać skrypt klucza obcego w miejscu docelowym (Usługa Azure Database for MySQL), uruchom następujący skrypt w mysql workbench:

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
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Uruchom klucz obcy drop (który jest drugą kolumną) w wyniku kwerendy, aby usunąć klucz obcy.

5. Jeśli masz wyzwalacze (wstawić lub zaktualizować wyzwalacz) w danych, będzie wymusić integralność danych w miejscu docelowym przed replikowaniem danych ze źródła. Zalecenie jest, aby wyłączyć wyzwalacze we wszystkich tabelach *na miejsce docelowe* podczas migracji, a następnie włączyć wyzwalacze po zakończeniu migracji.

    Aby wyłączyć wyzwalacze w docelowej bazie danych:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Jeśli istnieją wystąpienia typu danych ENUM w dowolnej tabeli, zaleca się tymczasowe aktualizowanie do typu danych "zmienne znaki" w tabeli docelowej. Replikacja danych WHen została zakończona, a następnie przywróć typ danych do ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi migracji bazy danych platformy Azure

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure.

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego wystąpienia MySQL i docelowej bazy danych platformy Azure dla wystąpienia MySQL.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową; Dla tej migracji online, należy wybrać warstwę cenową Premium: 4vCores.

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** określ nazwę projektu w polu **tekstowym Typ serwera źródłowego** wybierz pozycję **MySQL**, a następnie w polu **tekstowym Typ serwera docelowego** wybierz pozycję **AzureDbForMySQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych online**.

    > [!IMPORTANT]
    > Pamiętaj, aby wybrać **migrację danych online;** Migracje w trybie offline nie są obsługiwane w tym scenariuszu.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternatywnie można wybrać **utwórz projekt tylko** do utworzenia projektu migracji teraz i wykonać migrację później.

6. Wybierz **pozycję Zapisz**.

7. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    > [!NOTE]
    > Proszę zanotować wymagania wstępne potrzebne do skonfigurowania migracji online w bloku tworzenia projektu.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na ekranie **szczegółów źródła migracji** określ szczegóły połączenia dla źródłowego wystąpienia MySQL.

   ![Szczegóły źródła](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz **pozycję Zapisz**, a następnie na ekranie Szczegóły **docelowe** określ szczegóły połączenia dla docelowej bazy danych platformy Azure dla serwera MySQL, który jest wstępnie aprowizowany i ma schemat **Pracowników wdrożony** przy użyciu mySQLDump.

    ![Wybieranie obiektu docelowego](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłowa baza danych, usługa migracji bazy danych Azure Database domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

    ![Stan działania — bieganie](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. W obszarze **NAZWA BAZY DANYCH**wybierz określoną bazę danych, aby uzyskać dostęp do stanu migracji dla operacji pełnego ładowania **danych** i **przyrostowej synchronizacji danych.**

    **Pełne obciążenie danych** pokazuje stan migracji obciążenia początkowego, podczas gdy **przyrostowa synchronizacja danych** pokazuje stan przechwytywania danych zmian (CDC).

    ![Ekran zapasów — pełne obciążenie danych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Ekran zapasów — przyrostowa synchronizacja danych](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego obciążenia bazy danych są oznaczone **jako Gotowe do przecięcia.**

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Zacznij się przeciąć](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych pokazuje **zakończone,** połącz aplikacje z nową docelową bazą danych platformy Azure dla mysql bazy danych.

Migracja online lokalnego wystąpienia MySQL do usługi Azure Database for MySQL została zakończona.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat usługi Azure Database for MySQL, zobacz artykuł [Co to jest usługa Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* W przypadku innych pytań wyślij wiadomość e-mail [z aliasem Zapytaj o migracje bazy danych platformy Azure.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
