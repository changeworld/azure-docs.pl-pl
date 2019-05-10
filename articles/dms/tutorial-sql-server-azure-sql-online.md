---
title: 'Samouczek: Używanie usługi Azure Database Migration Service do przeprowadzania migracji w trybie online z programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli) | Microsoft Docs'
description: Dowiedz się, w jaki sposób przeprowadzić migrację w trybie online z lokalnego programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli) za pomocą usługi Azure Database Migration Service.
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
ms.openlocfilehash: 266e4a16a69d7200fbe8b58bc20339b6979db877
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415921"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Samouczek: Migrowanie programu SQL Server do pojedynczej bazy danych lub bazy danych w puli usługi Azure SQL Database w trybie online przy użyciu usługi DMS

Usługa Azure Database Migration Service służy do migrowania baz danych z lokalnego wystąpienia programu SQL Server do usługi [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) przy minimalnym przestoju. W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** przywróconej do lokalnego wystąpienia programu SQL Server 2016 (lub nowszej wersji) do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), używając usługi Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Ocenianie lokalnej bazy danych za pomocą programu Data Migration Assistant.
> - Migrowanie przykładowego schematu przy użyciu programu Data Migration Assistant.
> - Tworzenie wystąpienia usługi Azure Database Migration Service.
> - Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> - Uruchamianie migracji.
> - Monitorowanie migracji.
> - Pobieranie raportu migracji.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację online z programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli). Aby uzyskać informacje o migracji offline, zobacz [Migracja programu SQL Server do usługi Azure SQL Database w trybie offline przy użyciu usługi DMS](tutorial-sql-server-to-azure-sql.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Pobrany i zainstalowany program [SQL Server 2012 lub nowszy](https://www.microsoft.com/sql-server/sql-server-downloads) (w dowolnej wersji).
- Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Utworzenie bazy danych usługi Azure SQL Database (pojedynczej lub w puli) — szczegółowe instrukcje znajdują w artykule [Tworzenie pojedynczej bazy danych usługi Azure SQL Database w witrynie Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Jeśli używasz usług SQL Server Integration Services (SSIS) i chcesz przeprowadzić migrację bazy danych katalogu dla projektów/pakietów SSIS (SSISDB) z programu SQL Server do usługi Azure SQL Database, docelowa baza danych SSISDB zostanie utworzona i będzie zarządzana automatycznie w Twoim imieniu po aprowizacji usług SSIS w usłudze Azure Data Factory (ADF). Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

- Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) w wersji 3.3 lub nowszej.
- Tworzenie usługi Azure Virtual Network (VNet) dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [usługiExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentacja usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a szczególnie artykułów Szybki Start krok po kroku szczegółowe informacje.

    > [!NOTE]
    > Podczas konfigurowania sieci wirtualnej, jeśli korzystasz z usługi ExpressRoute za pomocą komunikacji równorzędnej sieci do firmy Microsoft, Dodaj następujące usługi [punktów końcowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której będą udostępniane usługi:
    > - Docelowy punkt końcowy z bazy danych (na przykład SQL punktu końcowego, punktu końcowego usługi Cosmos DB i tak dalej)
    > - Punkt końcowy magazynu
    > - Punkt końcowy usługi Service bus
    >
    > Ta konfiguracja jest konieczne, ponieważ usługi Azure Database Migration Service nie ma łączności z Internetem.

- Upewnij się, że reguły sieci wirtualnej, sieciowej grupy zabezpieczeń nie blokują następujących portów komunikacji przychodzącej do usługi Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz zaporę systemu Windows, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
- Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
- W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
- Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla serwera usługi Azure SQL Database, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używane dla usługi Azure Database Migration Service.
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia z docelowym wystąpieniem usługi Azure SQL Database mają uprawnienie CONTROL DATABASE do docelowych baz danych Azure SQL Database.
- Źródłowy program SQL Server musi być w wersji SQL Server 2005 lub nowszej. Aby określić wersję uruchomionego wystąpienia programu SQL Server, zobacz artykuł [Jak określić wersję, wydanie i poziom aktualizacji programu SQL Server i jego składników](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Bazy danych muszą być w trybie odzyskiwania z niepełnym dziennikiem lub w trybie pełnego odzyskiwania. Aby określić model odzyskiwania skonfigurowany dla wystąpienia programu SQL Server, zobacz artykuł [Wyświetlanie lub zmiana modelu odzyskiwania bazy danych (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Upewnij się, że wykonano pełne kopie zapasowe baz danych. Aby wykonać pełną kopię zapasową bazy danych, zobacz artykuł [Instrukcje: wykonywanie pełnej kopii zapasowej bazy danych (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)).
- Jeśli jakiekolwiek tabele nie mają klucza podstawowego, włącz funkcję przechwytywania zmian danych (CDC, Change Data Capture) w bazie danych oraz określonych tabelach.
    > [!NOTE]
    > Możesz użyć poniższego skryptu, aby znaleźć tabele, które nie mają kluczy podstawowych.

    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
     ```

    >Jeśli wyniki wskażą, że co najmniej jedna tabela ma parametr „is_tracked_by_cdc” o wartości „0”, włącz przechwytywanie zmian dla bazy danych oraz określonych tabel, korzystając z procesu opisanego w artykule [Włączanie i wyłączanie funkcji przechwytywania zmian danych (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017).

- Skonfiguruj rolę dystrybutora dla źródłowego programu SQL Server.

    >[!NOTE]
    > Za pomocą poniższego zapytania możesz określić, czy zostały zainstalowane składniki replikacji.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    Jeśli w wyniku zwracany jest komunikat o błędzie z sugestią, aby zainstalować składniki replikacji, zainstaluj składniki replikacji programu SQL Server zgodnie z procedurą opisaną w artykule [Instalowanie replikacji programu SQL Server](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017).

    Jeśli replikacja została już zainstalowana, sprawdź, czy rola dystrybucji jest skonfigurowana w źródłowym programie SQL Server, uruchamiając poniższe polecenie T-SQL.

    ```sql
    EXEC sp_get_distributor;
    ```

    Jeśli nie skonfigurowano dystrybucji, a serwer dystrybucji wyświetla wartość NULL w wynikach powyższego polecenia, skonfiguruj dystrybucję przy użyciu wytycznych podanych w artykule [Konfigurowanie dystrybucji](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017).

- Wyłącz wyzwalacze bazy danych w docelowej usłudze Azure SQL Database.
    >[!NOTE]
    > Wyzwalacze bazy danych w docelowej usłudze Azure SQL Database możesz znaleźć przy użyciu następującego zapytania:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    Aby uzyskać więcej informacji, zobacz artykuł [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Ocena lokalnej bazy danych

Przed przeprowadzeniem migracji danych z lokalnego wystąpienia programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli) należy ocenić bazę danych programu SQL Server pod kątem problemów blokujących, które mogą uniemożliwić migrację. Korzystając z programu Data Migration Assistant w wersji 3.3 lub nowszej, wykonaj czynności opisane w artykule [Performing a SQL Server migration assessment (Ocena migracji programu SQL Server)](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem), aby ukończyć ocenę lokalnej bazy danych.

Aby ocenić lokalną bazę danych, wykonaj poniższe czynności:

1. W programie DMA wybierz ikonę New (Nowy), +, a następnie wybierz typ projektu **Assessment** (Ocena).
2. Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**, a następnie wybierz pozycję **Create**  (Utwórz), aby utworzyć projekt.

    Oceniając źródłową bazę danych programu SQL Server podczas migracji do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), możesz wybrać co najmniej jeden z następujących typów raportów oceny:

   - Check database compatibility (Sprawdzanie zgodności bazy danych)
   - Check feature parity (Sprawdzanie równoważności funkcji)

     Oba typy raportów są domyślnie zaznaczone.

3. W programie DMA na ekranie **Options** (Opcje) wybierz pozycję **Next** (Dalej).
4. Na ekranie **Select sources** (Wybierz źródła) w oknie dialogowym **Connect to a server** (Nawiązywanie połączenia z serwerem) podaj parametry połączenia z programem SQL Server, a następnie wybierz polecenie **Connect** (Połącz).
5. W oknie dialogowym **Add sources** (Dodawanie źródeł) wybierz opcję **AdventureWorks2012**, wybierz polecenie **Add** (Dodaj), a następnie wybierz pozycję **Start Assessment** (Rozpocznij ocenę).

    > [!NOTE]
    > Jeśli używasz usług SSIS, program DMA nie obsługuje obecnie oceny źródłowej bazy danych SSISDB. Jednak projekty/pakiety SSIS zostaną ocenione/zweryfikowane, ponieważ są ponownie wdrażane do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Po zakończeniu oceny zostaną wyświetlone wyniki podobne do widocznych na poniższej ilustracji:

    ![Ocena migracji danych](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    W przypadku baz danych usługi Azure SQL Database (pojedynczych lub w puli) oceny identyfikują problemy z równoważnością funkcji i blokowaniem migracji podczas wdrażania do pojedynczej bazy danych lub bazy danych w puli.

    - Kategoria **SQL Server feature parity** (Równoważność funkcji programu SQL Server) zapewnia rozbudowany zestaw zaleceń, alternatywne metody postępowania dostępne na platformie Azure i czynności korygujące, które pomogą Ci zaplanować projekty migracji.
    - Kategoria **Compatibility issues** (Problemy ze zgodnością) identyfikuje częściowo obsługiwane lub nieobsługiwane funkcje, oznaczające problemy ze zgodnością, które mogą spowodować zablokowanie migracji lokalnych baz danych programu SQL Server do usługi Azure SQL Database. Przedstawiane są również zalecenia, które pomogą Ci rozwiązać te problemy.

6. Zapoznaj się z wynikami oceny dotyczącymi problemów blokujących migrację oraz równoważności funkcji, wybierając odpowiednie opcje.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Jeśli ocena wypadła dobrze i wiesz, że wybrana baza danych nadaje się do migracji do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), użyj programu DMA, aby przeprowadzić migrację schematu do usługi Azure SQL Database.

> [!NOTE]
> Przed utworzeniem projektu migracji w programie DMA upewnij się, że masz już aprowizowaną bazę danych Azure SQL Database, zgodnie z wymaganiami wstępnymi. Do celów tego samouczka nazwa bazy danych usługi Azure SQL Database to **AdventureWorksAzure**, ale możesz podać dowolną inną nazwę.

> [!IMPORTANT]
> Jeśli korzystasz z usług SSIS, program DMA nie obsługuje obecnie migracji źródłowej bazy danych SSISDB, ale możesz ponownie wdrożyć swoje projekty/pakiety SSIS do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Aby przeprowadzić migrację **AdventureWorks2012** schematu do pojedynczej bazy danych lub baza danych w puli usługi Azure SQL Database, wykonaj następujące czynności:

1. W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie w obszarze **Project type** (Typ projektu) wybierz pozycję **Migration** (Migracja).
2. Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, a następnie w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**.
3. W obszarze **Migration Scope** (Zakres migracji), wybierz pozycję **Schema only** (Tylko schemat).

    Po wykonaniu poprzednich kroków interfejs programu DMA powinien wyglądać tak, jak pokazano na poniższej ilustracji:

    ![Tworzenie projektu programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.
5. W programie DMA określ szczegóły połączenia ze źródłowym wystąpieniem programu SQL Server, wybierz polecenie **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorks2012**.

    ![Szczegóły połączenia źródłowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Wybierz przycisk **Next** (Dalej), w obszarze **Connect to target server** (Połącz z serwerem docelowym) określ szczegóły połączenia z docelową bazą danych usługi Azure SQL Database, wybierz pozycję **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorksAzure**, która została już wcześniej aprowizowana w bazie danych usługi Azure SQL Database.

    ![Szczegóły połączenia docelowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. Wybierz polecenie **Next** (Dalej), aby przejść do ekranu **Select objects** (Wybieranie obiektów), na którym można określić obiekty schematu w bazie danych **AdventureWorks2012**, które muszą zostać wdrożone w usłudze Azure SQL Database.

    Domyślnie zaznaczone są wszystkie obiekty.

    ![Generowanie skryptów SQL](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Wybierz polecenie **Generate SQL script** (Wygeneruj skrypt SQL), aby utworzyć skrypty SQL, a następnie sprawdź skrypty pod kątem błędów.

    ![Skrypt schematu](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Wybierz polecenie **Deploy schema** (Wdróż schemat), aby wdrożyć schemat do usługi Azure SQL Database, a po wdrożeniu schematu sprawdź serwer docelowy pod kątem nieprawidłowości.

    ![Wdrażanie schematu](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service. 

5. Wybierz istniejącej sieci wirtualnej lub utworzyć nową.

    Sieć wirtualna udostępnia usługi Azure Database Migration Service dostęp do źródła programu SQL Server i wystąpieniem docelowym usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

    ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > Alternatywnie można wybrać **Tworzenie projektu** można teraz utworzyć projekt migracji i wykonaj migrację później.

6. Wybierz pozycję **Zapisz**.

7. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    ![Tworzenie i uruchamianie działania usługi Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego wystąpienia programu SQL Server.

    Upewnij się, że używasz w pełni kwalifikowanej nazwy domeny źródłowego wystąpienia programu SQL Server. Możesz również użyć adresu IP, jeśli rozpoznawanie nazw DNS nie jest możliwe.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze źródłowym, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Jeśli korzystasz z usług SSIS, program DMS nie obsługuje obecnie migracji źródłowej bazy danych SSISDB, ale możesz ponownie wdrożyć swoje projekty/pakiety SSIS do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz polecenie **Zapisz**, a następnie na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego serwera usługi Azure SQL Database, czyli dla aprowizowanej wcześniej bazy danych usługi Azure SQL Database, w której wdrożono schemat **AdventureWorks2012** za pomocą programu DMA.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych, co źródłowa baza danych, usługa Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Wybierz polecenie **Zapisz**, a następnie na ekranie **Wybierz tabele** rozwiń listę tabel i zapoznaj się z listą odpowiednich pól.

    Usługa Azure Database Migration Service automatycznie wybiera wszystkie puste tabele źródłowe istniejące w docelowym wystąpieniu usługi Azure SQL Database. Jeśli chcesz ponownie przeprowadzić migrację tabel, które już zawierają dane, musisz jawnie wybrać te tabele w tym bloku.

    ![Wybór tabel](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

- Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

    ![Stan działania — inicjowanie](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

2. Kliknij określoną bazę danych, aby uzyskać stan migracji dla operacji **Pełne ładowanie danych** oraz **Przyrostowa synchronizacja danych**.

    ![Stan działania — w toku](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpoczynanie migracji jednorazowej](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3. Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych będzie wyświetlać wartość **Ukończono**, połącz aplikacje z nową docelową usługą Azure SQL Database.

    ![Stan działania — ukończono](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>Kolejne kroki

- Warsztaty praktyczne: [SQL migration using Azure Data Migration Service (DMS) (Migrowanie baz danych SQL przy użyciu usługi Azure Database Migration Service)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure SQL Database, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure SQL Database](known-issues-azure-sql-online.md).
- Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Aby uzyskać informacje o usłudze Azure SQL Database, zobacz artykuł [Co to jest usługa Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
