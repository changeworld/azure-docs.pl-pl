---
title: 'Samouczek: Azure Database Migration Service umożliwia wykonanie migracji online programu Oracle do usługi Azure Database for PostgreSQL | Dokumentacja firmy Microsoft'
description: Dowiedz się dokonać migracji online z oprogramowania Oracle w środowisku lokalnym lub na maszynach wirtualnych do usługi Azure Database for PostgreSQL za pomocą usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 0b3af3d29e6e938f0301d751a79170c7c1964b45
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243803"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Samouczek: Migrowanie bazy danych Oracle do usługi Azure Database for PostgreSQL online przy użyciu usługi DMS (wersja zapoznawcza)

Azure Database Migration Service można użyć do migracji baz danych Oracle baz danych hostowanych lokalnie lub na maszynach wirtualnych [— Azure Database for postgresql w warstwie](https://docs.microsoft.com/azure/postgresql/) przy minimalnych przestojach. Innymi słowy można wykonać migrację z minimalnym czasem przestojów aplikacji. W tym samouczku, wykonywana jest migracja **HR** przykładowej bazy danych ze środowiska lokalnego lub wystąpienia maszyny wirtualnej programu Oracle 11 g do usługi Azure Database for PostgreSQL za pomocą działania migracji w trybie online w usłudze Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Należy ocenić nakład pracy migracji za pomocą narzędzia ora2pg.
> * Migrowanie schematu próbki, przy użyciu narzędzia ora2pg.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

> [!NOTE]
> Korzystanie z usługi Azure Database Migration Service w celu wykonania migracji do usługi online wymaga tworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Środowisko optymalne migracji firma Microsoft zaleca tworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure jako docelowej bazy danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano, jak można wykonać migracji do usługi online od firmy Oracle do usługi Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj [Oracle 11g w wersji 2 (Standard Edition, jednej wersji Standard lub Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Pobierz przykład **HR** bazy danych z [tutaj](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Pobierz i zainstaluj ora2pg w dowolnym [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) lub [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Utwórz wystąpienie usługi Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Połącz z wystąpieniem programu i utworzyć bazę danych, korzystając z instrukcji w tym [dokumentu](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Tworzenie usługi Azure Virtual Network (VNet) dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [usługiExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentacja usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a szczególnie artykułów Szybki Start krok po kroku szczegółowe informacje.

  > [!NOTE]
  > Podczas konfigurowania sieci wirtualnej, jeśli korzystasz z usługi ExpressRoute za pomocą komunikacji równorzędnej sieci do firmy Microsoft, Dodaj następujące usługi [punktów końcowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której będą udostępniane usługi:
  > * Docelowy punkt końcowy z bazy danych (na przykład SQL punktu końcowego, punktu końcowego usługi Cosmos DB i tak dalej)
  > * Punkt końcowy magazynu
  > * Punkt końcowy usługi Service bus
  >
  > Ta konfiguracja jest konieczne, ponieważ usługa Azure Database Migration Service nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń (NSG) sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do usługi Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę Windows, aby umożliwić usłudze Azure Database Migration Service dostęp do serwera bazy danych Oracle źródła, która domyślnie jest TCP port 1521 bazy danych.
* Podczas korzystania z urządzenia zapory przed źródłowych baz danych, użytkownik może być konieczne dodanie reguły zapory zezwalające usługi Azure Database Migration Service dostęp źródłowych baz danych do migracji do.
* Tworzenie z poziomu serwera [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) dla usługi Azure Database for PostgreSQL zezwolić na dostęp usługi Azure Database Migration Service do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używane dla usługi Azure Database Migration Service.
* Zapewnianie dostępu do baz danych Oracle źródła.

  > [!NOTE]
  > Rola DBA jest wymagana dla użytkownika połączyć się ze źródłem bazy danych Oracle.

  * Dzienniki wykonaj ponownie archiwum jest wymagana dla synchronizacji przyrostowej w usłudze Azure Database Migration Service do przechwytywania zmian danych. Wykonaj następujące kroki, aby skonfigurować źródła Oracle:
    * Zaloguj się przy użyciu uprawnień grupy SYSDBA, uruchamiając następujące polecenie:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Zamknij wystąpienie bazy danych, uruchamiając następujące polecenie.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Czekaj na potwierdzenie `'ORACLE instance shut down'`.

    * Uruchom nowe wystąpienie i instalacji (ale nie otwieraj) bazy danych, aby włączyć lub wyłączyć bu archiwizacji, uruchamiając następujące polecenie:

      ```
      STARTUP MOUNT;
      ```

      Baza danych musi być zainstalowana; Czekaj na potwierdzenie "Instancja Oracle pracę".

    * Zmień bazę danych, archiwizowanie trybu, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Otwórz bazę danych dla normalnych operacji, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE OPEN;
      ```

      Może być konieczne ponowne uruchomienie pliku ŁUK pojawienie się.

    * Aby sprawdzić, uruchom następujące polecenie:

      ```
      SELECT log_mode FROM v$database;
      ```

      Powinna pojawić się odpowiedź `'ARCHIVELOG'`. Jeśli odpowiedź jest `'NOARCHIVELOG'`, a następnie wymaganie nie jest spełniony.

  * Włącz dodatkowe rejestrowanie przy użyciu jednej z następujących opcji replikacji.

    * **Opcja 1**.
      Zmień poziom rejestrowania dodatkowe bazy danych na pokrycie wszystkich tabel za pomocą klucza podstawowego i unikatowego indeksu. Wykrywanie kwerenda będzie zwracać `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Zmiana wersji zapoznawczych poziomu tabeli rejestrowania. Uruchom tylko dla tabel, manipulowanie danymi, które nie mają PKs lub indeksy unikatowe.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opcja 2**.
      Zmień poziom rejestrowania dodatkowe bazy danych na pokrycie wszystkich tabel i wykrywania zapytanie zwraca `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Zmiana wersji zapoznawczych poziomu tabeli rejestrowania. Logika poniżej, aby uruchomić tylko jednej instrukcji dla każdej tabeli.

      Jeśli tabela ma klucz podstawowy:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Jeśli tabela ma unikatowy indeks:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      W przeciwnym razie uruchom następujące polecenie:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Aby sprawdzić, uruchom następujące polecenie:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Powinna pojawić się odpowiedź `'YES'`.

> [!IMPORTANT]
> Dla publicznej wersji zapoznawczej tego scenariusza, Azure Database Migration Service obsługuje Oracle w wersji 10g lub 11g. Klienci korzystający z oprogramowania Oracle w wersji 12 c lub nowszym należy zauważyć, że protokół uwierzytelniania minimalne dozwolone dla sterownika ODBC nawiązać połączenia z programem Oracle musi być 8. Dla źródła danych programu Oracle w wersji 12c lub później, należy skonfigurować protokół uwierzytelniania w następujący sposób:
>
> * Zaktualizuj SQLNET. ORA:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Uruchom ponownie komputer, aby nowe ustawienia zaczęły obowiązywać.
> * Zmień hasło dla istniejących użytkowników:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   Aby uzyskać więcej informacji, zobacz stronę [tutaj](http://www.dba-oracle.com/t_allowed_login_version_server.htm).
>
> Na koniec należy pamiętać, że zmiana protokołu uwierzytelniania mogą mieć wpływ na uwierzytelnienie klienta.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Oceny nakład pracy na oprogramowanie Oracle do usługi Azure Database for postgresql w warstwie migracji

Firma Microsoft zaleca, aby ocenić nakład pracy wymaganych do przeprowadzenia migracji od firmy Oracle do usługi Azure Database for PostgreSQL przy użyciu ora2pg. Użyj `ora2pg -t SHOW_REPORT` dyrektywy, aby utworzyć raport zawierający listę wszystkich obiektów bazy danych Oracle, migracji szacowany koszt (w dniach dla deweloperów) i niektórych obiektów bazy danych, które mogą wymagać szczególną uwagę podczas konwersji.

Większość klientów będą spędzać czas sporo, przeglądając raport z oceny i biorąc pod uwagę nakład pracy związany z konwersji automatycznej i ręcznej.

Aby skonfigurować i uruchomić ora2pg, aby utworzyć raport z oceny, zobacz **Premigration: Ocena** części [Oracle do usługi Azure Database for postgresql — podręcznik](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Raport z oceny ora2pg próbka jest dostępna dla odwołania [tutaj](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Eksportuj schemat bazy danych Oracle

Zalecamy użycie ora2pg do przekonwertowania schematu bazy danych Oracle i innych obiektów bazy danych Oracle (typy, procedury, funkcje itp.) na schemat, który jest zgodny z usługą Azure Database for PostgreSQL. ora2pg obejmuje wiele dyrektyw pomagającym w zdefiniowaniu wstępnie określonych typów danych. Na przykład, można użyć `DATA_TYPE` dyrektywy, aby zastąpić wszystkie NUMBER(*,0) bigint, a nie NUMERIC(38).

Możesz uruchomić ora2pg, aby wyeksportować każdy z obiektów bazy danych w plikach .sql. Następnie możesz przejrzeć pliki .sql przed zaimportowaniem ich do usługi Azure Database for PostgreSQL za pomocą narzędzia psql lub można wykonywać. Skrypt SQL w narzędziu PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Na przykład:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Aby skonfigurować i uruchomić ora2pg dla konwersji z wersji schematu, zobacz **migracji: Schemat i dane** części [Oracle do usługi Azure Database for postgresql — podręcznik](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Konfigurowanie schematu w usłudze Azure Database for PostgreSQL

Domyślnie Oracle przechowuje schema.table.column we wszystkich przypadkach górnego, podczas gdy PostgreSQL zachowuje schema.table.column w małych liter. Azure Database Migration Service rozpocząć przenoszenie danych od firmy Oracle do usługi Azure Database for PostgreSQL schema.table.column musi być tym samym formacie przypadków jako źródło Oracle.

Na przykład, jeśli źródło Oracle ma Schema "H"." EMPLOYEES"." EMPLOYEE_ID", a następnie schematu PostgreSQL, należy użyć tego samego formatu.

Aby upewnij się, że format przypadków schema.table.column takie same dla bazy danych Oracle i Azure Database for PostgreSQL, firma Microsoft zaleca, wykonaj następujące kroki.

> [!NOTE]
> Można użyć innego podejścia do wyprowadzenia schematu wielkie litery. Pracujemy, aby poprawić i zautomatyzować ten krok.

1. Eksportowanie schematów ora2pg przy użyciu małych liter. W skrypcie sql tworzenia tabeli ręcznie Utwórz schemat z wielkimi literami "SCHEMA".
2. Pozostałe obiekty bazy danych Oracle, takich jak wyzwalacze, sekwencji, procedur, typy i funkcje, należy zaimportować do usługi Azure Database for PostgreSQL.
3. Aby tabela i kolumna GÓRNEGO, uruchom następujący skrypt:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* Usuwanie klucza obcego w docelowej bazie danych, aby uzyskać pełne ładowanie do uruchomienia. Zapoznaj się **migrować schemat przykładowych** część artykułu [tutaj](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) skryptu używanego w celu usunięcia klucza obcego.
* Azure Database Migration Service umożliwia uruchamianie pełnego ładowania i synchronizacji.
* Gdy dane w lokalizacji docelowej usługi Azure Database dla PostgreSQL wystąpienia zostaje przechwycony ze źródłem, wykonaj uruchomienie produkcyjne w usłudze Azure Database Migration Service bazy danych.
* Zapewnienie SCHEMATÓW, tabel i kolumn małych liter (Jeśli schemat dla usługi Azure Database for postgresql — powinna być temu zapytania aplikacji), uruchom następujący skrypt:

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz istniejącej sieci wirtualnej lub utworzyć nową.

    Sieć wirtualna udostępnia usługi Azure Database Migration Service dostęp do źródłowej bazy danych Oracle i docelową usługę Azure Database for postgresql w warstwie wystąpienia.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

    ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na **nowy projekt migracji** ekranu, określ nazwę dla projektu, w **typ serwera źródła** pola tekstowego, wybierz opcję **Oracle**w **typ serwera docelowego**  pola tekstowego, wybierz opcję **— Azure Database for postgresql w warstwie**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

   ![Tworzenie projektu usługi Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternatywnie można wybrać **Tworzenie projektu** można teraz utworzyć projekt migracji i wykonaj migrację później.

6. Wybierz **Zapisz**, należy uwzględnić wymagania, aby pomyślnie korzystać z usługi Azure Database Migration Service do przeprowadzenia migracji online, a następnie wybierz **tworzenia i uruchamiania działania**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na **Dodaj szczegóły źródła** ekranu, określ szczegóły połączenia dla wystąpienia bazy danych Oracle źródła.

  ![Ekran Dodawanie szczegółów źródła](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Przekaż Sterownik Oracle OCI

1. Wybierz **Zapisz**, a następnie na **OCI zainstalować sterownik** ekranu, zaloguj się do swojego konta bazy danych Oracle i pobrać sterownik **instantclient-basiclite-windows.x64-12.2.0.1.0.zip**(37,128,586 Byte(s)) (SHA1 sumy kontrolnej: 865082268) z [tutaj](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Pobierz sterownik do folderu udostępnionego.

   Upewnij się, że folder jest udostępniony za pomocą nazwy użytkownika, który określony minimalny dostęp tylko do odczytu. Azure Database Migration Service uzyskuje dostęp do i odczytuje z udziału do przekazania sterownik OCI na platformie Azure, Personifikując nazwy użytkownika, które określisz.

   Nazwa użytkownika, które określisz musi być kontem użytkownika Windows.

   ![Instalacja sterownika OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz **Zapisz**, a następnie na **docelowe szczegóły** ekranu, określ szczegóły połączenia z docelową usługę Azure Database dla serwera PostgreSQL, który jest wstępnie przygotowany wystąpienia usługi Azure Database for PostgreSQL, do którego **HR** schemat został wdrożony.

    ![Ekran Szczegóły elementu docelowego](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych, co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelowej bazy danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

  Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

     ![Stan działania — uruchamianie](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. W obszarze **Nazwa bazy danych**, wybierz konkretnej bazy danych, aby przejść do stanu w ramach migracji **pełne ładowanie danych** i **Przyrostowa synchronizacja danych** operacji.

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).

     ![Stan działania — ukończono pełne ładowanie](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.

   ![Rozpoczynanie migracji jednorazowej](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Wybierz polecenie **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych jest wyświetlany **Ukończono**, łączenie aplikacji z nowy obiekt docelowy — Azure Database for postgresql w warstwie wystąpienia.

 > [!NOTE]
 > Ponieważ PostgreSQL, domyślnie ma schema.table.column w małych liter, możesz przywrócić z wielkiej litery na małe litery, przy użyciu skryptu w **Konfigurowanie schematu w usłudze Azure Database for PostgreSQL** sekcji we wcześniejszej części tego artykułu.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacji na temat usługi Azure Database Migration Service, zobacz artykuł [co to jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje o usłudze Azure Database for PostgreSQL, zobacz artykuł [co to jest Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
