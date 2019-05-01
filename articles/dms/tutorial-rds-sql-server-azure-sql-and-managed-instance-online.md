---
title: 'Samouczek: Azure Database Migration Service na użytek online migracji usług pulpitu zdalnego programu SQL Server do usługi Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft'
description: Dowiedz się dokonać migracji online z usług pulpitu zdalnego programu SQL Server do usługi Azure SQL Database lub do usługi Azure SQL Database wystąpienie zarządzane przy użyciu usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: 75228098bcb62b83f8e93aebe600bffac62d6179
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727359"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Samouczek: Migrowanie usług pulpitu zdalnego programu SQL Server do usługi Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL Database i online przy użyciu usługi DMS
Azure Database Migration Service można użyć do migracji baz danych z wystąpienia programu SQL Server dla usług pulpitu zdalnego do [usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) lub [wystąpienie zarządzane usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) przy minimalnych przestojach. W tym samouczku, wykonywana jest migracja **Adventureworks2012** Przywrócono bazę danych do programu SQL Server dla usług pulpitu zdalnego wystąpienia programu SQL Server 2012 (lub nowszego) do usługi Azure SQL Database lub usługi Azure SQL Database wystąpienie zarządzane przy użyciu usługi Azure Database Migration Usługa.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Utwórz wystąpienie usługi Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL Database. 
> * Migrowanie przykładowego schematu przy użyciu programu Data Migration Assistant.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.
> * Pobieranie raportu migracji.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano online migracji z usług pulpitu zdalnego programu SQL Server do usługi Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

- Utworzenie [bazy danych usług pulpitu zdalnego programu SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
- Utworzenie wystąpienia usługi Azure SQL Database — szczegółowe instrukcje znajdują w artykule [Tworzenie bazy danych Azure SQL Database w witrynie Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Jeśli w przypadku migracji do wystąpienia zarządzanego usługi Azure SQL Database, postępuj zgodnie z szczegółowo w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started), a następnie utwórz pustą bazę danych o nazwie **AdventureWorks2012**. 
 
- Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) w wersji 3.3 lub nowszej.
- Utworzenie sieci wirtualnej platformy Azure dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager. W przypadku migrowania do wystąpienia zarządzanego usługi Azure SQL Database, upewnij się utworzyć wystąpienia usługi DMS w tej samej PODSIECI, które są używane dla wystąpienia zarządzanego usługi Azure SQL Database, ale w innej podsieci.  Alternatywnie, jeśli używasz innej sieci wirtualnej dla usługi DMS, musisz utworzyć równorzędną komunikację sieci wirtualnych między dwiema sieciami wirtualnymi.
 
    > [!NOTE]
    > Podczas konfigurowania sieci Wirtualnej, jeśli korzystasz z usługi ExpressRoute za pomocą komunikacji równorzędnej sieci do firmy Microsoft, Dodaj następujące usługi [punktów końcowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której będą udostępniane usługi:
    > - Docelowy punkt końcowy z bazy danych (na przykład SQL punktu końcowego, punktu końcowego usługi Cosmos DB i tak dalej)
    > - Punkt końcowy magazynu
    > - Punkt końcowy usługi Service bus
    >
    > Ta konfiguracja jest konieczne, ponieważ usługi Azure Database Migration Service nie ma łączności z Internetem. 
 
- Upewnij się, że reguły sieci Wirtualnej, sieciowej grupy zabezpieczeń nie blokują następujących portów komunikacji przychodzącej do usługi Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń usługi Azure VNET, zapoznaj się z artykułem [Filter network traffic with network security groups (Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz zaporę systemu Windows, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
- Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla serwera usługi Azure SQL Database, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi Azure Database Migration Service.
- Upewnij się, że poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem usług pulpitu zdalnego programu SQL Server są powiązane z kontem będącym członkiem roli serwera „Processadmin” i członkiem ról bazy danych „db_owner” dla wszystkich baz danych, które mają zostać poddane migracji.
- Upewnij się, że poświadczenia użyte do nawiąż połączenie z wystąpieniem usługi Azure SQL Database docelowej uprawnienie Kontrola bazy danych na docelowych baz danych Azure SQL i być członkiem roli sysadmin w przypadku migracji do usługi Azure SQL Database wystąpienie zarządzane.
- Źródłowe usługi pulpitu zdalnego programu SQL Server muszą być w wersji SQL Server 2012 lub nowszej. Aby określić wersję uruchomionego wystąpienia programu SQL Server, zobacz artykuł [Jak określić wersję, wydanie i poziom aktualizacji programu SQL Server i jego składników](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Włącz przechwytywanie zmian danych w bazie danych usług pulpitu zdalnego programu SQL Server i wszystkich tabelach użytkownika wybranych do migracji.
    > [!NOTE]
    > Aby włączyć przechwytywanie zmian danych w bazie danych usług pulpitu zdalnego programu SQL Server, możesz użyć poniższego skryptu.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Aby włączyć przechwytywanie zmian danych we wszystkich tabelach, możesz użyć poniższego skryptu.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
- Wyłącz wyzwalacze bazy danych w docelowej usłudze Azure SQL Database.
    > [!NOTE]
    > Wyzwalacze bazy danych w docelowej usłudze Azure SQL Database możesz znaleźć przy użyciu następującego zapytania:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Aby uzyskać więcej informacji, zobacz artykuł [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu
Użyj narzędzia DMA, aby przeprowadzić migrację schematu do usługi Azure SQL Database.

> [!NOTE]
> Przed utworzeniem projektu migracji w programie DMA upewnij się, że masz już aprowizowaną bazę danych Azure SQL Database, zgodnie z wymaganiami wstępnymi. Do celów tego samouczka nazwa bazy danych usługi Azure SQL Database to **AdventureWorks2012**, ale możesz podać dowolną inną nazwę.

Aby przeprowadzić migrację schematu **AdventureWorks2012** do usługi Azure SQL Database, wykonaj następujące czynności:

1.  W programie Data Migration Assistant wybierz ikonę New + (Nowy), a następnie w obszarze **Project type** (Typ projektu) wybierz pozycję **Migration** (Migracja).
2.  Wpisz nazwę projektu, w polu tekstowym **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**, a następnie w polu tekstowym **Target server type** (Typ serwera docelowego) wybierz pozycję **Azure SQL Database**.
3.  W obszarze **Migration Scope** (Zakres migracji), wybierz pozycję **Schema only** (Tylko schemat).

    Po wykonaniu poprzednich kroków interfejs programu DMA powinien wyglądać tak, jak pokazano na poniższej ilustracji:
    
    ![Tworzenie projektu programu Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4.  Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.
5.  W programie DMA określ szczegóły połączenia ze źródłowym wystąpieniem programu SQL Server, wybierz polecenie **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorks2012**.

    ![Szczegóły połączenia źródłowego programu Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6.  Wybierz przycisk **Next** (Dalej), w obszarze **Connect to target server** (Połącz z serwerem docelowym) określ szczegóły połączenia z docelową bazą danych usługi Azure SQL Database, wybierz pozycję **Connect** (Połącz), a następnie wybierz bazę danych **AdventureWorksAzure**, która została już wcześniej zaaprowizowana w bazie danych usługi Azure SQL Database.

    ![Szczegóły połączenia docelowego programu Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7.  Wybierz polecenie **Next** (Dalej), aby przejść do ekranu **Select objects** (Wybieranie obiektów), na którym można określić obiekty schematu w bazie danych **AdventureWorks2012**, które muszą zostać wdrożone w usłudze Azure SQL Database.

    Domyślnie zaznaczone są wszystkie obiekty.

    ![Generowanie skryptów SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8.  Wybierz polecenie **Generate SQL script** (Wygeneruj skrypt SQL), aby utworzyć skrypty SQL, a następnie sprawdź skrypty pod kątem błędów.

    ![Skrypt schematu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9.  Wybierz polecenie **Deploy schema** (Wdróż schemat), aby wdrożyć schemat do usługi Azure SQL Database, a po wdrożeniu schematu sprawdź serwer docelowy pod kątem nieprawidłowości.

    ![Wdrażanie schematu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration
1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.
 
   ![Wyświetlanie subskrypcji w portalu](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)
       
2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.
 
    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)
    
3.  Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.
 
    ![Rejestrowanie dostawcy zasobów](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia
1.  W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2.  Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.
 
    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3.  Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service. 

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usłudze Azure Database Migration Service dostęp do źródłowego wystąpienia programu SQL Server i docelowego wystąpienia usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową dla tej migracji online, ale pamiętaj o wybraniu warstwy cenowej Premium.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

     ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7.  Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji
Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.
 
      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.
 
     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)
 
3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **AWS RDS for SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Azure SQL Database**.

    > [!NOTE]
    > Wybierz typ serwera docelowego, **usługi Azure SQL Database** migracji do usługi Azure SQL Database do obu usługi Azure SQL Database pojedyncze bazy danych i również wystąpienie zarządzane.

5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    > [!IMPORTANT]
    > Pamiętaj o wybraniu **migracji danych w trybie Online**; jest w trybie offline nie są obsługiwane migracje dla tego scenariusza.

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternatywnie można wybrać **Tworzenie projektu** można teraz utworzyć projekt migracji i wykonaj migrację później.

6. Wybierz pozycję **Zapisz**.

7. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

    ![Tworzenie i uruchamianie działania usługi Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)
 
## <a name="specify-source-details"></a>Określanie szczegółów źródła
1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego wystąpienia programu SQL Server.
 
    Upewnij się, że używasz w pełni kwalifikowanej nazwy domeny źródłowego wystąpienia programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze źródłowym, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego
1. Wybierz polecenie **Zapisz**, a następnie na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego serwera usługi Azure SQL Database, czyli dla aprowizowanej wcześniej bazy danych usługi Azure SQL Database, w której wdrożono schemat **AdventureWorks2012** za pomocą programu DMA.

    ![Wybieranie obiektu docelowego](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych, co źródłowa baza danych, usługa Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Wybierz polecenie **Zapisz**, a następnie na ekranie **Wybierz tabele** rozwiń listę tabel i zapoznaj się z listą odpowiednich pól.

    Usługa Azure Database Migration Service automatycznie wybiera wszystkie puste tabele źródłowe istniejące w docelowym wystąpieniu usługi Azure SQL Database. Jeśli chcesz ponownie przeprowadzić migrację tabel, które już zawierają dane, musisz jawnie wybrać te tabele na tym ekranie.

    ![Wybór tabel](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4.  Wybierz pozycję **Zapisz** po ustawieniu następujących **zaawansowanych ustawień migracji online**.
    
    | Ustawienie | Opis |
    | ------------- | ------------- |
    | **Maksymalna liczba tabel ładowanych równolegle** | Określa liczbę tabel, które usługa DMS uruchamia równolegle podczas migracji. Wartość domyślna to 5, ale można ją ustawić na wartość optymalną, aby zaspokoić specyficzne potrzeby dotyczące migracji, oparte na dowolnej migracji w ramach weryfikacji koncepcji. |
    | **Gdy tabela źródłowa jest obcinana** | Określa, czy usługa DMS obcina tabelę docelową podczas migracji. To ustawienie może być przydatne, jeśli co najmniej jedną tabelę są obcinane w trakcie procesu migracji. |
    | **Skonfiguruj ustawienia dla danych obiektów wielkich (LOB)** | Określa, czy usługa DMS migruje nieograniczone dane obiektu LOB, czy też ogranicza migrowane dane obiektu LOB do określonego rozmiaru.  Gdy jest objęta limitem migracji danych obiektu LOB, wszystkie dane BIZNESOWE, po przekroczeniu tego limitu zostanie obcięta. Dla migracji środowiska produkcyjnego zaleca się wybrać pozycję **Zezwalaj na nieograniczone rozmiary obiektu LOB**, aby zapobiec utracie danych. Podczas zezwalania na nieograniczony rozmiar obiektu LOB zaznacz pole wyboru **Migruj dane obiektu LOB w jednym bloku, gdy rozmiar obiektu LOB jest mniejszy (w KB) niż określony**, aby zwiększyć wydajność. |
    
    ![Ustawianie zaawansowanych ustawień migracji online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5.  Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji
- Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

    ![Stan działania — inicjowanie](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji
1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Uruchomiono**.

2. Kliknij określoną bazę danych, aby uzyskać stan migracji dla operacji **Pełne ładowanie danych** oraz **Przyrostowa synchronizacja danych**.

    ![Stan działania — w toku](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej
Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpoczynanie migracji jednorazowej](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)
 
2.  Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3.  Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych będzie wyświetlać wartość **Ukończono**, połącz aplikacje z nową docelową usługą Azure SQL Database.
 
    ![Stan działania — ukończono](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure SQL Database, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure SQL Database](known-issues-azure-sql-online.md).
- Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Aby uzyskać informacje o usłudze Azure SQL Database, zobacz artykuł [Co to jest usługa Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
- Informacji na temat usługi Azure SQL Database, wystąpienia zarządzanego, zawiera strona [wystąpienia zarządzanego Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
