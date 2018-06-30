---
title: Utwórz kopię zapasową bazy danych programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: W tym samouczku opisano kopii zapasowej serwera SQL Azure. Wyjaśniono także, odzyskiwanie programu SQL Server.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/29/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 89a1df607c220e5dc12bc6263955d6e445e529bd
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116128"
---
# <a name="back-up-sql-server-database-in-azure"></a>Wykonaj kopię zapasową bazy danych programu SQL Server na platformie Azure

Bazy danych programu SQL Server są krytycznych obciążeń wymagających cel niski punktu odzyskiwania (RPO) i długoterminowego przechowywania. Kopia zapasowa Azure zapewnia rozwiązanie SQL Serverbackup, które wymaga infrastruktury zero, co oznacza nie złożonych, Utwórz kopię zapasową serwera, nie agenta zarządzania lub magazynu kopii zapasowej do zarządzania. Kopia zapasowa Azure udostępnia funkcje scentralizowanego zarządzania dla kopii zapasowych na wszystkich serwerach SQL lub nawet różnych obciążeń.

 W tym artykule dowiesz się:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące kopii zapasowych programu SQL Server na platformie Azure
> * Tworzenie i używanie magazynu usług odzyskiwania
> * Skonfiguruj kopie zapasowe bazy danych programu SQL Server
> * Ustawienie zasad tworzenia kopii zapasowej (lub przechowywania) dla punktów odzyskiwania
> * Przywracanie bazy danych

Przed rozpoczęciem procedury przedstawione w tym artykule, należy mieć programu SQL Server działające na platformie Azure. [Maszyny wirtualne marketplace SQL umożliwia szybko utworzyć serwer SQL](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Następujące elementy są znane ograniczenia dla publicznej wersji zapoznawczej.

- Maszyny wirtualnej SQL wymaga łączności z Internetem na dostęp do usługi Azure publicznych adresów IP. Aby uzyskać więcej szczegółowych informacji, zobacz sekcję [ustanowienia połączenia z siecią](backup-azure-sql-database.md#establish-network-connectivity).
- Można chronić maksymalnie 2000 baz danych w jednym magazynie usług odzyskiwania. Dodatkowe baz danych powinny być przechowywane w oddzielnych magazyn usług odzyskiwania.
- [Kopia zapasowa grup dostępności rozproszonej ma ograniczenia](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- Wystąpienia klastra trybu Failover (FCI) SQL nie są obsługiwane.
- Użyj portalu Azure, aby skonfigurować Azure Backup w celu ochrony baz danych programu SQL Server. Obsługa programu Azure PowerShell, interfejsu wiersza polecenia i interfejsów API REST nie jest obecnie dostępna.

## <a name="supported-azure-geos"></a>Obsługiwane platformy Azure obszarach geograficznych.

- Australia Południowo-Wschodnia (ASE) 
- Brazylia Południowa (BRS)
- Kanada Środkowa (CNC)
- Kanada Wschodnia (CE)
- Środkowe stany USA (CUS)
- Azja Wschodnia (EA)
- Australia Wschodnia (AE) 
- Wschodnie stany USA (EUS)
- Wschodnie stany USA 2 (EUS2)
- Japonia Wschodnia (JPE)
- Japonia Zachodnia (JPW)
- Indie Środkowe (INC) 
- Indie Południowe (INS)
- Korea Środkowa (KRC)
- Korea Południowa (KRS)
- Północno-środkowe stany USA (NCUS) 
- Europa Północna (NE) 
- Południowo-środkowe stany USA (SCUS) 
- Azja Południowo-Wschodnia (SEA)
- Południowe Zjednoczone Królestwo (UKS) 
- Zachodnie Zjednoczone Królestwo (UKW) 
- Europa Zachodnia (WE) 
- Zachodnie stany USA (WUS)
- Zachodnio-środkowe stany USA (WCUS)
- Zachodnie stany USA 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Obsługiwane systemy operacyjne i wersje programu SQL server

Następujące systemy operacyjne obsługiwane wersje programu SQL Server, których dotyczą SQL marketplace, maszyny wirtualne platformy Azure i spoza witryny marketplace maszyny wirtualne (SQL Server ręcznie zainstalowanym).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux nie jest obecnie obsługiwane.

### <a name="supported-versionseditions-of-sql-server"></a>Obsługiwane wersje/wersje programu SQL Server

- SQL 2012 Enterprise, Standard, sieci Web i deweloperów, Express
- SQL 2014 Enterprise, Standard, sieci Web i deweloperów, Express
- Express programu SQL 2016 Enterprise, Standard, sieci Web i deweloperów,
- SQL 2017 Enterprise, Standard, sieci Web i deweloperów, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Wymagania wstępne dotyczące ochrony programu SQL Server za pomocą usługi Kopia zapasowa Azure 

Przed utworzeniem kopii zapasowej bazy danych programu SQL Server, sprawdź następujące warunki. :

- Zidentyfikuj lub [Tworzenie magazynu usług odzyskiwania](backup-azure-sql-database.md#create-a-recovery-services-vault) w tym samym regionie, lub ustawień regionalnych, jako maszynę wirtualną hostowany program SQL Server.
- [Sprawdź uprawnienia na maszynie wirtualnej](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) potrzebne do tworzenia kopii zapasowych baz danych SQL.
- [Maszyny wirtualnej SQL ma łączność sieciową](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Może mieć tylko jedno rozwiązanie tworzenia kopii zapasowej w czasie do kopii zapasowej baz danych programu SQL Server. Przed użyciem tej funkcji, wyłącz wszelkie inne kopii zapasowej SQL, innego kopie zapasowe będą kolidować i zakończyć się niepowodzeniem. Można włączyć usługi Kopia zapasowa Azure dla maszyn wirtualnych IaaS wraz z kopii zapasowej SQL bez żadnych konfliktów 
>

Jeśli te warunki w danym środowisku, przejdź do sekcji [konfigurowanie Twojego magazynu, aby chronić bazę danych SQL](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Jeśli wymagania wstępne nie istnieją, nadal odczytu w tej sekcji.


## <a name="establish-network-connectivity"></a>Ustanowienie połączenia z siecią

Dla wszystkich operacji maszyny wirtualnej SQL musi mieć łączność Azure publicznych adresów IP. Niepowodzenie operacji maszyny wirtualnej SQL (takich jak bazy danych odnajdywania, konfigurowanie tworzenia kopii zapasowej, zaplanowane tworzenie kopii zapasowych, przywracania punktów odzyskiwania i tak dalej) bez połączenia z publicznych adresów IP. Aby Wyczyść ścieżkę dla kopii zapasowej ruchu użyj jednej z następujących opcji:

- Lista dozwolonych adresów IP centrum danych Azure zakresów — listą dozwolonych adresów IP platformy Azure zakresy IP centrum danych, użyj [strony Centrum szczegółowe informacje na temat zakresów adresów IP i instrukcje dotyczące pobierania](https://www.microsoft.com/download/details.aspx?id=41653). 
- Wdrażanie serwera proxy HTTP dla routingu ruchu — podczas tworzenia kopii zapasowej bazy danych SQL na maszynie wirtualnej, kopii zapasowej rozszerzenia na maszynie Wirtualnej używa interfejsów API protokołu HTTPS należy wysyłać polecenia zarządzania do usługi Kopia zapasowa Azure i danych do usługi Azure Storage. Zapasowy numer wewnętrzny również używa usługi Azure Active Directory (AAD) do uwierzytelniania. Kierować ruchem zapasowy numer wewnętrzny, te trzy usługi za pośrednictwem serwera proxy HTTP, ponieważ jest on tylko składnik skonfigurowany do uzyskiwania dostępu do publicznej sieci internet.

Są kompromisy między wyborów: możliwości zarządzania, kontrolę i kosztów.

> [!NOTE]
>Tagi usługi dla usługi Kopia zapasowa Azure powinny być dostępne przez ogólnej dostępności.
>

| Opcja | Zalety | Wady |
| ------ | ---------- | ------------- |
| Lista dozwolonych adresów IP, zakresów | Brak dodatkowych kosztów. <br/> Do otwierania dostępu w grupy NSG, użyj **AzureNetworkSecurityRule zestaw** polecenia cmdlet. | Złożone, aby zarządzać jako dotkniętych zakresów IP ulec zmianie. <br/>Zapewnia dostęp do całego systemu Azure, Magazyn nie tylko.|
| Użyj serwera proxy HTTP   | Kontrolę na serwerze proxy za pośrednictwem magazynu dozwolone adresy URL. <br/>Pojedynczy punkt internet dostęp do maszyn wirtualnych. <br/> Nie może ulec zmiany adresu IP platformy Azure. | Dodatkowe koszty uruchomioną maszynę Wirtualną z oprogramowaniem serwera proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ustawianie uprawnień dla maszyn wirtualnych SQL spoza witryny marketplace

Aby utworzyć kopię zapasową maszyny wirtualnej, wymaga kopia zapasowa Azure **AzureBackupWindowsWorkload** można zainstalować rozszerzenia. Jeśli używasz portalu Azure marketplace maszyn wirtualnych, przejdź do [baz danych serwera SQL odnajdywanie](backup-azure-sql-database.md#discover-sql-server-databases). Jeśli nie utworzono maszynę wirtualną hostujące bazy danych SQL z poziomu portalu Azure marketplace, wykonaj następującą sekcją, aby zainstalować rozszerzenie i ustawić odpowiednie uprawnienia. Oprócz **AzureBackupWindowsWorkload** rozszerzenie, kopia zapasowa Azure wymaga uprawnień administratora systemu SQL do ochrony baz danych. Podczas odnajdowania baz danych na maszynie wirtualnej, kopia zapasowa Azure tworzy konto NT Service\AzureWLBackupPluginSvc. Dla usługi Kopia zapasowa Azure do odnajdywania baz danych konta NT Service\AzureWLBackupPluginSvc musi mieć logowania SQL oraz uprawnienia administratora systemu SQL. W poniższej procedurze wyjaśniono, jak zapewnić te uprawnienia.

Aby skonfigurować uprawnienia:

1. W [portalu Azure](https://portal.azure.com), Otwórz magazyn usług odzyskiwania używane do ochrony baz danych.
2. W menu nawigacyjnym magazynu, kliknij przycisk **+ kopia zapasowa** otworzyć **cel kopii zapasowej** menu.

   ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cel kopii zapasowej** menu w **gdzie działa Twoje obciążenie?** menu, pozostaw **Azure** jako domyślny.

4. Na **co chcesz utworzyć kopię zapasową** menu, rozwiń menu rozwijane i wybierz **programu SQL Server w maszynie Wirtualnej platformy Azure**.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cel kopii zapasowej** menu zostaną wyświetlone dwa nowe kroki: **odnajdywanie baz danych na maszynach wirtualnych** i **Konfigurowanie usługi Kopia zapasowa**. **Odnajdywanie baz danych na maszynach wirtualnych** uruchamia wyszukiwanie maszyn wirtualnych platformy Azure.

    ![Pokazuje nowe kroki tworzenia kopii zapasowej w celu](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Kliknij przycisk **Uruchom odnajdowanie** do wyszukiwania niechronionych maszyn wirtualnych w ramach subskrypcji. W zależności od liczby niechronionych maszyn wirtualnych w ramach subskrypcji może upłynąć trochę czasu, aby przejść przez wszystkie maszyny wirtualne.

    ![Oczekiwanie na kopię zapasową](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po niechronione maszyny wirtualnej zostanie odnaleziony, zostanie wyświetlony na liście. Niechronionych maszyn wirtualnych są wyświetlane według ich maszyny wirtualnej nazwy i grupie zasobów. Istnieje możliwość dla wielu maszyn wirtualnych do tej samej nazwie. Jednak maszyn wirtualnych o tej samej nazwie należą do różnych grup zasobów. Jeśli na liście nie ma oczekiwanego maszyny wirtualnej, zobacz, jeśli maszyna wirtualna jest już chroniona do magazynu.

6. Z listy maszyn wirtualnych, wybierz maszynę Wirtualną, zawierający bazy danych SQL, aby utworzyć kopię zapasową, a następnie kliknij przycisk **odnajdywanie bazami danych**. 

    Proces odnajdywania instaluje **AzureBackupWindowsWorkload** rozszerzenia na maszynie wirtualnej. Rozszerzenie udostępnia usługi Kopia zapasowa Azure do komunikowania się z maszyny wirtualnej, aby go utworzyć kopię zapasową bazy danych SQL. Raz instaluje rozszerzenia kopia zapasowa Azure tworzy konto usług wirtualnych systemu Windows, **NT Service\AzureWLBackupPluginSvc**, na maszynie wirtualnej. Konto usług wirtualnych wymaga uprawnień administratora systemu SQL. Podczas usług wirtualnych konto procesu instalacji, jeśli zostanie wyświetlony błąd **UserErrorSQLNoSysadminMembership**, zobacz sekcję [uprawnienia administratora systemu SQL ustalania](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    Obszar powiadomień będzie wyświetlany postęp odnajdywania bazy danych. W zależności od tego, jak wiele baz danych znajdują się na maszynie wirtualnej może upłynąć trochę czasu na ukończenie zadania. Po odnalezieniu wybranych baz danych, zostanie wyświetlony komunikat z potwierdzeniem.

    ![pomyślne wdrożenie powiadomienie](./media/backup-azure-sql-database/notifications-db-discovered.png)

Po skojarzeniu bazy danych w magazynie usług odzyskiwania, następnym krokiem jest [Konfigurowanie kopii zapasowej](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Ustalenie uprawnień administratora systemu SQL

Podczas procesu instalacji, jeśli zostanie wyświetlony błąd **UserErrorSQLNoSysadminMembership**, zaloguj się do programu SQL Server Management Studio (SSMS) przy użyciu konta z uprawnieniami administratora systemu SQL. O ile nie wymagają szczególnych uprawnień, należy używać uwierzytelniania systemu Windows do rozpoznania konta.

1. Na serwerze SQL, otwórz **zabezpieczeń/logowania** folderu.

    ![Otwieranie programu SQL Server i zabezpieczeń i logowania folderów Aby wyświetlić konta](./media/backup-azure-sql-database/security-login-list.png)

2. Do logowania do folderu, kliknij prawym przyciskiem myszy i wybierz **nowe dane logowania**, a podczas logowania — okno dialogowe nowego, kliknij przycisk **wyszukiwania**

    ![Otwórz okno dialogowe Wyszukiwanie w logowania — nowy](./media/backup-azure-sql-database/new-login-search.png)

3. Ponieważ konta usług wirtualnych systemu Windows **NT Service\AzureWLBackupPluginSvc** został już utworzony podczas rejestracji maszyny wirtualnej i faza odnajdywania SQL, wprowadź nazwę konta, pojawiającą się w  **Wprowadź nazwę obiektu do wybrania** okna dialogowego. Kliknij przycisk **Sprawdź nazwy** do rozpoznania nazwy. 

    ![Kliknij przycisk Sprawdź nazwy do rozpoznania nazwy usługi nieznany](./media/backup-azure-sql-database/check-name.png)

4. Kliknij przycisk **OK** aby zamknąć okno dialogowe Wybieranie użytkownika lub grupy.

5. W **ról serwera** okna dialogowego, upewnij się, że **sysadmin** wybrać rolę. Następnie kliknij przycisk **OK** zamknąć **nowe dane logowania -**.

    ![Upewnij się, że wybrano roli serwera sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Teraz powinny istnieć wymagane uprawnienia.

6. Chociaż w stałej błąd uprawnień, nadal musisz skojarzyć bazy danych z magazynu usług odzyskiwania. W portalu Azure **chronione serwery** listy, kliknij prawym przyciskiem myszy serwer w błąd, a następnie wybierz **ponowne odnajdywanie bazami danych**.

    ![Sprawdź, czy serwer ma odpowiednie uprawnienia](./media/backup-azure-sql-database/check-erroneous-server.png)

    Obszar powiadomień będzie wyświetlany postęp odnajdywania bazy danych. W zależności od tego, jak wiele baz danych znajdują się na maszynie wirtualnej może upłynąć trochę czasu na ukończenie zadania. W przypadku odnalezienia wybranych baz danych w obszarze powiadomień zostanie wyświetlony komunikat z potwierdzeniem.

    ![pomyślne wdrożenie powiadomienie](./media/backup-azure-sql-database/notifications-db-discovered.png)

Po skojarzeniu bazy danych w magazynie usług odzyskiwania, następnym krokiem jest [Konfigurowanie kopii zapasowej](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdywanie baz danych programu SQL Server

Kopia zapasowa Azure umożliwia odnalezienie wszystkich baz danych w wystąpieniu programu SQL Server, można je chronić zgodnie z wymaganiami kopii zapasowej. Użyj poniższej procedury do identyfikowania maszyny wirtualne obsługujące bazy danych SQL. Po zidentyfikowaniu maszyny wirtualnej, kopia zapasowa Azure instaluje lekkie rozszerzenia, aby odnaleźć baz danych programu SQL server.

1. Zaloguj się do subskrypcji w [portalu Azure](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **wszystkie usługi**.

    ![Wybierz opcję wszystkie usługi, w menu głównym](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. W oknie dialogowym z wszystkich usług, wpisz *usług odzyskiwania*. Po rozpoczęciu wpisywania, dane wejściowe filtruje listę zasobów. Gdy zostanie wyświetlony, wybierz **Magazyny usług odzyskiwania**.

    ![Typ usługi odzyskiwania w oknie dialogowym wszystkie usługi](./media/backup-azure-sql-database/all-services.png) <br/>

    Zostanie wyświetlona lista magazynów usług odzyskiwania w ramach subskrypcji. 

4. Z listy magazyn usług odzyskiwania wybierz magazyn, którego chcesz użyć do ochrony baz danych SQL.

5. W menu nawigacyjnym magazynu, kliknij przycisk **+ kopia zapasowa** otworzyć **cel kopii zapasowej** menu.

   ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

6. Na **cel kopii zapasowej** menu w **gdzie działa Twoje obciążenie?** menu, pozostaw **Azure** jako domyślny.

7. Na **co chcesz utworzyć kopię zapasową** menu, rozwiń menu rozwijane i wybierz **programu SQL Server w maszynie Wirtualnej platformy Azure**.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Po wybraniu **cel kopii zapasowej** rozwijalna należy wykonać dwie czynności: Odnajdywanie baz danych w maszynach wirtualnych i konfigurowanie usługi Kopia zapasowa. 

    ![Pokazuje nowe kroki tworzenia kopii zapasowej w celu](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Kliknij przycisk **Uruchom odnajdowanie** do wyszukiwania niechronionych maszyn wirtualnych w ramach subskrypcji. W zależności od liczby niechronionych maszyn wirtualnych w ramach subskrypcji może upłynąć trochę czasu, aby przejść przez wszystkie maszyny wirtualne.

    ![Oczekiwanie na kopię zapasową](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po niechronione maszyny wirtualnej zostanie odnaleziony, zostanie wyświetlony na liście. Wiele maszyn wirtualnych może mieć takiej samej nazwie. Jednak wiele maszyn wirtualnych o tej samej nazwie należą do różnych grup zasobów. Niechronione maszyny wirtualne są wyświetlane według ich maszyny wirtualnej nazwy i grupie zasobów. Jeśli nie ma oczekiwanego maszyny wirtualnej, zobacz, jeśli maszyny wirtualnej jest już chroniona do magazynu.

9. Z listy maszyn wirtualnych, zaznacz pole wyboru maszyny wirtualnej, który zawiera bazy danych SQL, które chcesz chronić, a następnie kliknij przycisk **odnajdywanie bazami danych**.

    Kopia zapasowa Azure umożliwia odnalezienie wszystkich baz danych na maszynie wirtualnej. Aby dowiedzieć się, jak wykonywanego w fazie odnajdywania bazy danych, zobacz następującą sekcję [operacji wewnętrznej bazy danych podczas rozpoznawania baz danych SQL](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Po odnalezieniu bazy danych SQL, można przystąpić do [skonfigurować zadanie tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Operacje wewnętrznej bazy danych podczas odnajdywania bazy danych SQL

Jeśli używasz **odnajdywanie bazami danych** narzędzia Kopia zapasowa Azure wykonuje następujące operacje w tle:

- rejestruje maszynę wirtualną z magazynu usług odzyskiwania do utworzenia kopii zapasowej obciążeń. Wszystkie bazy danych na maszynie wirtualnej w zarejestrowany tylko utworzeniem kopii zapasowej tego magazynu usług odzyskiwania. 

- Instaluje **AzureBackupWindowsWorkload** rozszerzenia na maszynie wirtualnej. Tworzenie kopii zapasowej bazy danych SQL jest rozwiązaniem bez wykorzystania agentów, czyli z rozszerzeniem zainstalowany na maszynie wirtualnej, agent nie jest zainstalowany w bazie danych SQL.

- tworzy konto usługi **NT Service\AzureWLBackupPluginSvc**, na maszynie wirtualnej. Wszystkie operacje tworzenia kopii zapasowej i przywracania Użyj konta usługi. **NT Service\AzureWLBackupPluginSvc** wymaga uprawnień administratora systemu SQL. Wszystkie maszyny wirtualne SQL Marketplace dostarczane z SqlIaaSExtension zainstalowane i AzureBackupWindowsWorkload używa SQLIaaSExtension na automatyczne uzyskiwanie wymaganych uprawnień. Jeśli maszyna wirtualna nie ma zainstalowanych SqlIaaSExtension, odnajdywanie bazy danych kończy się niepowodzeniem i otrzymasz komunikat o błędzie **UserErrorSQLNoSysAdminMembership**. Aby dodać uprawnienia administratora systemu do utworzenia kopii zapasowej, postępuj zgodnie z instrukcjami [Ustawianie uprawnień kopia zapasowa Azure dla maszyn wirtualnych SQL spoza witryny marketplace](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Wybierz maszynę wirtualną i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Konfigurowanie kopii zapasowej bazy danych SQL Server

Kopia zapasowa Azure udostępnia usługi zarządzania, aby chronić baz danych programu SQL Server i zarządzać nimi zadania tworzenia kopii zapasowej. Zarządzanie i możliwości monitorowania są zależne od magazynu usług odzyskiwania. 

> [!NOTE]
> Może mieć tylko jedno rozwiązanie tworzenia kopii zapasowej w czasie do kopii zapasowej baz danych programu SQL Server. Przed użyciem tej funkcji, wyłącz wszelkie inne kopii zapasowej SQL, innego kopie zapasowe będą kolidować i zakończyć się niepowodzeniem. Można włączyć usługi Kopia zapasowa Azure dla maszyn wirtualnych IaaS wraz z kopii zapasowej SQL bez żadnych konfliktów 
>

Aby skonfigurować ochronę bazy danych SQL:

1. Otwórz magazyn usług odzyskiwania zarejestrowany z maszyną wirtualną programu SQL.

2. W menu nawigacyjnym magazynu, kliknij przycisk **+ kopia zapasowa** otworzyć **cel kopii zapasowej** menu.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cel kopii zapasowej** menu w **gdzie działa Twoje obciążenie?** menu, pozostaw **Azure** jako domyślny.

4. Na **co chcesz utworzyć kopię zapasową** menu, rozwiń menu rozwijane i wybierz **programu SQL Server w maszynie Wirtualnej platformy Azure**.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Po wybraniu **cel kopii zapasowej** rozwijalna należy wykonać dwie czynności: Odnajdywanie baz danych w maszynach wirtualnych i konfigurowanie usługi Kopia zapasowa. Jeśli już przeszli w tym artykule w kolejności, już znasz niechronionych maszyn wirtualnych, a ten magazyn został zarejestrowany z maszyną wirtualną. Teraz możesz skonfigurować ochrony dla baz danych SQL.

5. W menu Cel kopii zapasowej, kliknij przycisk **Konfigurowanie usługi Kopia zapasowa**.

    ![Pokazuje nowe kroki tworzenia kopii zapasowej w celu](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Usługa Kopia zapasowa Azure Wyświetla wszystkie wystąpienia programu SQL z autonomicznej bazy danych, a także grupy dostępności funkcji SQL AlwaysOn. Aby wyświetlić autonomicznej bazy danych w wystąpieniu programu SQL, kliknij ikonę strzałki obok nazwy wystąpienia, aby wyświetlić baz danych. Na poniższych ilustracjach przedstawiono przykłady wystąpienia autonomicznego i zawsze włączonej grupy dostępności.

    > [!NOTE]
    > Pełne i różnicowe kopie zapasowe są wykonywane z węzła podstawowego jako platformy SQL ma ograniczenie. Tworzenie kopii zapasowej dziennika może się zdarzyć zależności od swoich preferencji tworzenia kopii zapasowej. Z powodu tego ograniczenia należy zarejestrować węzła podstawowego.
    >

    ![Lista baz danych w wystąpieniu programu SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Kliknij ikonę strzałki obok zawsze włączonych grup dostępności, aby wyświetlić listę baz danych.

    ![Lista baz danych w grupie dostępności funkcji AlwaysOn](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Wybierz z listy baz danych, wszystkie punkty, które chcesz chronić, a następnie kliknij przycisk **OK**.

    ![Wybierz wiele baz danych, aby je chronić](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Można wybrać maksymalnie 50 bazy danych w tym samym czasie. Jeśli chcesz chronić więcej niż 50 bazy danych, upewnij się wiele przebiegów. Po włączeniu ochrony najpierw 50 baz danych, powtórz ten krok, aby chronić dalej zestaw baz danych.
    > [!Note] 
    > Aby zoptymalizować obciążeń kopii zapasowych, kopia zapasowa Azure dzieli dużych zadań tworzenia kopii zapasowej na wielu instancji. Maksymalna liczba baz danych na jedno zadanie tworzenia kopii zapasowej jest 50.
    >
    >

7. Utwórz lub wybierz zasady tworzenia kopii zapasowej, w **kopii zapasowej** menu, wybierz opcję **kopii zapasowej zasad**, aby otworzyć menu.

    ![Wybierz opcję zasad tworzenia kopii zapasowej](./media/backup-azure-sql-database/select-backup-policy.png)

8. Z **wybierz zasady tworzenia kopii zapasowej** menu rozwijanego wybierz zasady tworzenia kopii zapasowej, a następnie kliknij przycisk **OK**. Aby uzyskać informacje na temat tworzenia własnych zasad tworzenia kopii zapasowej, zobacz sekcję [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-sql-database.md#define-a-backup-policy).

    ![Wybierz z menu rozwijanego zasad tworzenia kopii zapasowej](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    W menu zasad tworzenia kopii zapasowej z **wybierz zasady tworzenia kopii zapasowej** menu rozwijanego możesz: 
    - Domyślna zasada HourlyLogBackup 
    - istniejące zasady tworzenia kopii zapasowej wcześniej utworzony dla bazy danych SQL,
    - Aby [zdefiniować nową zasadę](backup-azure-sql-database.md#define-a-backup-policy) oparte na cel punktu odzyskiwania (RPO) i zakresu przechowywania. 

    > [!Note]
    > Kopia zapasowa Azure obsługuje przechowywania długoterminowego w oparciu o schemacie tworzenia kopii zapasowej dziadek ojciec syn. w celu zoptymalizowania użycia magazynu wewnętrznej bazy danych, spełniając wymagania dotyczące zgodności.
    >

9. Po wybraniu opcji zasady tworzenia kopii zapasowej, w **menu kopii zapasowej** kliknij **kopii zapasowej Włącz**.

    ![Włącz wybrane zasady tworzenia kopii zapasowej](./media/backup-azure-sql-database/enable-backup-button.png)

    Możesz śledzić postępy konfiguracji w obszarze powiadomień w portalu.

    ![Wyświetl obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Definiowanie zasad tworzenia kopii zapasowych

Zasady tworzenia kopii zapasowych określają macierz z informacjami przy tworzeniu kopii zapasowych woluminów i jak długo są przechowywane kopie zapasowe. Kopia zapasowa Azure umożliwia planowanie trzy typy kopii zapasowych baz danych SQL:

* Pełna kopia zapasowa - pełną kopię zapasową bazy kopię zapasową całej bazy danych. Pełna kopia zapasowa zawiera wszystkie dane w określonej bazy danych lub zestaw grup plików lub pliki, a za mało dziennika, aby odzyskać dane. Możesz wyzwolić co najwyżej jedną pełną kopię zapasową dziennie. Istnieje możliwość utworzenia pełnej kopii zapasowej na interwał codziennie lub co tydzień. 
* Różnicowej kopii zapasowej - różnicowej kopii zapasowej jest oparte na najnowszych, poprzedniego pełnych danych kopii zapasowej. Różnicowej kopii zapasowej przechwytuje dane o zmianie po wykonaniu pełnej kopii zapasowej. Możesz wyzwolić co najwyżej jeden różnicowej kopii zapasowej na dzień. Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej tego samego dnia.
* Tworzenie kopii zapasowej dziennika transakcji - kopię zapasową dziennika umożliwia w momencie przywracania do określonego drugi. Maksymalnie można skonfigurować kopie zapasowe dziennika transakcyjne co 15 minut.

Utworzono zasadę w magazynie usług odzyskiwania poziomu. Jeśli masz wiele magazynów magazynów można używać tych samych zasad tworzenia kopii zapasowej, ale należy zastosować zasady tworzenia kopii zapasowych do każdego magazynu. Podczas tworzenia zasad tworzenia kopii zapasowej codziennie, pełnej kopii zapasowej jest ustawieniem domyślnym. Możesz dodać różnicowej kopii zapasowej, ale tylko wtedy, jeśli przełącznik tworzenie pełnej kopii zapasowej co tydzień. W poniższej procedurze wyjaśniono, jak utworzyć zasady tworzenia kopii zapasowych programu SQL Server w maszynie wirtualnej platformy Azure.

Aby utworzyć zasady tworzenia kopii zapasowej

1. W menu zasad tworzenia kopii zapasowej z **wybierz zasady tworzenia kopii zapasowej** menu rozwijanego wybierz **Utwórz nowy**.

   ![Utwórz nowe zasady tworzenia kopii zapasowej](./media/backup-azure-sql-database/create-new-backup-policy.png)

    Menu zasad tworzenia kopii zapasowej zmienia zapewnienie pól wymaganych przez wszystkie nowe zasady tworzenia kopii zapasowej serwera SQL.

   ![nowe zasady tworzenia kopii zapasowej pola](./media/backup-azure-sql-database/blank-new-policy.png)

2. W **Nazwa zasady**, podaj nazwę. 

3. Pełna kopia zapasowa jest obowiązkowe. Zaakceptuj wartości domyślne dla pełnej kopii zapasowej, lub kliknij przycisk **pełnej kopii zapasowej** Aby edytować te zasady.

    ![nowe zasady tworzenia kopii zapasowej pola](./media/backup-azure-sql-database/full-backup-policy.png)

    W zasadzie pełnej kopii zapasowej wybierz codziennie lub co tydzień częstotliwości. Jeśli wybierzesz codziennie, wybierz godziny i strefy czasowej, po rozpoczęciu zadania tworzenia kopii zapasowej. Jeśli wybierzesz codzienne pełne kopie zapasowe, nie można utworzyć różnicowych kopii zapasowych.

   ![codzienne ustawienie interwału](./media/backup-azure-sql-database/daily-interval.png)

    Jeśli wybierzesz co tydzień, wybierz dzień tygodnia, godziny i strefy czasowej po rozpoczęciu zadania tworzenia kopii zapasowej.

   ![Ustawienie interwału co tydzień](./media/backup-azure-sql-database/weekly-interval.png)

4. Domyślnie wybrane są wszystkie opcje zakresu przechowywania (codziennie, co tydzień, miesięczne i roczne). Usuń zaznaczenie pola wyboru limitu zakresu przechowywania, nie mają i Ustaw interwały do użycia. W menu zasady pełnej kopii zapasowej, kliknij przycisk **OK** aby zaakceptować ustawienia.

   ![Ustawienie interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

    Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Na przykład w przypadku wybrania codziennie pełna kopia zapasowa tylko jeden pełnej kopii zapasowej zostanie wywołany każdego dnia. W zależności od tego, co tydzień przechowywania, konkretny dzień tworzenia kopii zapasowej jest oznakowany i przechowywane na podstawie zakresu przechowywania co tydzień. Zakres przechowywania miesięcznych i rocznych działa podobnie.

5. Aby dodać zasady różnicowej kopii zapasowej, kliknij przycisk **różnicowej kopii zapasowej** aby otworzyć menu. 

   ![Otwórz różnicowej zasad](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Wybierz z menu zasad różnicowej kopii zapasowej **włączyć** otworzyć kontroli częstotliwości i przechowywania. Możesz wyzwolić, co najwyżej jeden różnicowej kopii zapasowej na dzień.
    > [!Important] 
    > Co najwyżej różnicowe kopie zapasowe mogą być przechowywane na okres 180 dni. Dłużej przechowywania należy możesz korzystać tylko pełne kopie zapasowe, różnicowe kopie zapasowe nie można użyć.
    >

   ![Edytowanie zasad różnicowej](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Kliknij przycisk **OK** Aby zapisać zasady i wrócić do menu głównego zasad tworzenia kopii zapasowej.

6. Aby dodać transakcyjne zasady kopii zapasowej dziennika, kliknij przycisk **kopii zapasowej dziennika** aby otworzyć menu. Wybierz z menu kopii zapasowej dziennika **włączyć**i ustaw kontroli częstotliwości i przechowywania. Kopie zapasowe dziennika może wystąpić tak często, jak co 15 minut, a także mogą być przechowywane przez okres do 35 dni. Kliknij przycisk **OK** Aby zapisać zasady i wrócić do menu głównego zasad tworzenia kopii zapasowej.

   ![Edytowanie zasad tworzenia kopii zapasowych dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Wybierz, czy włączyć kompresję kopii zapasowej SQL. Kompresja jest domyślnie wyłączona.

    Do wewnętrznej bazy danych kopia zapasowa Azure korzysta natywnych kompresja kopii zapasowej SQL.

8. Po dokonaniu wszystkich zmian z zasadami tworzenia kopii zapasowej, kliknij przycisk **OK**. 

   ![zakres przechowywania różnicowej](./media/backup-azure-sql-database/differential-backup-policy.png)

## <a name="restore-a-sql-database"></a>Przywróć bazę danych SQL

Kopia zapasowa Azure udostępnia funkcje, aby przywrócić pojedyncze bazy danych do określonej daty lub godziny, aż określony drugie, przy użyciu kopii zapasowej dziennika transakcji. Na podstawie czasów przywracania podasz, kopia zapasowa Azure automatycznie ustala odpowiedni pełnej, różnicowej i łańcuch kopii zapasowych dziennika wymaganych do przywrócenia danych.

Alternatywnie można wybrać określonego Full lub różnicowej kopii zapasowej do przywrócenia określony punkt odzyskiwania, a nie w określonym czasie.

Przywracanie bazy danych

1. Otwórz magazyn usług odzyskiwania zarejestrowany z maszyną wirtualną programu SQL.

2. Na pulpicie nawigacyjnym magazynu, wybierz **użycia** elementy kopii zapasowej można otworzyć menu elementów kopii zapasowej.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. W **elementów kopii zapasowych** menu, wybierz typ kopii zapasowej zarządzania **SQL na maszynie Wirtualnej Azure**. 

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Lista elementów kopii zapasowych można dostosować do listy baz danych serwera SQL. 

4. Z listy baz danych wybierz bazę danych, którą chcesz przywrócić.

    ![z listy wybierz SQL na maszynie Wirtualnej Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych zostanie otwarte menu. To menu zawiera szczegóły kopii zapasowej bazy danych, w tym:

    * Najstarsza i Przywróć punkty
    * Stan tworzenia kopii zapasowej dziennika w ostatnich 24 godzin (dla baz danych w pełnej i zbiorcze modelu odzyskiwania zarejestrowane, jeśli skonfigurowane dla kopii zapasowych dziennika transakcyjnych)

5. W menu wybranej bazy danych, kliknij przycisk **przywracania bazy danych** aby otworzyć menu przywracania.

    ![Wybierz opcję przywracania bazy danych](./media/backup-azure-sql-database/restore-db-button.png)

    **Przywrócić** otwiera menu i nie tak **Przywracanie konfiguracji** menu. **Przywracanie konfiguracji** menu jest pierwszym etapem konfigurowania przywracania. W tym menu można wybrać, której chcesz przywrócić dane. Dostępne opcje to:
    * Alternatywny miejsce — Użyj tej opcji, jeśli chcesz przywrócić bazę danych do lokalizacji alternatywnej przy jednoczesnym zachowaniu oryginalnej źródłowej bazy danych.
    * Zastąpienie bazy danych — przywraca dane do tego samego wystąpienia programu SQL Server jako oryginalnego źródła. To powoduje, że zastąpić oryginalnej bazy danych.

    > [!Important]
    > Jeśli wybrana baza danych należy do grupy dostępności AlwaysOn, SQL nie zezwala na bazy danych zostaną zastąpione. W takim przypadku tylko **lokalizacji alternatywnej** opcja jest włączona.
    >

    ![Kliknij przycisk Konfiguruj, aby otworzyć menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Przywróć do lokalizacji alternatywnej

Ta procedura przeprowadzi Cię przez przywrócenie danych do lokalizacji alternatywnej. Jeśli chcesz zastąpić bazy danych podczas przywracania, przejść do sekcji [przywrócić i zastąpić bazy danych](backup-azure-sql-database.md#restore-and-overwrite-the-database). W tej procedurze przyjęto założenie, Magazyn usług odzyskiwania otwartych i w menu Przywracanie konfiguracji. Jeśli nie, Rozpocznij od sekcji [przywracania bazy danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Można przywrócić bazę danych SQL Server, w tym samym regionie Azure, a serwer docelowy musi być SuperScript w magazynie usług odzyskiwania. 
>

**Serwera** menu rozwijanego wyświetlane są tylko serwery SQL zarejestrowana w magazynie usług odzyskiwania. Jeśli serwer ma nie jest w **serwera** listy, zobacz sekcję [baz danych serwera SQL odnajdywanie](backup-azure-sql-database.md#discover-sql-server-databases) odnaleźć serwera. W procesie odnajdowania bazy danych wszystkich nowych serwerach są rejestrowane w magazynie usług odzyskiwania.

1. W **Przywracanie konfiguracji** menu:

    * Wybierz **lokalizacji alternatywnej**,
    * Aby uzyskać **serwera**, wybierz serwer SQL, w której chcesz przywrócić bazę danych.
    * w **wystąpienia** menu rozwijanego wybierz wystąpienie serwera SQL
    * w **przywrócić Nazwa bazy danych** okna dialogowego, podaj nazwę docelowej bazy danych.
    * w razie potrzeby wybierz **zastąpić, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu programu SQL**.
    * Kliknij przycisk **OK** ukończyć konfigurowanie docelowego i przejdź do wybierania punktu przywracania.

    ![Wybierz alternatywną lokalizację, wystąpienia i nazwę w menu konfiguracji przywracania](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. W **wybierz punkt przywracania** menu, możesz wybrać dzienniki (punktu w czasie) lub pełnych & różnicowej punktu przywracania. Jeśli chcesz wykonać przywracanie do określonego dziennika w momencie, nadal w ramach tego kroku. Jeśli chcesz przywrócić pełna lub różnicowa punkt przywracania, przejdź do kroku 3.

    ![Wybierz menu punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Do punktu w czasie przywracania jest dostępna tylko dla kopii zapasowych dzienników dla baz danych z pełną & dziennikiem modelu odzyskiwania. Aby wykonać przywracanie do określonego punktu w czasie:

    1. Wybierz **dzienniki (punktu w czasie)** jako opcję przywracania.

        ![Wybierz typ punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **przywrócić daty/godziny**, kliknij ikonę kalendarza, aby otworzyć kalendarz. Pogrubione daty zawiera punkty odzyskiwania i zostanie wyróżniona bieżącą datę. Wybierz datę z kalendarza z punktami odzyskiwania. Nie można wybierać dat z żadne punkty odzyskiwania.

        ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu typu date oś czasu wykresu Wyświetla dostępne punkty odzyskiwania ciągłego zakresu.

    3. Przy użyciu wykresu osi czasu lub okna dialogowego czasu, należy określić godzinę dla punktu odzyskiwania, kliknij przycisk **OK** do wykonania kroku punktu przywracania.
    
       ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Wybierz punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

       ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Advanced Configuration** menu:

        * Aby zachować bazy danych nie działa po przywróceniu, na **Przywróć bez odzyskiwania** menu, wybierz opcję **włączone**.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, podaj nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** zatwierdzania ustawienia i zamknąć **Advanced Configuration**.

    5. Na **przywrócić** menu, kliknij przycisk **przywrócić** można rozpocząć zadania przywracania. W obszarze powiadomień możesz śledzić postęp. Można także śledzić postęp zadań przywracania bazy danych.

       ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-job-notification.png)

3. W **wybierz punkt przywracania** menu, wybierz punkt odzyskiwania. Możesz wybrać dzienniki (punktu w czasie) lub pełnych & różnicy. Jeśli chcesz przywrócić dziennika w momencie, wróć do kroku 2. Ten krok przywraca punkt określonych pełna lub różnicowa przywracania. Po wybraniu tej opcji widać punkty odzyskiwania wszystkich pełnej i różnice w ciągu ostatnich 30 dni. Jeśli chcesz wyświetlić punkty odzyskiwania, starsze niż 30 dni, kliknij przycisk **filtru** otworzyć **punkty przywracania filtru** menu. Jeśli punkt odzyskiwania różnicowa, kopia zapasowa Azure najpierw przywraca punktu odzyskiwania pełnego odpowiednie, a następnie stosuje wybranego różnicowej punktu odzyskiwania.

    ![Wybierz menu punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W **wybierz punkt przywracania** menu, wybierz **pełnych & różnicowej**.

       ![Wybierz menu punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zostanie wyświetlona lista dostępnych punktów odzyskiwania.

    2. Z listy punktów odzyskiwania, wybierz punkt odzyskiwania, a następnie kliknij przycisk **OK** do wykonania procedury punktu przywracania. 

        ![Wybierz punkt odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

        ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Advanced Configuration** menu:

        * Aby zachować bazy danych nie działa po przywróceniu, na **Przywróć bez odzyskiwania** menu, wybierz opcję **włączone**. **Przywróć bez odzyskiwania** jest domyślnie wyłączona.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, podaj nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** zatwierdzania ustawienia i zamknąć **Advanced Configuration**.

    4. Na **przywrócić** menu, kliknij przycisk **przywrócić** można rozpocząć zadania przywracania. W obszarze powiadomień możesz śledzić postęp. Można także śledzić postęp zadań przywracania bazy danych.

       ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Przywracanie i zastąpić bazy danych

Ta procedura przeprowadzi Cię przez Przywracanie danych, nadpisując bazy danych. Jeśli chcesz przywrócić do alternatywnej lokalizacji, należy przejść do sekcji [Przywróć do lokalizacji alternatywnej](backup-azure-sql-database.md#restore-to-an-alternate-location). W tej procedurze założono, a w magazynie usług odzyskiwania otwartych **Przywracanie konfiguracji** menu (zobacz poniższy rysunek). Jeśli nie, Rozpocznij od sekcji [przywracania bazy danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Kliknij przycisk Konfiguruj, aby otworzyć menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-overwrite-db.png)

**Serwera** menu rozwijanego wyświetlane są tylko serwery SQL zarejestrowana w magazynie usług odzyskiwania. Jeśli serwer ma nie jest w **serwera** listy, zobacz sekcję [baz danych serwera SQL odnajdywanie](backup-azure-sql-database.md#discover-sql-server-databases) odnaleźć serwera. W procesie odnajdowania bazy danych wszystkich nowych serwerach są rejestrowane w magazynie usług odzyskiwania.

1. W **Przywracanie konfiguracji** menu, wybierz opcję **zastąpić DB** i kliknij przycisk **OK** aby ukończyć konfigurowanie docelowego. 

   ![Kliknij przycisk Zastąp bazy danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **Serwera**, **wystąpienia**, i **przywrócić Nazwa bazy danych** okien dialogowych nie są konieczne.

2. W **wybierz punkt przywracania** menu, możesz wybrać dzienniki (punktu w czasie) lub pełnych & różnicowej punktu przywracania. Jeśli chcesz przywrócić dziennika w momencie, nadal w ramach tego kroku. Jeśli chcesz przywrócić pełnych & różnicowej punkt przywracania, przejdź do kroku 3.

    ![Wybierz menu punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Do punktu w czasie przywracania jest dostępna tylko dla kopii zapasowych dzienników dla baz danych z pełną & dziennikiem modelu odzyskiwania. Aby wybrać punkt w czasie przywracania do konkretnego drugi:

    1. Wybierz **dzienniki (punktu w czasie)** jako opcję przywracania.

        ![Wybierz typ punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **przywrócić daty/godziny**, kliknij ikonę kalendarza, aby otworzyć kalendarz. Pogrubione daty zawiera punkty odzyskiwania i zostanie wyróżniona bieżącą datę. Wybierz datę z kalendarza z punktami odzyskiwania. Nie można wybierać dat z żadne punkty odzyskiwania.

        ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu typu date oś czasu wykresu Wyświetla dostępne punkty odzyskiwania.

    3. Przy użyciu wykresu osi czasu lub okna dialogowego czasu, należy określić godzinę dla punktu odzyskiwania, kliknij przycisk **OK** do wykonania kroku punktu przywracania.
    
       ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Wybierz punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

       ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Advanced Configuration** menu:

        * Aby zachować bazy danych nie działa po przywróceniu, na **Przywróć bez odzyskiwania** menu, wybierz opcję **włączone**.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, podaj nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** zatwierdzania ustawienia i zamknąć **Advanced Configuration**.

    5. Na **przywrócić** menu, kliknij przycisk **przywrócić** można rozpocząć zadania przywracania. W obszarze powiadomień możesz śledzić postęp. Można także śledzić postęp zadań przywracania bazy danych.

       ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-job-notification.png)

3. W **wybierz punkt przywracania** menu, wybierz punkt odzyskiwania. Możesz wybrać dzienniki (punktu w czasie) lub pełnych & różnicy. Jeśli chcesz przywrócić dziennika w momencie, wróć do kroku 2. Ten krok przywraca punkt określonych pełna lub różnicowa przywracania. Po wybraniu tej opcji widać punkty odzyskiwania wszystkich pełnej i różnice w ciągu ostatnich 30 dni. Jeśli chcesz wyświetlić punkty odzyskiwania, starsze niż 30 dni, kliknij przycisk **filtru** otworzyć **punkty przywracania filtru** menu. Jeśli punkt odzyskiwania różnicowa, kopia zapasowa Azure najpierw przywraca punktu odzyskiwania pełnego odpowiednie, a następnie stosuje wybranego różnicowej punktu odzyskiwania.

    ![Wybierz menu punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W **wybierz punkt przywracania** menu, wybierz **pełnych & różnicowej**.

       ![Wybierz menu punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zostanie wyświetlona lista dostępnych punktów odzyskiwania.

    2. Z listy punktów odzyskiwania, wybierz punkt odzyskiwania, a następnie kliknij przycisk **OK** do wykonania procedury punktu przywracania. 

        ![Wybierz punkt odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

        ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Advanced Configuration** menu:

        * Aby zachować bazy danych nie działa po przywróceniu, na **Przywróć bez odzyskiwania** menu, wybierz opcję **włączone**. **Przywróć bez odzyskiwania** jest domyślnie wyłączona.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, podaj nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** zatwierdzania ustawienia i zamknąć **Advanced Configuration**.

    4. Na **przywrócić** menu, kliknij przycisk **przywrócić** można rozpocząć zadania przywracania. W obszarze powiadomień możesz śledzić postęp. Można także śledzić postęp zadań przywracania bazy danych.

       ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Zarządzanie operacjami usługi Kopia zapasowa Azure dla bazy danych SQL na maszynach wirtualnych platformy Azure

Ta sekcja zawiera informacje o różnych kopia zapasowa Azure operacji zarządzania dostępny dla serwera SQL na maszynach wirtualnych Azure. Istnieją następujące operacje wysokiego poziomu:

* Monitorowanie zadań
* Alerty kopii zapasowej
* Zatrzymaj ochronę bazy danych SQL
* Wznów ochronę bazy danych SQL
* Uruchomić zadanie tworzenia kopii zapasowej ad hoc.
* Wyrejestruj programu SQL server

### <a name="monitor-jobs"></a>Monitorowanie zadań
Kopia zapasowa Azure jest rozwiązaniem klasy Enterprise udostępnia zaawansowane kopii zapasowej alertów i powiadomień dla niepowodzenia (zobacz sekcja alerty kopii zapasowej poniżej). Jeśli nadal chcesz monitorować określonych zadań można użyć dowolnej z poniższych opcji opartych na wymagań:

#### <a name="using-azure-portal---recovery-services-vault-for-all-ad-hoc-operations"></a>Przy użyciu portalu Azure -> magazynu usług odzyskiwania dla wszystkich operacji w trybie ad hoc
Azure pokazuje kopii zapasowej wszystkich ręcznie wyzwalane lub ad hoc, zadania w portalu zadania tworzenia kopii zapasowej. Zadania dostępne w portalu include: wszystkie skonfigurować operacje tworzenia kopii zapasowej, ręcznie wyzwalane operacje tworzenia kopii zapasowej, operacje przywracania rejestracji odnajdywanie operacje bazy danych i Zatrzymaj operacje tworzenia kopii zapasowej. 
![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Wszystkich zaplanowanych zadań kopii zapasowej w tym pełne, dziennika i różnicowej kopii zapasowej nie zostanie wyświetlone w portalu i mogą być monitorowane, przy użyciu programu SQL Server Management Studio, zgodnie z poniższym opisem.
>

#### <a name="using-sql-server-management-studio-ssms-for-backup-jobs"></a>Za pomocą programu SQL Server Management Studio (SSMS) dla zadania tworzenia kopii zapasowej
Kopia zapasowa Azure korzysta natywnych interfejsów API SQL dla wszystkich operacji tworzenia kopii zapasowej. Przy użyciu macierzystych interfejsów API, można pobrać wszystkie informacje o zadaniu z [tabeli zestawu kopii zapasowych SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) w bazie danych msdb. 

Można użyć poniżej kwerendy, na przykład aby pobrać wszystkie zadania tworzenia kopii zapasowej dla określonej bazy danych o nazwie "DB1". Można dostosować poniżej zapytania więcej zaawansowane monitorowanie.
```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="backup-alerts"></a>Alerty kopii zapasowej

Z kopii zapasowych dziennika jest wykonywane co 15 minut czasami monitorowania zadań tworzenia kopii zapasowej może być niewygodny. Kopia zapasowa Azure planowane w takiej sytuacji potencjalnie niewygodny zapewniając alertów e-mail wyzwalane przez wszelkie niepowodzenia wykonywania kopii zapasowej. Alerty są konsolidowane na poziomie bazy danych przez kod błędu. Na przykład jeśli baza danych ma wiele błędów tworzenia kopii zapasowej, nie odbiera alert dla każdego błędu, możesz otrzymywać wiadomości e-mail przy pierwszym niepowodzeniu. Następnie można zalogować się do portalu Azure do monitorowania kolejnych błędów dla tej bazy danych. 

Aby monitorować alerty kopii zapasowej:

1. Zaloguj się do Twojej subskrypcji platformy Azure w [portalu Azure](https://portal.azure.com).

2. Otwórz magazyn usług odzyskiwania zarejestrowany z maszyną wirtualną programu SQL.

3. W menu magazynu usług odzyskiwania, wybierz **alerty i zdarzenia**. 

   ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. W **alerty i zdarzenia** menu, wybierz opcję **alerty kopii zapasowej** do wyświetlania na liście alertów.

   ![menu zaawansowanej konfiguracji](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Zatrzymaj ochronę na bazie danych programu SQL Server

Zatrzymanie ochrony bazy danych programu SQL Server, kopia zapasowa Azure zapyta, czy chcesz zachować punktów odzyskiwania. Istnieją dwa sposoby zatrzymanie ochrony bazy danych SQL:

* Zatrzymanie wszystkich przyszłych zadań kopii zapasowej i usunięcie wszystkich punktów odzyskiwania
* Zatrzymanie wszystkich przyszłych zadań kopii zapasowej, ale pozostawić punktów odzyskiwania 

Pozostawienie punktów odzyskiwania niesie koszt, jak punkty odzyskiwania dla bazy danych SQL przenoszenia chronione wystąpienia SQL cennik opłat oraz magazynu używane. Aby uzyskać więcej informacji na temat cen usługi Kopia zapasowa Azure SQL, zobacz [cennikiem usługi Kopia zapasowa Azure](https://azure.microsoft.com/pricing/details/backup/). Zatrzymanie ochrony bazy danych:

1. Otwórz magazyn usług odzyskiwania zarejestrowany z maszyną wirtualną programu SQL.

2. Na pulpicie nawigacyjnym magazynu, wybierz **użycia** elementy kopii zapasowej można otworzyć menu elementów kopii zapasowej.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. W **elementów kopii zapasowych** menu, wybierz typ kopii zapasowej zarządzania **SQL na maszynie Wirtualnej Azure**. 

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu celem tworzenia kopii zapasowej](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Lista elementów kopii zapasowych można dostosować do listy baz danych serwera SQL. 

4. Z listy baz danych wybierz bazę danych, której chcesz zatrzymać ochrony.

    ![z listy wybierz SQL na maszynie Wirtualnej Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych zostanie otwarte menu. 

5. W menu wybranej bazy danych, kliknij przycisk **kopii zapasowej Zatrzymaj** zatrzymanie ochrony bazy danych.

    ![Wybierz opcję przywracania bazy danych](./media/backup-azure-sql-database/stop-db-button.png)

    **Zatrzymać kopii zapasowej** zostanie otwarte menu.

6. W **zatrzymać kopii zapasowej** menu, wybierz, aby zachować dane z kopii zapasowej lub Usuń dane z kopii zapasowej. Opcjonalnie możesz podać przyczynę zatrzymywanie ochrony i komentarza.

    ![Wybierz opcję przywracania bazy danych](./media/backup-azure-sql-database/stop-backup-button.png)

7. Kliknij przycisk **kopii zapasowej Zatrzymaj** zatrzymać ochrony na bazie danych. 

### <a name="resume-protection-for-a-sql-database"></a>Wznów ochronę bazy danych SQL

Jeśli **Zachowaj dane kopii zapasowej** wybrano opcję podczas zatrzymywania ochrony dla bazy danych SQL, jest możliwe wznowić ochronę. Jeśli dane kopii zapasowej nie została zachowana, nie można wznowić ochronę. 

1. Aby wznowić ochronę bazy danych SQL, otwórz element kopii zapasowej, a następnie kliknij przycisk **wznowienie kopii zapasowej**.

    ![wznowić ochronę bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

   Zostanie otwarte menu zasad tworzenia kopii zapasowej.

2. Z **zasad tworzenia kopii zapasowej** menu, wybierz zasady, a następnie kliknij przycisk **zapisać**.

### <a name="trigger-an-adhoc-backup"></a>Wyzwól kopię zapasową adhoc

Gdy użytkownicy mogą Wyzwól kopię zapasową adhoc. Istnieją cztery typy kopii zapasowych ad hoc. Aby uzyskać szczegółowe informacje o poszczególnych typów, zobacz artykuł, [kopii zapasowych typy SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Pełna kopia zapasowa
* Skopiuj tylko pełna kopia zapasowa
* Różnicowej kopii zapasowej
* Tworzenie kopii zapasowej dziennika

### <a name="unregister-a-sql-server"></a>Wyrejestruj programu SQL Server

Aby wyrejestrować programu SQL server, po usunięciu ochrony, ale przed usunięciem magazynu

1. Otwórz magazyn usług odzyskiwania zarejestrowany z maszyną wirtualną programu SQL.

2. W **Zarządzaj** sekcji menu magazynu kliknij **infrastruktura kopii zapasowej**.  

   ![wznowić ochronę bazy danych](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. W **serwerów zarządzania** kliknij **chronione serwery**.

   ![wznowić ochronę bazy danych](./media/backup-azure-sql-database/protected-servers.png)

    Zostanie otwarte menu chronione serwery. 

4. W **chronione serwery** menu, wybierz serwer, który chcesz wyrejestrować. Jeśli chcesz usunąć magazyn, należy wyrejestrować wszystkie serwery.

5. W menu chronionych serwerów kliknij prawym przyciskiem myszy serwer chroniony i wybierz **usunąć**. 

   ![wznowić ochronę bazy danych](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup, zobacz przykładowy skrypt programu PowerShell umożliwiający tworzenie kopii zapasowej zaszyfrowanych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Back up encrypted VM (Tworzenie kopii zapasowej zaszyfrowanej maszyny wirtualnej)](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
