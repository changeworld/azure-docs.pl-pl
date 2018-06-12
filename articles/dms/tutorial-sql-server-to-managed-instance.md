---
title: Usługa DMS umożliwia migrację do wystąpienia zarządzane bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się przeprowadzić migrację z lokalnej instalacji programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure za pomocą usługi Azure migracji bazy danych.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: 86af0101d84fe9cd44211a931567a85d7b5166e0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261614"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrowanie serwera SQL do Azure wystąpienia bazy danych SQL zarządzane przy użyciu DMS
Usługa Azure bazy danych migracji umożliwia migrację bazy danych z lokalnego wystąpienia programu SQL Server do [wystąpienia zarządzane bazy danych SQL Azure](../sql-database/sql-database-managed-instance.md). Aby dodatkowe metody, które mogą wymagać pewnych nakładu pracy, zobacz artykuł [migracji wystąpienie programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Projekty migracji z programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure są w wersji zapoznawczej i podlegają [uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym samouczku, wykonywana jest migracja **Adventureworks2012** bazy danych z lokalnego wystąpienia programu SQL Server do usługi Azure wystąpienia bazy danych SQL zarządzane przy użyciu usługi Azure bazy danych migracji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Utwórz wystąpienie usługi migracji bazy danych Azure.
> * Utwórz projekt migracji za pomocą usługi Azure migracji bazy danych.
> * Uruchom migrację.
> * Monitoruj migrację.
> * Pobierz raport migracji.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka, musisz:

- Tworzenie sieci Wirtualnej dla usługi Azure migracji bazy danych przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia połączenie lokacja lokacja z serwerami lokalnymi źródła przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Dowiedz się topologii sieci migracji wystąpienia zarządzane bazy danych SQL Azure przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi).
- Upewnij się, że Twoje Azure (VNET) sieciowej grupy zabezpieczeń sieci wirtualnej reguły blok następujący komunikat porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na filtrowanie ruchu NSG sieci Wirtualnej Azure, zobacz artykuł [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurowanie sieci [zapory systemu Windows dla dostępu aparatu bazy danych źródła](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz Zaporę systemu Windows, aby umożliwić usłudze migracji bazy danych Azure dostępu do źródła SQL Server, która domyślnie jest TCP port 1433.
- Jeśli używasz wielu wystąpień programu SQL Server o nazwie przy użyciu portów dynamicznych, możesz włączyć usługę Przeglądarka SQL i umożliwiają dostęp do portu UDP 1434 na zaporach, dzięki czemu usługa migracji bazy danych Azure mogą łączyć się nazwanego wystąpienia w źródle serwer.
- Jeśli korzystasz z urządzenia zapory przed źródła baz danych, może być konieczne dodanie reguły zapory, aby umożliwić dostęp do baz danych źródła dla plików za pośrednictwem portu SMB 445, a także migracji z usługi migracji bazy danych Azure.
- Utwórz wystąpienie zarządzane bazy danych SQL Azure, wykonując szczegółowo w artykule [utworzyć wystąpienia zarządzanego bazy danych SQL Azure w portalu Azure](https://aka.ms/sqldbmi).
- Sprawdź, czy dane logowania umożliwiający połączenie źródła programu SQL Server i zarządzane wystąpienia docelowego są członkowie roli serwera sysadmin.
- Utwórz udział sieciowy, który usługa migracji bazy danych Azure umożliwia tworzenie kopii zapasowej źródłowej bazy danych.
- Upewnij się, że konto usługi uruchamiania źródła ma wystąpienie programu SQL Server uprawnienia do zapisu na udział sieciowy, który został utworzony i czy konto komputera serwera źródłowego ma dostęp do odczytu i zapisu do tego samego udziału.
- Zwróć uwagę użytkownika systemu Windows (i hasło), który ma uprawnienia pełnej kontroli w udziale sieciowym, która została wcześniej utworzona. Usługa Azure bazy danych migracji personifikuje poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure dla operacji przywracania.
- Tworzenie kontenera obiektu blob i pobrać jego identyfikatora URI, wykonując kroki opisane w artykule [zasobów zarządzania magazynu obiektów Blob Azure Eksplorator magazynu](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), pamiętaj zaznaczyć wszystkie uprawnienia (Odczyt, zapis, usuwanie, listy) w oknie zasad podczas Tworzenie Identyfikatora URI. Ta szczegóły udostępnia usługę migracji bazy danych Azure dostęp do Twojego kontenera konta magazynu o przekazywaniu używany do migracji plików kopii zapasowej baz danych do wystąpienia zarządzane bazy danych SQL Azure

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się do portalu Azure, wybierz opcję **wszystkie usługi**, a następnie wybierz **subskrypcje**.

    ![Pokaż portalu subskrypcji](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Pokaż dostawców zasobów](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Wyszukiwanie do migracji, a następnie po prawej stronie **Microsoft.DataMigration**, wybierz pozycję **zarejestrować**.

    ![Rejestrowanie dostawcy zasobów](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Utwórz wystąpienie usługi migracji bazy danych Azure

1. W portalu Azure wybierz + **Utwórz zasób**, wyszukaj **usługi migracji bazy danych Azure**, a następnie wybierz **usługi migracji bazy danych Azure** z listy rozwijanej Lista.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Na **usługi migracji bazy danych Azure** ekranu wybierz **Utwórz**.

    ![Utwórz wystąpienie usługi migracji bazy danych Azure](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Na **Utwórz usługę migracji** ekranu, określ nazwę usługi, subskrypcji i grupy nowy lub istniejący zasób.

4. Wybierz istniejącą sieć wirtualną (VNET) lub utwórz je.
 
    Sieć wirtualna zawiera usługę migracji bazy danych Azure dostęp do źródła programu SQL Server i docelowego wystąpienia zarządzane bazy danych SQL Azure.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci Wirtualnej w portalu Azure, zobacz artykuł [utworzyć sieć wirtualną przy użyciu portalu Azure](https://aka.ms/DMSVnet).

    Aby uzyskać dodatkowe szczegóły, zobacz artykuł [sieci topologii dla migracji wystąpienia zarządzane bazy danych SQL Azure przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na kosztów i warstwy cenowej, zobacz [cennikiem](https://aka.ms/dms-pricing).
   
    ![Tworzenie usługi DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Wybierz **Utwórz** można utworzyć usługi.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znalezienie go w portalu Azure, otwórz go, a następnie utwórz nowy projekt migracji.

1. W portalu Azure wybierz **wszystkie usługi**, wyszukaj usługi migracji bazy danych Azure, a następnie wybierz **usługi migracji bazy danych Azure**.

    ![Znajdź wszystkie wystąpienia usługi migracji bazy danych Azure](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Na **ekranu z migracji bazy danych Azure**, wyszukaj nazwę wystąpienia utworzone, a następnie wybierz wystąpienie.
 
3. Wybierz + **nowy projekt migracji**.

4. Na **nowy projekt migracji** ekranu, należy określić nazwę projektu, w **typ serwera źródła** pole tekstowe, wybierz opcję **programu SQL Server**, a następnie w **docelowego Typ serwera** pole tekstowe, wybierz opcję **wystąpienia zarządzane bazy danych SQL Azure**.

   ![Utwórz projekt DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Wybierz **Utwórz** Aby utworzyć projekt.

## <a name="specify-source-details"></a>Określ szczegóły źródła

1. Na **źródła szczegóły** ekranu, określ szczegóły połączenia dla źródła SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, wybierz **zaufania certyfikatów serwera** pole wyboru.

    Zaufanego certyfikatu nie jest zainstalowany, programu SQL Server generuje certyfikat z podpisem własnym, po uruchomieniu tego wystąpienia. Ten certyfikat służy do szyfrowania poświadczeń dla połączeń klienta.

    > [!CAUTION]
    > Połączeń SSL, które są szyfrowane za pomocą certyfikatu z podpisem własnym nie zapewnia dużego bezpieczeństwa. Są one narażony na ataki man-in--middle. Nie należy polegać na protokołu SSL przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Wybierz pozycję **Zapisz**.

4. Na **zaznacz źródłowej bazy danych** wybierz **Adventureworks2012** bazy danych do migracji.

   ![Wybierz źródło baz danych](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określ szczegóły docelowego

1.  Na **docelowe szczegóły** ekranu, określ szczegóły połączenia dla obiektu docelowego, który jest wstępnie przygotowany Azure wystąpienia bazy danych SQL zarządzanych do której **AdventureWorks2012** baza danych migracji.

    Azure SQL wystąpienie bazy danych zarządzanych nie jest już zainicjowane, wybierz opcję **nr** łącza udostępnić wystąpienia. Można nadal kontynuować tworzenie projektu, a następnie, gdy Azure SQL bazy danych zarządzanych wystąpienie będzie gotowe, wróć do tego określonego projektu do wykonania migracji.   
 
       ![Wybierz element docelowy](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Wybierz pozycję **Zapisz**.

3.  Na **podsumowanie projektu** ekranu, przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.
 
    ![Migrację podsumowania](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Wybierz pozycję **Zapisz**.   

## <a name="run-the-migration"></a>Uruchom migrację

1.  Wybierz ostatnio zapisany projekt, wybierz pozycję + **nowe działanie**, a następnie wybierz **uruchamiania migracji**.

    ![Tworzenie nowego działania](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Po wyświetleniu monitu wprowadź poświadczenia z serwerem źródłowym a docelowym, a następnie wybierz **zapisać**.

3.  Na **zaznacz źródłowej bazy danych** wybierz źródłowej bazy danych, które chcesz migrować.

    ![Wybierz źródło baz danych](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  Wybierz **zapisać**, a następnie na **skonfigurować ustawień migracji** ekranu, podaj następujące szczegóły:

    | | |
    |--------|---------|
    |**Udziału lokalizacji sieciowej** | Sieci lokalnej udostępnić, czy usługa migracji bazy danych Azure może potrwać źródło kopii zapasowych bazy danych. Konto usługi uruchomione wystąpienie programu SQL Server źródło musi mieć uprawnienia do zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresów IP serwera w udziale sieciowym, na przykład "\\\servername.domainname.com\backupfolder" lub "\\\IP address\backupfolder".|
    |**Nazwa użytkownika** | Nazwa użytkownika systemu windows Azure usługa migracji bazy danych może spersonifikować i przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure dla operacji przywracania. |
    |**Hasło** | Hasło dla użytkownika. |
    |**Ustawienia konta magazynu** | Identyfikator URI sygnatury dostępu Współdzielonego, który udostępnia usługi migracji bazy danych Azure dostęp do Twojego kontenera konta magazynu, do której usługa przekazywania plików kopii zapasowych i które jest używane w przypadku migracji baz danych do wystąpienia zarządzane bazy danych SQL Azure. [Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu Współdzielonego dla kontenera obiektów blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Skonfiguruj ustawienia migracji](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  Wybierz **zapisać**, a następnie na **podsumowanie migracji** ekranu w **Nazwa działania** tekst Określ nazwę działania migracji.

6. Rozwiń węzeł **opcję sprawdzania poprawności** sekcji, aby wyświetlić **wybierz opcję sprawdzania poprawności** ekranu, określ, czy sprawdzanie poprawności migrowanych bazy danych pod kątem poprawności zapytania, a następnie wybierz **Zapisz** .  

    ![Podsumowanie migracji](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. Wybierz **uruchamiania migracji**.

    Zostanie wyświetlone okno działania migracji, a stan działania jest **oczekujące**.

   ![Aktywności migracji oczekujące](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>Monitoruj migrację

1. Na ekranie działania migracji wybierz **Odśwież** Aby zaktualizować wyświetlane, aż zostanie wyświetlony stan migracji jest widoczny jako **Ukończono**.
 
   ![Działanie migracji zostało ukończone](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. Po ukończeniu migracji, zaznacz **Pobierz raport** uzyskać raport zawierający listę szczegółów skojarzonych z procesem migracji.
 
3. Sprawdź, czy docelowa baza danych w środowisku docelowym wystąpienia zarządzane bazy danych SQL Azure.

## <a name="next-steps"></a>Kolejne kroki

- Samouczek pokazuje, jak przeprowadzić migrację bazy danych do wystąpienia zarządzane przy użyciu polecenia Przywróć T-SQL, zobacz [przywrócenia kopii zapasowej do wystąpienia zarządzane za pomocą polecenia restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informacji o zarządzanych wystąpienie znajduje się w temacie [co to jest wystąpieniem zarządzane](../sql-database/sql-database-managed-instance.md).
- Informacji o podłączaniu aplikacje do wystąpienia zarządzane, zobacz [łączyć aplikacje](../sql-database/sql-database-managed-instance-connect-app.md).
