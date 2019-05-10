---
title: 'Samouczek: Używanie usługi Azure Database Migration Service do przeprowadzania migracji w trybie offline z programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli) | Microsoft Docs'
description: Dowiedz się, w jaki sposób przeprowadzić migrację z lokalnego programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli) w trybie offline za pomocą usługi Azure Database Migration Service.
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
ms.openlocfilehash: 22e55379c79678a2792f439137e8d58d03cd8377
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415090"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Samouczek: Migrowanie programu SQL Server do pojedynczej bazy danych lub bazy danych w puli usługi Azure SQL Database w trybie offline przy użyciu usługi DMS

Usługa Azure Database Migration Service służy do migrowania baz danych z lokalnego wystąpienia programu SQL Server do usługi [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** przywróconej do lokalnego wystąpienia programu SQL Server 2016 (lub nowszej wersji) do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), używając usługi Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Ocenianie lokalnej bazy danych za pomocą programu Data Migration Assistant.
> - Migrowanie przykładowego schematu przy użyciu programu Data Migration Assistant.
> - Tworzenie wystąpienia usługi Azure Database Migration Service.
> - Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> - Uruchamianie migracji.
> - Monitorowanie migracji.
> - Pobieranie raportu migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację w trybie offline z programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli). Aby uzyskać informacje o migracji online, zobacz [Przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie online przy użyciu usługi DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Pobrany i zainstalowany program [SQL Server 2016 lub nowszy ](https://www.microsoft.com/sql-server/sql-server-downloads) (w dowolnej wersji).
- Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Utworzenie bazy danych usługi Azure SQL Database (pojedynczej lub w puli) — szczegółowe instrukcje znajdują w artykule [Tworzenie pojedynczej bazy danych usługi Azure SQL Database w witrynie Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Jeśli używasz usług SQL Server Integration Services (SSIS) i chcesz przeprowadzić migrację bazy danych katalogu dla projektów/pakietów SSIS (SSISDB) z programu SQL Server do usługi Azure SQL Database, docelowa baza danych SSISDB zostanie utworzona i będzie zarządzana automatycznie w Twoim imieniu po aprowizacji usług SSIS w usłudze Azure Data Factory (ADF). Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
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
- Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) adresów IP na poziomie serwera dla serwera usługi Azure SQL Database, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi Azure Database Migration Service.
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia z docelowym wystąpieniem usługi Azure SQL Database mają uprawnienie CONTROL DATABASE do docelowych baz danych Azure SQL Database.

## <a name="assess-your-on-premises-database"></a>Ocena lokalnej bazy danych

Przed przeprowadzeniem migracji danych z lokalnego wystąpienia programu SQL Server do bazy danych usługi Azure SQL Database (pojedynczej lub w puli) należy ocenić bazę danych programu SQL Server pod kątem problemów blokujących, które mogą uniemożliwić migrację. Korzystając z programu Data Migration Assistant w wersji 3.3 lub nowszej, wykonaj czynności opisane w artykule [Performing a SQL Server migration assessment (Ocena migracji programu SQL Server)](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem), aby ukończyć ocenę lokalnej bazy danych. W skrócie należy wykonać następujące czynności:

1. W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie wybierz typ projektu **Assessment** (Ocena).
2. Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**, a następnie wybierz pozycję **Create**  (Utwórz), aby utworzyć projekt.

    Oceniając źródłową bazę danych programu SQL Server podczas migracji do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), możesz wybrać co najmniej jeden z następujących typów raportów oceny:

   - Check database compatibility (Sprawdzanie zgodności bazy danych)
   - Check feature parity (Sprawdzanie równoważności funkcji)

     Oba typy raportów są domyślnie zaznaczone.

3. W programie Data Migration Assistant na ekranie **Options** (Opcje) wybierz polecenie **Next** (Dalej).
4. Na ekranie **Select sources** (Wybierz źródła) w oknie dialogowym **Connect to a server** (Nawiązywanie połączenia z serwerem) podaj parametry połączenia z programem SQL Server, a następnie wybierz polecenie **Connect** (Połącz).
5. W oknie dialogowym **Add sources** (Dodawanie źródeł) wybierz opcję **AdventureWorks2012**, wybierz polecenie **Add** (Dodaj), a następnie wybierz pozycję **Start Assessment** (Rozpocznij ocenę).

    > [!NOTE]
    > Jeśli używasz usług SSIS, program DMA nie obsługuje obecnie oceny źródłowej bazy danych SSISDB. Jednak projekty/pakiety SSIS zostaną ocenione/zweryfikowane, ponieważ są ponownie wdrażane do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Po zakończeniu oceny zostaną wyświetlone wyniki podobne do widocznych na poniższej ilustracji:

    ![Ocena migracji danych](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    W przypadku baz danych usługi Azure SQL Database (pojedynczych lub w puli) oceny identyfikują problemy z równoważnością funkcji i blokowaniem migracji podczas wdrażania do pojedynczej bazy danych lub bazy danych w puli.

    - Kategoria **SQL Server feature parity** (Równoważność funkcji programu SQL Server) zapewnia rozbudowany zestaw zaleceń, alternatywne metody postępowania dostępne na platformie Azure i czynności korygujące, które pomogą Ci zaplanować projekty migracji.
    - Kategoria **Compatibility issues** (Problemy ze zgodnością) identyfikuje częściowo obsługiwane lub nieobsługiwane funkcje, oznaczające problemy ze zgodnością, które mogą spowodować zablokowanie migracji lokalnych baz danych programu SQL Server do usługi Azure SQL Database. Przedstawiane są również zalecenia, które pomogą Ci rozwiązać te problemy.

6. Zapoznaj się z wynikami oceny dotyczącymi problemów blokujących migrację oraz równoważności funkcji, wybierając odpowiednie opcje.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu

Jeśli ocena wypadła dobrze i wiesz, że wybrana baza danych nadaje się do migracji do bazy danych usługi Azure SQL Database (pojedynczej lub w puli), użyj programu DMA, aby przeprowadzić migrację schematu do usługi Azure SQL Database.

> [!NOTE]
> Przed utworzeniem projektu migracji w programie Data Migration Assistant upewnij się, że masz już zaaprowizowaną bazę danych Azure SQL Database, zgodnie z wymaganiami wstępnymi. Do celów tego samouczka nazwa bazy danych usługi Azure SQL Database to **AdventureWorksAzure**, ale możesz podać dowolną inną nazwę.
> [!IMPORTANT]
> Jeśli korzystasz z usług SSIS, program DMA nie obsługuje obecnie migracji źródłowej bazy danych SSISDB, ale możesz ponownie wdrożyć swoje projekty/pakiety SSIS do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Aby przeprowadzić migrację **AdventureWorks2012** schematu do pojedynczej bazy danych lub baza danych w puli usługi Azure SQL Database, wykonaj następujące czynności:

1. W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie w obszarze **Project type** (Typ projektu) wybierz pozycję **Migration** (Migracja).
2. Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, a następnie w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**.
3. W obszarze **Migration Scope** (Zakres migracji), wybierz pozycję **Schema only** (Tylko schemat).

    Po wykonaniu poprzednich kroków interfejs programu Data Migration Assistant powinien wyglądać tak, jak pokazano na poniższej ilustracji:

    ![Tworzenie projektu programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.
5. W programie Data Migration Assistant określ szczegóły połączenia ze źródłowym wystąpieniem programu SQL Server, wybierz polecenie **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorks2012**.

    ![Szczegóły połączenia źródłowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Wybierz polecenie **Next** (Dalej), w obszarze **Connect to target server** (Połącz z serwerem docelowym) określ szczegóły połączenia z docelową bazą danych Azure SQL Database, wybierz polecenie **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorksAzure**, która została już wcześniej aprowizowana w bazie danych Azure SQL Database.

    ![Szczegóły połączenia docelowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Wybierz polecenie **Next** (Dalej), aby przejść do ekranu **Select objects** (Wybieranie obiektów), na którym można określić obiekty schematu w bazie danych **AdventureWorks2012**, które muszą zostać wdrożone w usłudze Azure SQL Database.

    Domyślnie zaznaczone są wszystkie obiekty.

    ![Generowanie skryptów SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Wybierz polecenie **Generate SQL script** (Wygeneruj skrypt SQL), aby utworzyć skrypty SQL, a następnie sprawdź skrypty pod kątem błędów.

    ![Skrypt schematu](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Wybierz polecenie **Deploy schema** (Wdróż schemat), aby wdrożyć schemat do usługi Azure SQL Database, a po wdrożeniu schematu sprawdź serwer docelowy pod kątem nieprawidłowości.

    ![Wdrażanie schematu](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service.

5. Wybierz istniejącej sieci wirtualnej lub utworzyć nową.

    Sieć wirtualna udostępnia usługi Azure Database Migration Service dostęp do źródła programu SQL Server i wystąpieniem docelowym usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

     ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

    ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**. 

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego wystąpienia programu SQL Server.

    Upewnij się, że używasz w pełni kwalifikowanej nazwy domeny źródłowego wystąpienia programu SQL Server. Możesz również użyć adresu IP, jeśli rozpoznawanie nazw DNS nie jest możliwe.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze źródłowym, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Jeśli korzystasz z usług SSIS, program DMS nie obsługuje obecnie migracji źródłowej bazy danych SSISDB, ale możesz ponownie wdrożyć swoje projekty/pakiety SSIS do docelowej bazy danych SSISDB hostowanej przez usługę Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Wybierz polecenie **Zapisz**, a następnie na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego serwera usługi Azure SQL Database, czyli dla aprowizowanej wcześniej bazy danych usługi Azure SQL Database, w której wdrożono schemat **AdventureWorks2012** za pomocą programu Data Migration Assistant.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych, co źródłowa baza danych, usługa Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Wybierz polecenie **Zapisz**, a następnie na ekranie **Wybierz tabele** rozwiń listę tabel i zapoznaj się z listą odpowiednich pól.

    Należy pamiętać, że usługa Azure Database Migration Service automatycznie wybiera wszystkie puste tabele źródłowe istniejące w docelowym wystąpieniu usługi Azure SQL Database. Jeśli chcesz ponownie przeprowadzić migrację tabel, które już zawierają dane, musisz jawnie wybrać te tabele w tym bloku.

    ![Wybór tabel](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Wybierz polecenie **Zapisz**, a następnie na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

5. Rozwiń sekcję **Opcja weryfikacji**, aby wyświetlić ekran **Wybierz opcję weryfikacji**, a następnie określ, czy przeprowadzić weryfikację baz danych po migracji pod kątem **porównania schematów**, **spójności danych** oraz **poprawności zapytań**.

    ![Wybieranie opcji weryfikacji](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Wybierz polecenie **Zapisz**, a następnie zapoznaj się z podsumowaniem, aby upewnić się, że dane źródła i elementu docelowego są zgodne z tymi, które wcześniej określono.

    ![Podsumowanie migracji](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

- Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Oczekujący**.

    ![Stan działania](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

    ![Stan działania — ukończono](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Po zakończeniu migracji wybierz polecenie **Pobierz raport**, aby pobrać raport zawierający szczegółowe informacje o procesie migracji.

3. Sprawdź docelową bazę danych na serwerze docelowym usługi Azure SQL Database.

### <a name="additional-resources"></a>Dodatkowe zasoby

- Warsztaty praktyczne: [SQL migration using Azure Data Migration Service (DMS) (Migrowanie baz danych SQL przy użyciu usługi Azure Database Migration Service)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure SQL Database, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure SQL Database](known-issues-azure-sql-online.md).
- Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Aby uzyskać informacje o usłudze Azure SQL Database, zobacz artykuł [Co to jest usługa Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
