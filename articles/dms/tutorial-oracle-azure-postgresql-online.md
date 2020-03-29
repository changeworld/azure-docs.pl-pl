---
title: 'Samouczek: Migrowanie usługi Oracle online do bazy danych platformy Azure dla postgreSQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z oracle lokalnie lub na maszynach wirtualnych do usługi Azure Database for PostgreSQL przy użyciu usługi migracji bazy danych azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 956523e2b51795a4bc97c653dab8b408b06061f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255566"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Samouczek: Migrowanie bazy danych Oracle do usługi Azure database dla postgreSql online przy użyciu dms (wersja zapoznawcza)

Usługi migracji bazy danych azure można użyć do migracji baz danych z baz danych Oracle hostowanych lokalnie lub na maszynach wirtualnych do [usługi Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) przy minimalnym przestoju. Innymi słowy można zakończyć migrację z minimalnym przestojem do aplikacji. W tym samouczku należy przeprowadzić migrację przykładowej bazy danych **działu danych z** lokalnego lub maszynowego wystąpienia oracle 11g do bazy danych platformy Azure dla postgreSQL przy użyciu działania migracji online w usłudze migracji bazy danych platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Oceń wysiłek migracji za pomocą narzędzia ora2pg.
> * Migrowanie przykładowego schematu przy użyciu narzędzia ora2pg.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu usługi migracji bazy danych platformy Azure.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

> [!NOTE]
> Korzystanie z usługi migracji bazy danych Azure do wykonywania migracji online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca utworzenie wystąpienia usługi migracji bazy danych platformy Azure w tym samym regionie platformy Azure co docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano sposób przeprowadzania migracji online z oracle do usługi Azure Database dla postgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Pobierz i zainstaluj [oracle 11g Release 2 (Standard Edition, Standard Edition One lub Enterprise Edition).](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* Pobierz przykładową **bazę danych HR** [tutaj](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Pobierz i [zainstaluj ora2pg na windowsie lub linuksie](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Utwórz wystąpienie usługi Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Połącz się z wystąpieniem i utwórz bazę danych, korzystając z instrukcji zawartej w tym [dokumencie](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.

  > [!NOTE]
  > Podczas konfigurowania sieci wirtualnej, jeśli używasz usługi ExpressRoute z komunikacją równorzędną sieciową do firmy Microsoft, dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usługi do podsieci, w której usługa będzie aprowizowana:
  >
  > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, punkt końcowy usługi Cosmos DB itd.)
  > * Punkt końcowy magazynu
  > * Punkt końcowy magistrali usług
  >
  > Ta konfiguracja jest konieczna, ponieważ usługa migracji bazy danych platformy Azure nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieciowej (NSG) sieci nie blokują następujących portów komunikacji przychodzącej do usługi migracji bazy danych Azure Database: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz zaporę systemu Windows, aby umożliwić usłudze migracji bazy danych platformy Azure dostęp do źródłowego serwera Oracle, który domyślnie jest portem TCP 1521.
* Podczas korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby umożliwić usłudze migracji usługi Azure Database dostęp do źródłowych baz danych w celu migracji.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla usługi Azure Database for PostgreSQL, aby umożliwić usłudze migracji usługi Azure Database dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi migracji bazy danych Azure.
* Włącz dostęp do źródłowych baz danych Oracle.

  > [!NOTE]
  > Rola DBA jest wymagana dla użytkownika, aby połączyć się ze źródłem Oracle.

  * Archiwum Dzienniki ponawiania jest wymagane do synchronizacji przyrostowej w usłudze migracji bazy danych Azure do przechwytywania zmian danych. Wykonaj następujące kroki, aby skonfigurować źródło Oracle:
    * Zaloguj się przy użyciu uprawnień SYSDBA, uruchamiając następujące polecenie:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Zamknij wystąpienie bazy danych, uruchamiając następujące polecenie.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Poczekaj na `'ORACLE instance shut down'`potwierdzenie .

    * Uruchom nowe wystąpienie i zainstaluj (ale nie otwieraj) bazy danych, aby włączyć lub wyłączyć archiwizację bu z uruchomionym następującym poleceniem:

      ```
      STARTUP MOUNT;
      ```

      Baza danych musi być zainstalowana; czekać na potwierdzenie "Instancja Oracle rozpoczęta".

    * Zmień tryb archiwizacji bazy danych, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Otwórz bazę danych dla normalnych operacji, uruchamiając następujące polecenie:

      ```
      ALTER DATABASE OPEN;
      ```

      Może być konieczne ponowne uruchomienie pliku ARC, aby się wyświetlić.

    * Aby zweryfikować, uruchom następujące polecenie:

      ```
      SELECT log_mode FROM v$database;
      ```

      Powinieneś otrzymać `'ARCHIVELOG'`odpowiedź . Jeśli odpowiedź `'NOARCHIVELOG'`jest , to wymaganie nie jest spełnione.

  * Włącz dodatkowe rejestrowanie dla replikacji przy użyciu jednej z następujących opcji.

    * **Wariant 1**.
      Zmień dodatkowe rejestrowanie na poziomie bazy danych, aby objąć wszystkie tabele z PK i unikatowym indeksem. Kwerenda detekcja zwróci `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Zmień dodatkowe rejestrowanie na poziomie tabeli. Uruchom tylko dla tabel, które mają manipulowanie danymi i nie mają PKs lub unikatowe indeksy.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Wariant 2**.
      Zmień dodatkowe rejestrowanie na poziomie bazy danych, aby objąć wszystkie tabele, a kwerenda wykrywająca zwraca . `'YES'`

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Zmień dodatkowe rejestrowanie na poziomie tabeli. Postępuj zgodnie z poniższą logiką, aby uruchomić tylko jedną instrukcję dla każdej tabeli.

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

    Aby zweryfikować, uruchom następujące polecenie:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Powinieneś otrzymać `'YES'`odpowiedź .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Ocena nakładu pracy w bazie danych Oracle na platformę Azure dla migracji postgreSQL

Zalecamy użycie ora2pg do oceny nakładu pracy wymaganego do migracji z oracle do usługi Azure Database dla postgreSQL. Użyj `ora2pg -t SHOW_REPORT` dyrektywy, aby utworzyć raport zawierający listę wszystkich obiektów Oracle, szacowany koszt migracji (w dniach dewelopera) i niektóre obiekty bazy danych, które mogą wymagać szczególnej uwagi w ramach konwersji.

Większość klientów poświęci dużo czasu na przejrzenie raportu z oceny i rozważenie automatycznego i ręcznego wysiłku konwersji.

Aby skonfigurować i uruchomić ora2pg, aby utworzyć raport oceny, zobacz **premigration: Assessment** sekcji [Oracle do Azure Database dla PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Przykładowy raport z oceny ora2pg jest dostępny w [tym](https://ora2pg.darold.net/report.html)miejscu.

## <a name="export-the-oracle-schema"></a>Eksportowanie schematu Oracle

Zaleca się użycie ora2pg do konwersji schematu Oracle i innych obiektów Oracle (typy, procedury, funkcje itp.) do schematu, który jest zgodny z usługą Azure Database for PostgreSQL. ora2pg zawiera wiele dyrektyw ułatwiające wstępne zdefiniowanie niektórych typów danych. Na przykład można użyć `DATA_TYPE` dyrektywy, aby zastąpić wszystkie NUMBER(*,0) bigintem, a nie numerem(38).

Można uruchomić ora2pg, aby wyeksportować każdy z obiektów bazy danych w plikach sql. Następnie można przejrzeć pliki .sql przed zaimportowaniem ich do usługi Azure Database for PostgreSQL przy użyciu psql lub wykonać plik . Skrypt SQL w PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Przykład:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Aby skonfigurować i uruchomić ora2pg do konwersji schematu, zobacz **migracja: Schemat i sekcja danych** oracle do usługi Azure Database dla [postgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Konfigurowanie schematu w usłudze Azure Database dla postgreSQL

Można przekonwertować schematy tabeli Oracle, procedury przechowywane, pakiety i inne obiekty bazy danych, aby były zgodne z postgres przy użyciu ora2pg przed rozpoczęciem potoku migracji w usłudze migracji usługi Azure Database Migration Service. Zobacz poniższe linki, jak pracować z ora2pg:

* [Instalowanie ora2pg w systemie Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Książka kucharska Oracle do migracji PostgreSQL na platformie Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Usługa migracji bazy danych platformy Azure można również utworzyć schemat tabeli PostgreSQL. Usługa uzyskuje dostęp do schematu tabeli w połączonym źródle Oracle i tworzy zgodny schemat tabeli w usłudze Azure Database for PostgreSQL. Pamiętaj, aby sprawdzić i sprawdzić format schematu w usłudze Azure Database dla PostgreSQL po zakończeniu tworzenia schematu i przenoszenia danych przez usługę Azure Database.

> [!IMPORTANT]
> Usługa migracji bazy danych platformy Azure tworzy tylko schemat tabeli; inne obiekty bazy danych, takie jak procedury przechowywane, pakiety, indeksy itp., nie są tworzone.

Należy również pamiętać, aby upuścić klucz obcy w docelowej bazie danych dla pełnego obciążenia do uruchomienia. Zapoznaj się **z sekcją Migruj przykładowy schemat** tego artykułu w tym [miejscu,](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) aby uzyskać skrypt, którego można użyć do upuszczenia klucza obcego. Użyj usługi migracji bazy danych Azure, aby uruchomić pełne ładowanie i synchronizację.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Gdy schemat tabeli PostgreSQL już istnieje

Jeśli utworzysz schemat PostgreSQL przy użyciu narzędzi, takich jak ora2pg przed rozpoczęciem przenoszenia danych za pomocą usługi migracji bazy danych platformy Azure, zamapuj tabele źródłowe na tabele docelowe w usłudze migracji bazy danych platformy Azure.

1. Podczas tworzenia nowego projektu oracle do usługi Azure Database dla projektu migracji PostgreSQL zostanie wyświetlony monit o wybranie docelowej bazy danych i schematu docelowego w kroku Wybierz schematy. Wypełnij docelową bazę danych i schemat docelowy.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Na ekranie **Ustawienia migracji** przedstawiono listę tabel w źródle Oracle. Usługa migracji bazy danych platformy Azure próbuje dopasować tabele w źródle i tabelach docelowych na podstawie nazwy tabeli. Jeśli istnieje wiele pasujących tabel docelowych z inną wielkością liter, można wybrać tabelę docelową, do której chcesz zamapować.

    ![Wyświetlanie subskrypcji w portalu](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Jeśli chcesz mapować nazwy tabel źródłowych do [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) tabel o różnych nazwach, e-mail i możemy dostarczyć skrypt do automatyzacji procesu.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Gdy schemat tabeli PostgreSQL nie istnieje

Jeśli docelowa baza danych PostgreSQL nie zawiera żadnych informacji o schemacie tabeli, usługa migracji bazy danych Azure Konwertuje schemat źródłowy i odtwarza go w docelowej bazie danych. Należy pamiętać, usługa migracji bazy danych Azure tworzy tylko schemat tabeli, a nie inne obiekty bazy danych, takie jak procedury przechowywane, pakiety i indeksy.
Aby usługa migracji bazy danych platformy Azure utworzyć schemat dla Ciebie, upewnij się, że środowisko docelowe zawiera schemat bez istniejących tabel. Jeśli usługa migracji bazy danych Azure odnajdzie dowolną tabelę, usługa zakłada, że schemat został utworzony przez narzędzie zewnętrzne, takie jak ora2pg.

> [!IMPORTANT]
> Usługa migracji bazy danych Platformy Azure wymaga, aby wszystkie tabele były tworzone w ten sam sposób, przy użyciu usługi migracji bazy danych azure lub narzędzia, takie jak ora2pg, ale nie oba.

Aby rozpocząć:

1. Utwórz schemat w docelowej bazie danych na podstawie wymagań aplikacji. Domyślnie schemat tabeli PostgreSQL i nazwy kolumn są małe. Schemat tabeli Oracle i kolumny, z drugiej strony, są domyślnie we wszystkich przypadkach kapitału.
2. W kroku Wybierz schematy określ docelową bazę danych i schemat docelowy.
3. Na podstawie schematu utworzonego w usłudze Azure Database for PostgreSQL usługa migracji bazy danych platformy Azure używa następujących reguł transformacji:

    Jeśli nazwa schematu w źródle Oracle i pasuje do tej w usłudze Azure Database for PostgreSQL, usługa migracji bazy danych Azure *tworzy schemat tabeli przy użyciu tego samego przypadku, co w docelowym*.

    Przykład:

    | Źródło schematu Oracle | Docelowa baza danych PostgreSQL.schema | DMS utworzył schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." KRAJÓW UE"." COUNTRY_ID" |
    | HR | targetHR.HR | "HR"." KRAJÓW UE"." COUNTRY_ID" |
    | HR | targetHR.Hr | *Nie można mapować spraw mieszanych |

    *Aby utworzyć schemat mieszanych przypadków i nazwy tabel w [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)docelowym postgreSql, skontaktuj się z nim . Możemy dostarczyć skrypt do konfigurowania schematu tabeli mieszanej sprawy w docelowej bazie danych PostgreSQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródła Oracle i docelowej bazy danych azure dla wystąpienia PostgreSQL.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

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
4. Na ekranie **Nowy projekt migracji** określ nazwę projektu w polu tekstowym Typ serwera **źródłowego** wybierz pozycję **Oracle**, w polu **tekstowym Typ serwera docelowego** wybierz pozycję **Azure Database for PostgreSQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych online**.

   ![Tworzenie projektu usługi Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternatywnie można wybrać **utwórz projekt tylko** do utworzenia projektu migracji teraz i wykonać migrację później.

6. Wybierz **pozycję Zapisz**, zwróć uwagę na wymagania dotyczące pomyślnego użycia usługi migracji bazy danych Azure do przeprowadzenia migracji online, a następnie wybierz pozycję **Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

* Na ekranie **Dodaj szczegóły źródła** określ szczegóły połączenia dla źródłowego wystąpienia Oracle.

  ![Ekran Dodawanie szczegółów źródła](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Prześlij sterownik Oracle OCI

1. Wybierz **zapisz**, a następnie na ekranie **Zainstaluj sterownik OCI,** zaloguj się na swoje konto Oracle i pobierz sterownik **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 Byte(s)) (SHA1 Suma kontrolna: 865082268) [stąd](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Pobierz sterownik do folderu udostępnionego.

   Upewnij się, że folder jest współużytkowana z nazwą użytkownika określoną przy minimalnym dostępie tylko do odczytu. Usługa migracji bazy danych azure uzyskuje dostęp i odczytuje z udziału, aby przekazać sterownik OCI na platformę Azure przez personifikacji nazwy użytkownika, którą określisz.

   Określona nazwa użytkownika musi być kontem użytkownika systemu Windows.

   ![Instalacja sterownika OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz **pozycję Zapisz**, a następnie na ekranie Szczegóły **docelowe** określ szczegóły połączenia dla docelowej bazy danych platformy Azure dla serwera PostgreSQL, która jest wstępnie aprowizowanym wystąpieniem usługi Azure Database for PostgreSQL, do którego wdrożono schemat **hr.**

    ![Ekran Szczegóły elementu docelowego](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera taką samą nazwę bazy danych jak źródłowa baza danych, usługa migracji bazy danych Azure Database domyślnie wybiera docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

  Zostanie wyświetlene okno aktywności migracji, a **stan** działania **jest inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

     ![Stan działania — bieganie](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. W obszarze **Nazwa bazy danych**wybierz określoną bazę danych, aby uzyskać dostęp do stanu migracji dla operacji pełnego ładowania **danych** i **przyrostowej synchronizacji danych.**

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).

     ![Stan działania — ukończono pełne ładowanie](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.

   ![Rozpoczynanie migracji jednorazowej](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Wybierz polecenie **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych pokazuje **zakończone,** połącz aplikacje z nowym docelowym wystąpieniem usługi Azure Database dla postgreSQL.

 > [!NOTE]
 > Ponieważ PostgreSQL domyślnie ma schema.table.column w małe litery, można przywrócić z wielkich liter do puchłych przy użyciu skryptu w **Konfigurowanie schematu w usłudze Azure Database for PostgreSQL** sekcji wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for PostgreSQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Aby uzyskać informacje na temat usługi migracji bazy danych platformy Azure, zobacz artykuł [Co to jest usługa migracji bazy danych platformy Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Aby uzyskać informacje na temat usługi Azure Database for PostgreSQL, zobacz artykuł [Co to jest usługa Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
