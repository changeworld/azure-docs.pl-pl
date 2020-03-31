---
title: 'Samouczek: Migrowanie mysql online do bazy danych platformy Azure dla mysql'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z usługi MySQL lokalnie do usługi Azure Database for MySQL przy użyciu usługi migracji bazy danych Azure.
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
ms.openlocfilehash: 7c8087a01bb71657e816be89b6a562dd4783b271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240736"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Samouczek: migrowanie programu MySQL do usługi Azure Database for MySQL w trybie online przy użyciu usługi DMS

Usługi migracji bazy danych platformy Azure można użyć do migracji baz danych z lokalnego wystąpienia MySQL do [usługi Azure Database dla MySQL](https://docs.microsoft.com/azure/mysql/) przy minimalnym przestoju. Innymi słowy migrację można przeprowadzić przy minimalnych przestojach w działaniu aplikacji. W tym samouczku należy przeprowadzić migrację przykładowej bazy danych **employees** z lokalnego wystąpienia MySQL 5.7 do usługi Azure Database for MySQL przy użyciu działania migracji online w usłudze migracji bazy danych platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Migrowanie przykładowego schematu za pomocą narzędzia mysqldump.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu usługi migracji bazy danych platformy Azure.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

> [!NOTE]
> Korzystanie z usługi migracji bazy danych Azure do wykonywania migracji online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca utworzenie wystąpienia usługi migracji bazy danych platformy Azure w tym samym regionie platformy Azure co docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobrany i zainstalowany program [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) w wersji 5.6 lub 5.7. Wersja lokalnego programu MySQL musi być zgodna z wersją usługi Azure Database for MySQL. Na przykład program MySQL 5.6 może być migrowany tylko do usługi Azure Database for MySQL 5.6, a nie do nowszej wersji 5.7.
* [Utwórz wystąpienie usługi Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Zapoznaj się z artykułem [Nawiązywanie połączeń z danymi i wykonywanie na nich zapytań przy użyciu środowiska roboczego bazy danych MySQL](https://docs.microsoft.com/azure/mysql/connect-workbench), aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych przy użyciu witryny Azure Portal.  
* Utwórz usługę migracji bazy danych usługi Azure Database dla usługi Azure Database przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.

    > [!NOTE]
    > Podczas konfigurowania sieci wirtualnej Sieci, jeśli używasz usługi ExpressRoute z komunikacją równorzędną sieciową do firmy Microsoft, dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usługi do podsieci, w której usługa będzie aprowizowana:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, punkt końcowy usługi Cosmos DB itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy magistrali usług
    >
    > Ta konfiguracja jest konieczna, ponieważ usługa migracji bazy danych platformy Azure nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieciowej sieci nie blokują następujących portów komunikacji przychodzącej do usługi migracji bazy danych Azure: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę systemu Windows, aby zezwolić usłudze migracji bazy danych platformy Azure na dostęp do źródła Serwera MySQL, który domyślnie jest portem TCP 3306.
* Podczas korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby umożliwić usłudze migracji usługi Azure Database dostęp do źródłowych baz danych w celu migracji.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla usługi Azure Database for MySQL, aby umożliwić usłudze migracji usługi Azure Database dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi migracji bazy danych Azure.
* Źródłowy serwer MySQL musi znajdować się w obsługiwanej bazie danych MySQL Community Edition. Aby określić wersję instancji programu MySQL, w narzędziu MySQL lub środowisku roboczym bazy danych MySQL uruchom następujące polecenie:

    ```
    SELECT @@version;
    ```

* Usługa Azure Database for MySQL obsługuje wyłącznie tabele InnoDB. Aby przekonwertować tabele MyISAM na format InnoDB, zapoznaj się z artykułem [Konwertowanie tabel z formatu MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Włącz rejestrowanie binarne w pliku my.ini (Windows) lub my.cnf (Unix) źródłowej bazy danych przy użyciu następującej konfiguracji:

  * **server_id** = 1 lub wyższa wartość (ma zastosowanie tylko w przypadku bazy danych MySQL 5.6)
  * ścieżka **pojemnika** = \<dziennika> (dotyczy tylko MySQL 5.6) Na przykład: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (zaleca się, aby nie używać zera; dotyczy tylko MySQL 5.6)
  * **Binlog_row_image** = full (ma zastosowanie tylko w przypadku bazy danych MySQL 5.6)
  * **log_slave_updates** = 1

* Użytkownik musi mieć rolę ReplicationAdmin z następującymi uprawnieniami:

  * **KLIENT REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
  * **REPLIKA REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
  * **SUPER** — wymagane tylko w wersjach wcześniejszych niż MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych. Aby wyodrębnić schemat, użyj polecenia mysqldump z parametrem `--no-data`.

Zakładając, że masz **przykładową** bazę danych MySQL Employees w systemie lokalnym, polecenie migracji schematu przy użyciu mysqldump jest następujące:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Przykład:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Aby zaimportować schemat do docelowej usługi Azure Database for MySQL, uruchom następujące polecenie:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Przykład:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem.  Wykonaj następujący skrypt w mysql workbench, aby wyodrębnić skrypt klucza obcego upuszczenia i dodać skrypt klucza obcego.

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

Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania w celu usunięcia klucza obcego.

> [!IMPORTANT]
> W przypadku importowania danych przy użyciu kopii zapasowej należy usunąć polecenia CREATE DEFINER ręcznie lub za pomocą polecenia --skip-definer podczas wykonywania mysqldump. DEFINER wymaga super uprawnień do tworzenia i jest ograniczona w usłudze Azure Database dla MySQL.

Jeśli masz wyzwalacz w danych (wstawić lub zaktualizować wyzwalacz), wymusi integralność danych w miejscu docelowym przed zreplikowanych danych ze źródła. W czasie trwania migracji zaleca się wyłączenie wyzwalaczy we wszystkich tabelach w docelowej lokalizacji. Po jej zakończeniu wyzwalacze należy ponownie włączyć.

Aby wyłączyć wyzwalacze w docelowej bazie danych, użyj następującego polecenia:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego programu SQL Server i docelowego wystąpienia bazy danych SQL.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdź wszystkie wystąpienia usługi migracji bazy danych platformy Azure](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Na ekranie **Usługi migracji bazy danych Azure** wyszukaj nazwę utworzonego wystąpienia usługi migracji bazy danych Azure, a następnie wybierz wystąpienie.

     ![Lokalizowanie wystąpienia usługi migracji bazy danych platformy Azure](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MySQL**, a w polu tekstowym **Typ serwera docelowego** wybierz pozycję **AzureDbForMySQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Alternatywnie można wybrać **utwórz projekt tylko** do utworzenia projektu migracji teraz i wykonać migrację później.

6. Wybierz pozycję **Zapisz**, pamiętając o uwzględnieniu wymagań niezbędnych do prawidłowego korzystania z usługi DMS na potrzeby migracji danych, a następnie wybierz pozycję **Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Dodawanie szczegółów źródła** określ szczegóły połączenia dla źródłowego wystąpienia programu MySQL.

    ![Ekran Dodawanie szczegółów źródła](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz polecenie **Zapisz**, a następnie na ekranie **Szczegóły elementu docelowego** określ szczegóły połączenia dla docelowego serwera usługi Azure Database for MySQL, czyli dla wstępnie zaaprowizowanego wystąpienia bazy danych Azure Database for MySQL, w której wdrożono schemat **Employees** za pomocą narzędzia mysqldump.

    ![Ekran Szczegóły elementu docelowego](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłowa baza danych, usługa migracji bazy danych Azure Database domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Chociaż można wybrać wiele baz danych w tym kroku, każde wystąpienie usługi migracji bazy danych Azure obsługuje maksymalnie cztery bazy danych dla jednoczesnej migracji. Ponadto istnieje limit dwóch wystąpień usługi migracji bazy danych platformy Azure na region w ramach subskrypcji. Na przykład jeśli masz 40 baz danych do migracji, można migrować tylko osiem z nich jednocześnie i tylko wtedy, gdy utworzono dwa wystąpienia usługi migracji bazy danych platformy Azure.

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlene okno aktywności migracji, a **stan** działania **jest inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

     ![Stan działania — ukończono](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. W obszarze **Nazwa bazy danych** wybierz określoną bazę danych, aby uzyskać stan migracji dla operacji **Pełne ładowanie danych** oraz **Przyrostowa synchronizacja danych**.

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).

     ![Stan działania — ukończono pełne ładowanie](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpoczynanie migracji jednorazowej](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych będzie wyświetlać wartość **Ukończono**, połącz aplikacje z nową docelową usługą Azure SQL Database.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for MySQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for MySQL](known-issues-azure-mysql-online.md).
* Aby uzyskać informacje na temat usługi migracji bazy danych platformy Azure, zobacz artykuł [Co to jest usługa migracji bazy danych platformy Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat usługi Azure Database for MySQL, zobacz artykuł [Co to jest usługa Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
