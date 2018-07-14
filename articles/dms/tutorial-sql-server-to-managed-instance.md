---
title: Usługa DMS umożliwia migrację do wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się przeprowadzić migrację z lokalnego programu SQL Server do wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/12/2018
ms.openlocfilehash: c911b096af6662e11afb4c4262b92c239d252c36
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990231"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrowanie programu SQL Server do bazy danych wystąpienia zarządzanego Azure SQL przy użyciu usługi DMS
Azure Database Migration Service można użyć do migracji baz danych z wystąpienia programu SQL Server w środowisku lokalnym, aby [wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance.md). Dodatkowe metody, które mogą wymagać pewnych ręcznej pracy, zobacz artykuł [migracja wystąpienia programu SQL Server do wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Projekty migracji z programu SQL Server do wystąpienia zarządzanego Azure SQL Database są w wersji zapoznawczej i podlegają [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym samouczku, wykonywana jest migracja **Adventureworks2012** bazy danych z lokalnego wystąpienia programu SQL Server do wystąpienia usługi Azure SQL Database zarządzane przy użyciu usługi Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji za pomocą usługi Azure Database Migration Service.
> * Uruchom migrację.
> * Monitoruj migrację.
> * Pobierz raport z migracji.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten samouczek, należy:

- Tworzenie sieci Wirtualnej dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Dowiedz się, topologie sieci na potrzeby migracji wystąpienia zarządzanego Azure SQL DB przy użyciu usługi Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Upewnij się, że Twojego Zrób reguły Azure (VNET) sieciowe grupy zabezpieczeń sieci wirtualnej blokuje komunikację następujące porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci Wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurowanie usługi [Windows zapory dla dostępu aparatu bazy danych źródła](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz zaporę Windows, aby umożliwić usłudze Azure Database Migration Service, uzyskiwanie dostępu do źródła programu SQL Server, czyli domyślnie TCP port 1433.
- Jeśli używasz wielu nazwanego wystąpienia programu SQL Server przy użyciu portów dynamicznych, możesz włączyć usługa przeglądarki SQL i zezwolić na dostęp do portu UDP 1434 za pośrednictwem zapory tak, aby usługi Azure Database Migration Service może nawiązać połączenia nazwanego wystąpienia w źródle serwer.
- Jeśli używasz urządzenia zapory przed źródłowe bazy danych, może być konieczne dodawanie reguły zapory zezwalające usługi Azure Database Migration Service dostęp źródłowych baz danych do migracji, a także plików za pośrednictwem portu 445 SMB do.
- Tworzenie wystąpienia usługi Azure SQL Database Managed wykonując szczegółowo w artykule [Tworzenie wystąpienia usługi Azure SQL Database zarządzane w witrynie Azure portal](https://aka.ms/sqldbmi).
- Upewnij się, że dane logowania używany do łączenia źródła programu SQL Server oraz zarządzane wystąpienia docelowego członkowie roli serwera sysadmin.
- Utwórz udział sieciowy, który umożliwia tworzenie kopii zapasowej źródłowej bazy danych Azure Database Migration Service.
- Upewnij się, że konto usługi uruchamiania źródła programu SQL Server wystąpienie ma uprawnienia do zapisu w udziale sieciowym, który został utworzony i czy konto komputera dla serwera źródłowego ma prawa do odczytu i zapisu do tego samego udziału.
- Należy zapamiętać Windows użytkownika (i hasło), który ma uprawnienia do pełnej kontroli w udziale sieciowym, która została wcześniej utworzona. Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure storage dla operacji przywracania.
- Utwórz kontener obiektów blob i pobrać jego identyfikatora URI sygnatury dostępu Współdzielonego wykonując kroki opisane w artykule [zasoby zarządzania usługi Azure Blob Storage za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), pamiętaj wybrać wszystkie uprawnienia (Odczyt, zapis, usuwanie, lista) w oknie zasad podczas Tworzenie identyfikatora URI sygnatury dostępu Współdzielonego. Te dane udostępnia usługi Azure Database Migration Service dostęp do kontenera konta magazynu do przekazywania plików kopii zapasowej, używany do migracji baz danych do wystąpienia zarządzanego Azure SQL Database

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Zarejestruj dostawcę zasobów Microsoft.DataMigration

1. Zaloguj się do witryny Azure portal, wybierz opcję **wszystkich usług**, a następnie wybierz pozycję **subskrypcje**.

    ![Pokaż portal subskrypcji](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Pokaż dostawców zasobów](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Wyszukiwanie do migracji, a następnie po prawej stronie **Microsoft.DataMigration**, wybierz opcję **zarejestrować**.

    ![Rejestrowanie dostawcy zasobów](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W witrynie Azure portal wybierz pozycję + **Utwórz zasób**, wyszukaj **Azure Database Migration Service**, a następnie wybierz pozycję **Azure Database Migration Service** z listy rozwijanej Lista.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Na **Azure Database Migration Service** ekranu, wybierz opcję **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Na **Utwórz usługę migracji** ekranu, określ nazwę usługi, subskrypcję i grupę nowy lub istniejący zasób.

4. Wybierz istniejącą sieć wirtualną (VNET) lub utworzyć nowe.
 
    Sieć wirtualna udostępnia usługi Azure Database Migration Service dostęp do źródła programu SQL Server i docelowego wystąpienia zarządzanego Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci Wirtualnej w witrynie Azure portal, zobacz artykuł [Utwórz sieć wirtualną przy użyciu witryny Azure portal](https://aka.ms/DMSVnet).

    Aby uzyskać dodatkowe szczegóły, zobacz artykuł [topologii na potrzeby migracji wystąpienia zarządzanego Azure SQL DB przy użyciu usługi Azure Database Migration Service sieci](https://aka.ms/dmsnetworkformi).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).
   
    ![Utwórz usługę DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Wybierz **Utwórz** do tworzenia usługi.

## <a name="create-a-migration-project"></a>Utwórz projekt migracji

Po utworzeniu usługi wyszukanie w witrynie Azure portal, otwórz go, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure portal wybierz **wszystkich usług**, wyszukaj Azure Database Migration Service, a następnie wybierz **usługi Azure Database Migration Service**.

    ![Znajdź wszystkie wystąpienia usługi Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Na **ekranu Azure Database Migration Service**, wyszukaj nazwę wystąpienia utworzone, a następnie wybierz wystąpienie usługi.
 
3. Wybierz pozycję + **nowy projekt migracji**.

4. Na **nowy projekt migracji** ekranu, określ nazwę dla projektu, w **typ serwera źródła** pola tekstowego, wybierz opcję **programu SQL Server**, a następnie w polu **docelowej Typ serwera** pola tekstowego, wybierz opcję **wystąpienia zarządzanego Azure SQL Database**.

   ![Tworzenie projektu usługi DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Wybierz **Utwórz** do tworzenia projektu.

## <a name="specify-source-details"></a>Określ szczegóły źródła

1. Na **źródła szczegóły** ekranu, określ szczegóły połączenia dla źródła programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, wybierz opcję **certyfikat serwera zaufania** pole wyboru.

    Jeśli zaufany certyfikat nie został zainstalowany, programu SQL Server generuje certyfikat z podpisem własnym, po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń dla połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewnia silne zabezpieczenia. Są one podatne na ataki typu man-in--middle. Nie należy polegać na protokołu SSL przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Wybierz pozycję **Zapisz**.

4. Na **Wybierz źródłowe bazy danych** ekranu, wybierz **Adventureworks2012** bazy danych do migracji.

   ![Wybierz źródłowe bazy danych](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określ szczegóły elementu docelowego

1.  Na **docelowe szczegóły** ekranu, określ szczegóły połączenia dla elementu docelowego, który jest wstępna aprowizacja bazy danych wystąpienia zarządzanego Azure SQL do której **AdventureWorks2012** bazy danych ma być migracji.

    Jeśli nie aprowizowano już Azure SQL Database Managed Instance, wybierz opcję **nie** łącza ułatwiające aprowizowanie wystąpienia. Można nadal kontynuować tworzenie projektu i gdy Azure SQL Database Managed Instance jest gotowy, wróć do tego określonego projektu, aby wykonać migrację.   
 
       ![Wybierz element docelowy](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Wybierz pozycję **Zapisz**.

3.  Na **podsumowanie projektu** ekranu, przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.
 
    ![Podsumowanie projektu migracji](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Wybierz pozycję **Zapisz**.   

## <a name="run-the-migration"></a>Uruchom migrację

1.  Wybierz ostatnio zapisany projekt, wybierz pozycję + **nowe działanie**, a następnie wybierz pozycję **Uruchamianie migracji**.

    ![Tworzenie nowego działania](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Po wyświetleniu monitu wprowadź poświadczenia dla źródła i serwery docelowe, a następnie wybierz **Zapisz**.

3.  Na **Wybierz źródłowe bazy danych** ekranu, wybierz źródłowych baz danych, które mają zostać zmigrowane.

    ![Wybierz źródłowe bazy danych](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases2.png)

4.  Wybierz **Zapisz**, a następnie na **wybierz identyfikatory logowania** ekranu, wybierz identyfikatory logowania, które mają zostać zmigrowane.

    Bieżąca wersja obsługuje tylko migrowania nazw logowania SQL.

    ![Wybierz identyfikatory logowania](media\tutorial-sql-server-to-managed-instance\dms-select-logins.png)

5. Wybierz **Zapisz**, a następnie na **Konfiguruj ustawienia migracji** ekranu, podaj następujące informacje:

    | | |
    |--------|---------|
    |**Udział lokalizacji sieciowej** | Sieci lokalnej udostępniać, że Azure Database Migration Service może potrwać źródła kopii zapasowych bazy danych. Konto usługi, uruchamiające źródłowe wystąpienie programu SQL Server musi mieć uprawnienia do zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresów IP serwera w udziale sieciowym, na przykład "\\\servername.domainname.com\backupfolder" lub "\\\IP address\backupfolder".|
    |**Nazwa użytkownika** | Nazwa użytkownika systemu windows, Azure Database Migration Service mogą personifikować i przekazania plików kopii zapasowej do kontenera usługi Azure storage dla operacji przywracania. |
    |**Hasło** | Hasło dla użytkownika. |
    |**Ustawienia konta magazynu** | Identyfikator URI sygnatury dostępu Współdzielonego, który udostępnia Azure Database Migration Service dostęp do usługi kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej i że jest używana do migracji baz danych do wystąpienia zarządzanego Azure SQL Database. [Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu Współdzielonego dla kontenera obiektów blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Konfiguruj ustawienia migracji](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings2.png)

5.  Wybierz **Zapisz**, a następnie na **podsumowanie migracji** ekranu w **Nazwa działania** tekstu należy określić nazwę działania migracji.

    ![Podsumowanie migracji](media\tutorial-sql-server-to-managed-instance\dms-migration-summary2.png)

6. Rozwiń **opcję sprawdzania poprawności** sekcji, aby wyświetlić **wybierz opcję weryfikacji** ekranu, określ, czy sprawdzanie poprawności zmigrowana baza danych pod kątem poprawności zapytania, a następnie wybierz **Zapisz** .  

7. Wybierz **Uruchamianie migracji**.

    Zostanie wyświetlone okno działania migracji, a stan działania to **oczekujące**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybierz **Odśwież** Aby zaktualizować wyświetlane dane.
 
   ![Działania migracji w toku](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-in-progress.png)

2. Można rozwinąć kategorie baz danych i nazwami logowania, aby monitorować stan migracji obiektów odpowiedniego serwera.

   ![Działania migracji w toku](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-monitor.png)

3. Po zakończeniu migracji, zaznacz **Pobierz raport** można pobrać raport zawierający listę szczegóły skojarzonego z procesem migracji.
 
4. Upewnij się, że docelowa baza danych w środowisku docelowym wystąpienia zarządzanego Azure SQL Database.

## <a name="next-steps"></a>Kolejne kroki

- Samouczek omawiający Migrowanie bazy danych do wystąpienia zarządzanego przy użyciu polecenia T-SQL RESTORE, zobacz [przywracanie kopii zapasowej do wystąpienia zarządzanego przy użyciu polecenia restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Aby uzyskać informacje o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane](../sql-database/sql-database-managed-instance.md).
- Aby uzyskać informacji na temat łączenia aplikacji do wystąpienia zarządzanego, zobacz [łączyć aplikacje](../sql-database/sql-database-managed-instance-connect-app.md).
