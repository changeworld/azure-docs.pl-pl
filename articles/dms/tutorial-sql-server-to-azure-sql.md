---
title: 'Samouczek: Przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie offline za pomocą usługi Azure Database Migration Service | Microsoft Docs'
description: Dowiedz się, w jaki sposób przeprowadzić migrację z lokalnego programu SQL Server do usługi Azure SQL Database w trybie offline za pomocą usługi Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 80f2a05c5a770043a8ff1da088be2ad4acb16768
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716450"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Samouczek: Przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie offline przy użyciu usługi DMS
Usługa Azure Database Migration Service służy do migrowania baz danych z lokalnego wystąpienia programu SQL Server do usługi [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** przywróconej do lokalnego wystąpienia programu SQL Server 2016 (lub nowszej wersji) do usługi Azure SQL Database, używając usługi Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Ocenianie lokalnej bazy danych za pomocą programu Data Migration Assistant.
> * Migrowanie przykładowego schematu przy użyciu programu Data Migration Assistant.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.
> * Pobieranie raportu migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację offline z programu SQL Server do usługi Azure SQL Database. Aby uzyskać informacje o migracji online, zobacz [Przeprowadzanie migracji z programu SQL Server do usługi Azure SQL Database w trybie online przy użyciu usługi DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

- Pobrany i zainstalowany program [SQL Server 2016 lub nowszy ](https://www.microsoft.com/sql-server/sql-server-downloads) (w dowolnej wersji).
- Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Utworzone wystąpienie usługi Azure SQL Database — szczegółowe instrukcje znajdują w artykule [Tworzenie bazy danych usługi Azure SQL Database w witrynie Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
- Sieć wirtualna dla usługi Azure Database Migration Service utworzona przy użyciu modelu wdrożenia usługi Azure Resource Manager, która zapewnia łączność między lokacjami dla lokalnych serwerów źródłowych, z użyciem usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub sieci [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Sprawdzenie, czy reguły sieciowej grupy zabezpieczeń sieci wirtualnej Azure Virtual Network nie blokują następujących portów komunikacji: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń usługi Azure VNET, zapoznaj się z artykułem [Filter network traffic with network security groups (Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz zaporę systemu Windows, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
- Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
- W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
- Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla serwera usługi Azure SQL Database, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi Azure Database Migration Service.
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Sprawdź, czy poświadczenia użyte do nawiązania połączenia z docelowym wystąpieniem usługi Azure SQL Database mają uprawnienie CONTROL DATABASE do docelowych baz danych Azure SQL Database.

## <a name="assess-your-on-premises-database"></a>Ocena lokalnej bazy danych
Przed przeprowadzeniem migracji danych z lokalnego wystąpienia programu SQL Server do usługi Azure SQL Database należy ocenić bazę danych programu SQL Server pod kątem problemów blokujących, które mogą uniemożliwić migrację. Korzystając z programu Data Migration Assistant w wersji 3.3 lub nowszej, wykonaj czynności opisane w artykule [Performing a SQL Server migration assessment (Ocena migracji programu SQL Server)](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem), aby ukończyć ocenę lokalnej bazy danych. W skrócie należy wykonać następujące czynności:
1.  W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie wybierz typ projektu **Assessment** (Ocena).
2.  Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**, a następnie wybierz pozycję **Create**  (Utwórz), aby utworzyć projekt.

    Oceniając źródłową bazę danych programu SQL Server podczas migracji do usługi Azure SQL Database, możesz wybrać co najmniej jeden z następujących typów raportów oceny:
    - Check database compatibility (Sprawdzanie zgodności bazy danych)
    - Check feature parity (Sprawdzanie równoważności funkcji)

    Oba typy raportów są domyślnie zaznaczone.

3.  W programie Data Migration Assistant na ekranie **Options** (Opcje) wybierz polecenie **Next** (Dalej).
4.  Na ekranie **Select sources** (Wybierz źródła) w oknie dialogowym **Connect to a server** (Nawiązywanie połączenia z serwerem) podaj parametry połączenia z programem SQL Server, a następnie wybierz polecenie **Connect** (Połącz).
5.  W oknie dialogowym **Add sources** (Dodawanie źródeł) wybierz opcję **AdventureWorks2012**, wybierz polecenie **Add** (Dodaj), a następnie wybierz pozycję **Start Assessment** (Rozpocznij ocenę).

    Po zakończeniu oceny zostaną wyświetlone wyniki podobne do widocznych na poniższej ilustracji:

    ![Ocena migracji danych](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    W przypadku usługi Azure SQL Database oceny identyfikują problemy z równoważnością funkcji i z blokowaniem migracji.

    - Kategoria **SQL Server feature parity** (Równoważność funkcji programu SQL Server) zapewnia rozbudowany zestaw zaleceń, alternatywne metody postępowania dostępne na platformie Azure i czynności korygujące, które pomogą Ci zaplanować projekty migracji.
    - Kategoria **Compatibility issues** (Problemy ze zgodnością) identyfikuje częściowo obsługiwane lub nieobsługiwane funkcje, oznaczające problemy ze zgodnością, które mogą spowodować zablokowanie migracji lokalnych baz danych programu SQL Server do usługi Azure SQL Database. Przedstawiane są również zalecenia, które pomogą Ci rozwiązać te problemy.

6.  Zapoznaj się z wynikami oceny dotyczącymi problemów blokujących migrację oraz równoważności funkcji, wybierając odpowiednie opcje.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu
Jeśli ocena wypadła dobrze i wiesz, że wybrana baza danych nadaje się do migracji do usługi Azure SQL Database, użyj programu Data Migration Assistant, aby przeprowadzić migrację schematu do usługi Azure SQL Database.

> [!NOTE]
> Przed utworzeniem projektu migracji w programie Data Migration Assistant upewnij się, że masz już zaaprowizowaną bazę danych usługi Azure SQL Database, zgodnie z wymaganiami wstępnymi. Do celów tego samouczka nazwa bazy danych usługi Azure SQL Database to **AdventureWorksAzure**, ale możesz podać dowolną inną nazwę.

Aby przeprowadzić migrację schematu **AdventureWorks2012** do usługi Azure SQL Database, wykonaj następujące czynności:

1.  W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie w obszarze **Project type** (Typ projektu) wybierz pozycję **Migration** (Migracja).
2.  Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, a następnie w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**.
3.  W obszarze **Migration Scope** (Zakres migracji), wybierz pozycję **Schema only** (Tylko schemat).

    Po wykonaniu poprzednich kroków interfejs programu Data Migration Assistant powinien wyglądać tak, jak pokazano na poniższej ilustracji:
    
    ![Tworzenie projektu programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4.  Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.
5.  W programie Data Migration Assistant określ szczegóły połączenia ze źródłowym wystąpieniem programu SQL Server, wybierz polecenie **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorks2012**.

    ![Szczegóły połączenia źródłowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6.  Wybierz polecenie **Next** (Dalej), w obszarze **Connect to target server** (Połącz z serwerem docelowym) określ szczegóły połączenia z docelową bazą danych usługi Azure SQL Database, wybierz polecenie **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorksAzure**, która została już wcześniej aprowizowana w bazie danych usługi Azure SQL Database.

    ![Szczegóły połączenia docelowego programu Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7.  Wybierz polecenie **Next** (Dalej), aby przejść do ekranu **Select objects** (Wybieranie obiektów), na którym można określić obiekty schematu w bazie danych **AdventureWorks2012**, które muszą zostać wdrożone w usłudze Azure SQL Database.

    Domyślnie zaznaczone są wszystkie obiekty.

    ![Generowanie skryptów SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8.  Wybierz polecenie **Generate SQL script** (Wygeneruj skrypt SQL), aby utworzyć skrypty SQL, a następnie sprawdź skrypty pod kątem błędów.

    ![Skrypt schematu](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9.  Wybierz polecenie **Deploy schema** (Wdróż schemat), aby wdrożyć schemat do usługi Azure SQL Database, a po wdrożeniu schematu sprawdź serwer docelowy pod kątem nieprawidłowości.

    ![Wdrażanie schematu](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration
1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.
 
   ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)
       
2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.
 
    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)
    
3.  Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.
 
    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia
1.  W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2.  Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.
 
    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3.  Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service. 

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usłudze Azure Database Migration Service dostęp do źródłowego wystąpienia programu SQL Server i docelowego wystąpienia usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    Jeśli potrzebujesz pomocy podczas wybierania właściwej warstwy usługi Azure Database Migration Service, zapoznaj się zaleceniami [tutaj](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7.  Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji
Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.
 
      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.
 
     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)
 
3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**. 

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5.  Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła
1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego wystąpienia programu SQL Server.
 
    Upewnij się, że używasz w pełni kwalifikowanej nazwy domeny źródłowego wystąpienia programu SQL Server. Możesz również użyć adresu IP, jeśli rozpoznawanie nazw DNS nie jest możliwe.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze źródłowym, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego
1. Wybierz polecenie **Zapisz**, a następnie na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego serwera usługi Azure SQL Database, czyli dla aprowizowanej wcześniej bazy danych usługi Azure SQL Database, w której wdrożono schemat **AdventureWorks2012** za pomocą programu Data Migration Assistant.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych, co źródłowa baza danych, usługa Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Wybierz polecenie **Zapisz**, a następnie na ekranie **Wybierz tabele** rozwiń listę tabel i zapoznaj się z listą odpowiednich pól.

    Należy pamiętać, że usługa Azure Database Migration Service automatycznie wybiera wszystkie puste tabele źródłowe istniejące w docelowym wystąpieniu usługi Azure SQL Database. Jeśli chcesz ponownie przeprowadzić migrację tabel, które już zawierają dane, musisz jawnie wybrać te tabele w tym bloku.

    ![Wybór tabel](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4.  Wybierz polecenie **Zapisz**, a następnie na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

5. Rozwiń sekcję **Opcja weryfikacji**, aby wyświetlić ekran **Wybierz opcję weryfikacji**, a następnie określ, czy przeprowadzić weryfikację baz danych po migracji pod kątem **porównania schematów**, **spójności danych** oraz **poprawności zapytań**.
    
    ![Wybieranie opcji weryfikacji](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6.  Wybierz polecenie **Zapisz**, a następnie zapoznaj się z podsumowaniem, aby upewnić się, że dane źródła i elementu docelowego są zgodne z tymi, które wcześniej określono.

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

 * Warsztaty praktyczne: [SQL migration using Azure Data Migration Service (DMS) (Migrowanie baz danych SQL przy użyciu usługi Azure Database Migration Service)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).