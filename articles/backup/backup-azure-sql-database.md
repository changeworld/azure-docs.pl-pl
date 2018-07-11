---
title: Utwórz kopię zapasową bazy danych SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: W tym samouczku wyjaśniono kopii zapasowych programu SQL Server na platformie Azure. W niniejszym artykule wyjaśniono również odzyskiwanie programu SQL Server.
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
ms.date: 7/6/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 32f45b66c4b1d22da3ffc4310a8a47c17319301f
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918054"
---
# <a name="back-up-sql-server-database-in-azure"></a>Tworzenie kopii zapasowej bazy danych programu SQL Server na platformie Azure

Bazy danych programu SQL Server są kluczowe dla działalności obciążenia wymagające niski cel punktu odzyskiwania (RPO) i długoterminowego przechowywania. Usługa Azure Backup udostępnia rozwiązania tworzenia kopii zapasowych programu SQL Server, która wymaga infrastruktury zero, co oznacza nie złożonych, Utwórz kopię zapasową serwera, nie agenta zarządzania lub magazynu kopii zapasowych do zarządzania. Usługa Azure Backup umożliwia scentralizowane zarządzanie dla kopii zapasowych, wszystkie serwery SQL lub nawet różnych obciążeń.

 W tym artykule dowiesz się:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące tworzenia kopii zapasowych programu SQL Server na platformie Azure
> * Tworzenie i używanie magazynu usługi Recovery Services
> * Konfigurowanie kopii zapasowych bazy danych programu SQL Server
> * Ustawienie zasad tworzenia kopii zapasowej (lub przechowywania) dla punktów odzyskiwania
> * Jak przywrócić bazy danych

Przed rozpoczęciem procedury przedstawione w tym artykule, należy mieć programu SQL Server działających na platformie Azure. [Maszyny wirtualne w portalu marketplace SQL umożliwia szybkie tworzenie programu SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Następujące elementy są znane ograniczenia publicznej wersji zapoznawczej.

- Maszyna wirtualna SQL wymaga łączności z Internetem na dostęp do platformy Azure z publicznymi adresami IP. Aby uzyskać więcej informacji, zobacz sekcję [ustanowienia połączenia z siecią](backup-azure-sql-database.md#establish-network-connectivity).
- Można chronić maksymalnie 2000 baz danych SQL w jednym magazynie usług Recovery Services. Dodatkowe bazy danych SQL powinny być przechowywane w osobnym magazynie usługi Recovery Services.
- [Wykonywanie kopii zapasowej rozproszonej grupy ma ograniczenia](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- Wystąpienia klastra SQL w tryb Failover (FCI) nie są obsługiwane.
- Umożliwia skonfigurowanie usługi Azure Backup, aby chronić bazy danych programu SQL Server w witrynie Azure portal. Obsługa programu Azure PowerShell, interfejsu wiersza polecenia i interfejsów API REST nie jest obecnie dostępna.

## <a name="supported-azure-geos"></a>Obsługiwanych regionach geograficznych systemu Azure

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

Następujące systemy operacyjne są obsługiwane. SQL marketplace maszyn wirtualnych platformy Azure i spoza witryny marketplace maszyn wirtualnych (programu SQL Server ręcznie zainstalowanym), są obsługiwane.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux nie jest obecnie obsługiwane.

### <a name="supported-versionseditions-of-sql-server"></a>Obsługiwane wersje/wersje programu SQL Server

- SQL 2012 Enterprise, Standard, Web Developer, Express
- SQL 2014 Enterprise, Standard, Web Developer, Express
- SQL 2016 Enterprise, Standard, Web Developer, Express
- SQL 2017 Enterprise, Standard, sieci Web i deweloper Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Wymagania wstępne dotyczące używania usługi Azure Backup w celu ochrony programu SQL Server 

Przed utworzeniem kopii zapasowej bazy danych programu SQL Server, sprawdź następujące warunki. :

- Identyfikowanie lub [Utwórz magazyn usługi Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) w tym samym regionie lub ustawień regionalnych, ponieważ maszyna wirtualna hostowany program SQL Server.
- [Sprawdzenie uprawnień na maszynie wirtualnej](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) potrzebne do tworzenia kopii zapasowych baz danych SQL.
- [Maszyna wirtualna SQL ma łączność sieciową](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Może mieć tylko jedno rozwiązanie tworzenia kopii zapasowych w czasie tworzenia kopii zapasowej bazy danych SQL Server. Wyłącz wszelkie inne kopii zapasowej SQL przed użyciem tej funkcji, inny kopie zapasowe będą kolidować i zakończyć się niepowodzeniem. Możesz włączyć usługę Azure Backup dla maszyn wirtualnych IaaS wraz z kopii zapasowej SQL bez konfliktów 
>

Jeśli te warunki w danym środowisku, przejdź do sekcji [skonfigurować swój magazyn, aby chronić bazę danych SQL](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Jeśli dowolny z wymagań wstępnych nie istnieje, kontynuuj czytanie tej sekcji.


## <a name="establish-network-connectivity"></a>Ustanowienie połączenia z siecią

Dla wszystkich operacji maszyny wirtualnej SQL musi mieć łączność platformy Azure z publicznymi adresami IP. Niepowodzenie operacji maszyny wirtualnej SQL (takich jak bazy danych, odnajdowanie, skonfigurowanie kopii zapasowej, zaplanowanych kopii zapasowych, przywracania punktów odzyskiwania i tak dalej) bez połączenia z publicznych adresów IP. Umożliwia jedną z następujących opcji zapewni przejrzystą ścieżkę ruchu kopii zapasowej:

- Lista dozwolonych adresów IP centrum danych platformy Azure zakresów — do platformy Azure z listy dozwolonych zakresów adresów IP centrum danych, należy użyć [strony Centrum informacji na temat zakresów adresów IP i instrukcje dotyczące pobierania](https://www.microsoft.com/download/details.aspx?id=41653). 
- Wdrażanie serwera proxy HTTP routing ruchu — jeśli wykonujesz kopię zapasową bazy danych SQL na maszynie wirtualnej, rozszerzenie kopii zapasowej na maszynie Wirtualnej używa interfejsów API protokołu HTTPS do wysyłania poleceń zarządzania do danych do usługi Azure Storage i Azure Backup. Rozszerzenie kopii zapasowej do uwierzytelniania również używa usługi Azure Active Directory (AAD). Kierowanie ruchu zapasowy numer wewnętrzny dla tych trzech usług za pośrednictwem serwera proxy HTTP, ponieważ jest jedynym składnikiem skonfigurowane do uzyskiwania dostępu do publicznej sieci internet.

To kompromis między opcje: koszt, kontrolę i możliwości zarządzania.

> [!NOTE]
>Tagi usługi dla usługi Azure Backup powinna być dostępna przez ogólnie dostępne.
>

| Opcja | Zalety | Wady |
| ------ | ---------- | ------------- |
| Lista dozwolonych adresów IP, zakresy | Bez dodatkowych kosztów. <br/> W przypadku otwierania dostępu w sieciowej grupie zabezpieczeń, użyj **AzureNetworkSecurityRule zestaw** polecenia cmdlet. | Trudne w zarządzaniu jako objęte zmień zakresy adresów IP wraz z upływem czasu. <br/>Zapewnia dostęp do całego systemu Azure, nie tylko magazyn.|
| Użyj serwera proxy HTTP   | Zapewnia szczegółową kontrolę na serwerze proxy magazynu dozwolone adresy URL. <br/>Pojedynczy punkt internet dostępu do maszyn wirtualnych. <br/> Nie może ulec zmianie adresów IP platformy Azure. | Dodatkowe koszty uruchamiania maszyn wirtualnych z oprogramowaniem serwera proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ustawianie uprawnień dla maszyn wirtualnych SQL spoza witryny marketplace

Aby utworzyć kopię zapasową maszyny wirtualnej, wymaga usługi Azure Backup **AzureBackupWindowsWorkload** można zainstalować rozszerzenia. Jeśli używasz portalu Azure marketplace maszyn wirtualnych, przejdź do sekcji [odnajdywania serwera baz danych programu SQL](backup-azure-sql-database.md#discover-sql-server-databases). Jeśli nie utworzono maszynę wirtualną, hostujące bazy danych SQL z witryny Azure marketplace, należy wykonać następującą sekcję, aby zainstalować rozszerzenie i ustawić odpowiednie uprawnienia. Oprócz **AzureBackupWindowsWorkload** rozszerzenia usługi Azure Backup wymaga uprawnień administratora systemu SQL do ochrony baz danych SQL. Podczas odnajdywania baz danych na maszynie wirtualnej, usługa Azure Backup tworzy konto usługi, NT Service\AzureWLBackupPluginSvc. Usługi Azure Backup do odnajdywania baz danych SQL, konto NT Service\AzureWLBackupPluginSvc musi mieć SQL i SQL uprawnienia administratora systemu. Poniższa procedura wyjaśnia, jak zapewnić te uprawnienia.

Aby skonfigurować uprawnienia:

1. W [witryny Azure portal](https://portal.azure.com), Otwórz magazyn usługi Recovery Services za pomocą ochrony baz danych SQL.
2. W menu pulpitu nawigacyjnego magazynu kliknij **+ kopia zapasowa** otworzyć **cel kopii zapasowej** menu.

   ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cel kopii zapasowej** menu w **gdzie jest uruchomione Twoje obciążenie?** menu, pozostaw **Azure** jako domyślny.

4. Na **jakich elementów chcesz utworzyć kopię zapasową** menu rozwiń menu rozwijane i wybierz **programu SQL Server na maszynie Wirtualnej platformy Azure**.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cel kopii zapasowej** w menu są wyświetlane dwa nowe kroki: **odnajdowanie baz danych na maszynach wirtualnych** i **konfigurowania kopii zapasowej**. **Znajdź bazy danych na maszynach wirtualnych** rozpoczyna wyszukiwanie maszyn wirtualnych platformy Azure.

    ![Pokazuje nowe kroki cel kopii zapasowej](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Kliknij przycisk **Uruchom odnajdowanie** wyszukiwania niechronionych maszyn wirtualnych w subskrypcji. W zależności od liczby niechronionych maszyn wirtualnych w subskrypcji może potrwać chwilę, aby przejść przez wszystkie maszyny wirtualne.

    ![Oczekiwanie na kopię zapasową](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po odnalezieniu niechronione maszyny wirtualnej pojawi się on na liście. Niechronione maszyny wirtualne są wyświetlane według ich maszynę wirtualną nazwą i grupą zasobów. Istnieje możliwość dla wielu maszyn wirtualnych mieć taką samą nazwę. Jednak maszyn wirtualnych o takiej samej nazwie należą do różnych grup zasobów. Jeśli na liście nie ma oczekiwanego maszyny wirtualnej, zobacz, czy maszyna wirtualna jest już chroniony w magazynie.

6. Z listy maszyn wirtualnych wybierz maszynę Wirtualną, zawierającą bazę danych SQL, którą chcesz utworzyć kopię zapasową, a następnie kliknij przycisk **odnajdowanie baz danych**. 

    Proces odnajdywania instaluje **AzureBackupWindowsWorkload** rozszerzenia na maszynie wirtualnej. Rozszerzenie umożliwia usłudze Azure Backup do komunikacji z maszyną wirtualną, aby go utworzyć kopię zapasową bazy danych SQL. Po zainstalowaniu rozszerzenia, usługa Azure Backup tworzy Windows wirtualnego konta usługi, **NT Service\AzureWLBackupPluginSvc**, na maszynie wirtualnej. Wirtualnego konta usługi wymaga uprawnień administratora systemu SQL. W procesie usługa wirtualnego konta instalacji, jeśli zostanie wyświetlony błąd **UserErrorSQLNoSysadminMembership**, zapoznaj się z sekcją [uprawnienia administratora systemu SQL ustalania](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    Obszar powiadomień jest przedstawiony postęp odnajdywania bazy danych. W zależności od liczby baz danych są na maszynie wirtualnej może upłynąć trochę czasu na ukończenie zadania. Po odnalezieniu wybranych baz danych, zostanie wyświetlony komunikat o powodzeniu.

    ![komunikat z powiadomieniem pomyślnego wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

Po skojarzeniu bazy danych z magazynu usługi Recovery Services, następnym krokiem jest [Konfigurowanie kopii zapasowej](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Naprawianie uprawnienia administratora systemu SQL

Podczas procesu instalacji, jeśli zostanie wyświetlony błąd **UserErrorSQLNoSysadminMembership**, użyj konta z uprawnieniami administratora systemu SQL, aby zalogować się do programu SQL Server Management Studio (SSMS). Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelniania Windows powinny działać.

1. Na serwerze SQL, otwórz **zabezpieczeń/logowania** folderu.

    ![Otwieranie folderów programu SQL Server i zabezpieczeń i zaloguj się do konta](./media/backup-azure-sql-database/security-login-list.png)

2. Do logowania do folderu, kliknij prawym przyciskiem myszy i wybierz **nowy identyfikator logowania**, a podczas logowania — nowe okno dialogowe, kliknij przycisk **wyszukiwania**

    ![Otwórz okno dialogowe wyszukiwania podczas logowania — nowy](./media/backup-azure-sql-database/new-login-search.png)

3. Od Windows wirtualnego konta usługi **NT Service\AzureWLBackupPluginSvc** został już utworzony podczas rejestracji maszyny wirtualnej i faza odnajdywania SQL, wprowadź nazwę konta, która jest wyświetlana w  **Wprowadź nazwę obiektu do wybrania** okna dialogowego. Kliknij przycisk **Sprawdź nazwy** do rozpoznania nazwy. 

    ![Kliknij przycisk Sprawdź nazwy, aby rozpoznać nazwę usługi nieznany](./media/backup-azure-sql-database/check-name.png)

4. Kliknij przycisk **OK** aby zamknąć okno dialogowe Wybieranie użytkownika lub grupy.

5. W **ról serwera** okna dialogowego, upewnij się, że **sysadmin** zaznaczona została rola. Następnie kliknij przycisk **OK** zamknąć **identyfikator logowania — nowy**.

    ![Upewnij się, że wybrano roli serwera sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Teraz powinny istnieć wymagane uprawnienia.

6. Jeśli naprawiono błąd uprawnień, nadal należy do skojarzenia z bazy danych z magazynu usługi Recovery Services. W witrynie Azure portal **serwerów chronionych** listy, kliknij prawym przyciskiem myszy serwer w wyniku błędu i wybierz **Rediscover baz danych**.

    ![Sprawdź, czy serwer ma odpowiednie uprawnienia](./media/backup-azure-sql-database/check-erroneous-server.png)

    Obszar powiadomień jest przedstawiony postęp odnajdywania bazy danych. W zależności od liczby baz danych są na maszynie wirtualnej może upłynąć trochę czasu na ukończenie zadania. W przypadku odnalezienia wybranych baz danych w obszarze powiadomień zostanie wyświetlony komunikat o powodzeniu.

    ![komunikat z powiadomieniem pomyślnego wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

Po skojarzeniu bazy danych z magazynu usługi Recovery Services, następnym krokiem jest [Konfigurowanie kopii zapasowej](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Usługa Azure Backup można wykryć wszystkie bazy danych w wystąpieniu programu SQL Server, dzięki czemu można je chronić zgodnie z wymaganiami kopii zapasowej. Poniższa procedura umożliwia zidentyfikowanie maszynie wirtualnej hostującej bazy danych SQL. Po zidentyfikowaniu maszyny wirtualnej, usługi Azure Backup instaluje rozszerzenie uproszczone do odnajdywania baz danych programu SQL server.

1. Zaloguj się do Twojej subskrypcji w [witryny Azure portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **wszystkich usług**.

    ![Wybierz opcję wszystkie usługi, w menu głównym](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. W oknie dialogowym z wszystkich usług, wpisz *usługi Recovery Services*. Po rozpoczęciu wpisywania, dane wejściowe filtrowanie listy zasobów. Gdy zostanie wyświetlony, wybierz **Magazyny usługi Recovery Services**.

    ![Wpisz wszystkie okna dialogowego usługi Recovery Services](./media/backup-azure-sql-database/all-services.png) <br/>

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji. 

4. Z listy magazyn usługi Recovery Services wybierz magazyn, który chcesz użyć do ochrony baz danych SQL.

5. W menu pulpitu nawigacyjnego magazynu kliknij **+ kopia zapasowa** otworzyć **cel kopii zapasowej** menu.

   ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

6. Na **cel kopii zapasowej** menu w **gdzie jest uruchomione Twoje obciążenie?** menu, pozostaw **Azure** jako domyślny.

7. Na **jakich elementów chcesz utworzyć kopię zapasową** menu rozwiń menu rozwijane i wybierz **programu SQL Server na maszynie Wirtualnej platformy Azure**.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Po wybraniu **cel kopii zapasowej** menu zostaną wyświetlone dwa kroki: odnajdowanie baz danych w maszynach wirtualnych i konfigurowanie kopii zapasowej. 

    ![Pokazuje nowe kroki cel kopii zapasowej](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Kliknij przycisk **Uruchom odnajdowanie** wyszukiwania niechronionych maszyn wirtualnych w subskrypcji. W zależności od liczby niechronionych maszyn wirtualnych w subskrypcji może potrwać chwilę, aby przejść przez wszystkie maszyny wirtualne.

    ![Oczekiwanie na kopię zapasową](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po odnalezieniu niechronione maszyny wirtualnej pojawi się on na liście. Wiele maszyn wirtualnych może mieć takiej samej nazwie. Jednak wiele maszyn wirtualnych o takiej samej nazwie należą do różnych grup zasobów. Niechronione maszyny wirtualne są wyświetlane według ich maszynę wirtualną nazwą i grupą zasobów. Jeśli nie ma oczekiwanego maszyny wirtualnej, zobacz, czy ta maszyna jest już chroniony w magazynie.

9. Z listy maszyn wirtualnych, zaznacz pole wyboru maszyny wirtualnej, który zawiera bazy danych SQL, które chcesz chronić, a następnie kliknij przycisk **odnajdowanie baz danych**.

    Usługa Azure Backup umożliwia odnalezienie wszystkich baz danych SQL na maszynie wirtualnej. Informacji na temat wykonywanego w fazie odnajdywania bazy danych na ten temat można znaleźć w poniższej sekcji [operacji wewnętrznej bazy danych podczas rozpoznawania baz danych SQL](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Po odnalezieniu baz danych SQL, można przystąpić do [skonfigurować zadanie tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Operacje wewnętrznej bazy danych, podczas odnajdywania baz danych SQL

Kiedy używasz **odnajdowanie baz danych** narzędzia, usługi Azure Backup wykonuje następujące operacje w tle:

- rejestruje maszynę wirtualną z magazynu usługi Recovery Services do tworzenia kopii zapasowych obciążeń. Wszystkie bazy danych na maszynie wirtualnej zarejestrowanej można tylko kopii zapasowej do tego magazynu usługi Recovery Services. 

- Instaluje **AzureBackupWindowsWorkload** rozszerzenia na maszynie wirtualnej. Wykonywanie kopii zapasowej bazy danych SQL jest rozwiązanie bez wykorzystania agentów, oznacza to, za pomocą rozszerzenia zainstalowane na maszynie wirtualnej, agent nie jest zainstalowany w bazie danych SQL.

- tworzy konto usługi **NT Service\AzureWLBackupPluginSvc**, na maszynie wirtualnej. Wszystkie operacje tworzenia kopii zapasowych i przywracania, użyj konta usługi. **NT Service\AzureWLBackupPluginSvc** musi mieć uprawnienia administratora systemu SQL. Wszystkie maszyny wirtualne SQL Marketplace wyposażony SqlIaaSExtension zainstalowane i AzureBackupWindowsWorkload używa SQLIaaSExtension, aby automatycznie uzyskać wymaganych uprawnień. Jeśli maszyna wirtualna nie ma SqlIaaSExtension zainstalowane, operacja odnajdywania z bazy danych nie powiedzie się i otrzymasz komunikat o błędzie **UserErrorSQLNoSysAdminMembership**. Aby dodać uprawnienia administratora systemu dla kopii zapasowej, postępuj zgodnie z instrukcjami [Ustawianie uprawnień usługi Azure Backup dla maszyn wirtualnych SQL spoza witryny marketplace](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Wybierz maszynę wirtualną i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Skonfiguruj kopię zapasową bazy danych programu SQL Server

Usługa Azure Backup udostępnia usługi zarządzania do ochrony baz danych programu SQL Server i zarządzanie zadaniami tworzenia kopii zapasowej. Funkcje zarządzania i monitorowania zależą od magazynu usługi Recovery Services. 

> [!NOTE]
> Może mieć tylko jedno rozwiązanie tworzenia kopii zapasowych w czasie tworzenia kopii zapasowej bazy danych SQL Server. Wyłącz wszelkie inne kopii zapasowej SQL przed użyciem tej funkcji, inny kopie zapasowe będą kolidować i zakończyć się niepowodzeniem. Możesz włączyć usługę Azure Backup dla maszyn wirtualnych IaaS wraz z kopii zapasowej SQL bez konfliktów 
>

Można skonfigurować ochrony dla usługi SQL database:

1. Otwórz magazyn usługi Recovery Services zarejestrowane przy użyciu maszyny wirtualnej SQL.

2. W menu pulpitu nawigacyjnego magazynu kliknij **+ kopia zapasowa** otworzyć **cel kopii zapasowej** menu.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cel kopii zapasowej** menu w **gdzie jest uruchomione Twoje obciążenie?** menu, pozostaw **Azure** jako domyślny.

4. Na **jakich elementów chcesz utworzyć kopię zapasową** menu rozwiń menu rozwijane i wybierz **programu SQL Server na maszynie Wirtualnej platformy Azure**.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Po wybraniu **cel kopii zapasowej** menu zostaną wyświetlone dwa kroki: odnajdowanie baz danych w maszynach wirtualnych i konfigurowanie kopii zapasowej. Jeśli zapoznaniu w tym artykule w kolejności został już odnaleziony niechronionych maszyn wirtualnych, a ten magazyn jest zarejestrowana z maszyną wirtualną. Teraz możesz przystąpić do konfigurowania ochrony dla baz danych SQL.

5. W menu Cel kopii zapasowej kliknij **konfigurowania kopii zapasowej**.

    ![Pokazuje nowe kroki cel kopii zapasowej](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Usługa Azure Backup Wyświetla wszystkie wystąpienia programu SQL z autonomicznych baz danych, a także grupy dostępności funkcji SQL AlwaysOn. Aby wyświetlić autonomicznych baz danych w wystąpieniu programu SQL, kliknij strzałkę obok nazwy wystąpienia i wyświetl jego bazy danych. Na poniższych ilustracjach przedstawiono przykłady wystąpienia autonomicznego i zawsze włączonej grupy dostępności.

    > [!NOTE]
    > W przypadku zawsze włączonej grupy dostępności SQL firma Microsoft uznaje preferencję tworzenia kopii zapasowych SQL. Jednak ze względu na ograniczenia platform SQL, pełne i różnicowe kopie zapasowe muszą mieć miejsce z węzła podstawowego. Tworzenie kopii zapasowej dziennika może się zdarzyć, zależnie od swoich preferencji tworzenia kopii zapasowej. Ze względu na to ograniczenie węzła podstawowego zawsze musi być zarejestrowany dla grup dostępności.
    >

    ![Lista baz danych w wystąpieniu SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Kliknij strzałkę obok zawsze włączonych grup dostępności, aby wyświetlić listę baz danych.

    ![Lista baz danych w grupie dostępności (AlwaysOn)](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Wybierz z listy baz danych, wszystkie opcje, które chcesz chronić, a następnie kliknij przycisk **OK**.

    ![Wybierz wiele baz danych, aby je chronić](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Można wybrać maksymalnie 50 baz danych w tym samym czasie. Jeśli chcesz chronić baz danych w ponad 50 wykonania wielu przebiegów. Po włączeniu ochrony 50 pierwszych baz danych, powtórz ten krok, aby chronić następny zestaw baz danych.
    > [!Note] 
    > W celu zoptymalizowania obciążeń kopii zapasowych, kopia zapasowa Azure dzieli duże zadania tworzenia kopii zapasowej na wiele partii. Maksymalna liczba baz danych w ramach jednego zadania tworzenia kopii zapasowej jest 50.
    >
    >

7. Utwórz lub wybierz zasady tworzenia kopii zapasowych, **kopii zapasowej** menu, wybierz opcję **zasady tworzenia kopii zapasowej**, aby otworzyć menu.

    ![Wybierz opcję Zasady tworzenia kopii zapasowej](./media/backup-azure-sql-database/select-backup-policy.png)

8. Z **wybierz zasady tworzenia kopii zapasowej** menu rozwijanego wybierz zasady tworzenia kopii zapasowej, a następnie kliknij przycisk **OK**. Aby uzyskać informacje na temat tworzenia zasad tworzenia kopii zapasowych, zobacz sekcję [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-sql-database.md#define-a-backup-policy).

    ![Wybierz zasady tworzenia kopii zapasowych z menu rozwijanego](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    W menu zasady tworzenia kopii zapasowych z **wybierz zasady tworzenia kopii zapasowej** menu rozwijanego możesz: 
    - domyślne zasady HourlyLogBackup 
    - istniejące zasady tworzenia kopii zapasowej, wcześniej utworzone dla programu SQL,
    - Aby [zdefiniować nową zasadę](backup-azure-sql-database.md#define-a-backup-policy) na podstawie celu punktu odzyskiwania (RPO) i zakresu przechowywania. 

    > [!Note]
    > Usługa Azure Backup obsługuje długoterminowego przechowywania danych w oparciu o schemacie tworzenia kopii zapasowej dziadek ojciec syn., aby zoptymalizować użycie magazynu zaplecza, jednocześnie spełniając potrzeby dotyczące zgodności.
    >

9. Po wybraniu zasad kopii zapasowych w **menu kopia zapasowa** kliknij **Włącz kopię zapasową**.

    ![Włącz wybrane zasady kopii zapasowych](./media/backup-azure-sql-database/enable-backup-button.png)

    Możesz śledzić postęp konfiguracji, w obszarze powiadomień portalu.

    ![Wyświetl obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Definiowanie zasad tworzenia kopii zapasowych

Zasady tworzenia kopii zapasowych określają macierz z informacjami po kopie zapasowe są pobierane i jak długo są przechowywane kopie zapasowe. Usługa Azure Backup umożliwia planowanie trzy typy kopii zapasowych baz danych SQL:

* Pełna kopia zapasowa - pełną kopię zapasową bazy tworzy kopię zapasową całej bazy danych. Pełna kopia zapasowa zawiera wszystkie dane w konkretnej bazy danych lub zestaw plików lub grup plików oraz wystarczającą ilość dziennik, aby odzyskać usuniętych danych. Co najwyżej użytkownik zainicjuje jedną pełną kopię zapasową dziennie. Istnieje możliwość wykonania pełnej kopii zapasowej na interwał codziennie lub co tydzień. 
* Różnicowa kopia zapasowa - różnicowej kopii zapasowej jest oparta na najnowszych, poprzedniego pełnych danych kopii zapasowej. Różnicowa kopia zapasowa przechwytuje tylko dane, które uległy zmianie od czasu pełnej kopii zapasowej. Co najwyżej możesz wyzwolić jednym różnicowa kopia zapasowa każdego dnia. Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej tego samego dnia.
* Tworzenie kopii zapasowej dziennika transakcji — kopię zapasową dziennika umożliwia w momencie przywracania do określonego drugi. Maksymalnie można skonfigurować, kopii zapasowych dziennika transakcji co 15 minut.

Utworzono zasadę w magazynie usługi Recovery Services poziomu. Jeśli masz wiele magazynów, magazynów można używać tych samych zasad tworzenia kopii zapasowej, ale należy zastosować zasady kopii zapasowych do każdego magazynu. Podczas tworzenia zasad tworzenia kopii zapasowej codziennie, pełnej kopii zapasowej jest ustawieniem domyślnym. Możesz dodać różnicowej kopii zapasowej, ale tylko wtedy, jeśli zaczniesz pełnych kopie zapasowych co tydzień. Poniższa procedura wyjaśnia sposób tworzenia zasad kopii zapasowych dla programu SQL server w maszynie wirtualnej platformy Azure.

Aby utworzyć zasady kopii zapasowych

1. W menu zasad kopii zapasowych z **wybierz zasady tworzenia kopii zapasowej** menu rozwijanego wybierz opcję **Utwórz nowy**.

   ![Utwórz nowe zasady tworzenia kopii zapasowej](./media/backup-azure-sql-database/create-new-backup-policy.png)

    Menu zasad tworzenia kopii zapasowych przełącza się do zapewnienia pól niezbędnych dla żadnych nowych zasad tworzenia kopii zapasowych serwera SQL.

   ![nowe pola zasad tworzenia kopii zapasowej](./media/backup-azure-sql-database/blank-new-policy.png)

2. W **nazwa_zasad**, podaj nazwę. 

3. Pełna kopia zapasowa jest obowiązkowy. Zaakceptuj wartości domyślne dla pełnej kopii zapasowej lub kliknij przycisk **pełnej kopii zapasowej** Aby edytować zasady.

    ![nowe pola zasad tworzenia kopii zapasowej](./media/backup-azure-sql-database/full-backup-policy.png)

    W ramach zasad pełnej kopii zapasowej wybierz częstotliwość codziennie lub co tydzień. Jeśli wybierzesz codziennie, wybierz godziny i strefy czasowej, po rozpoczęciu zadania tworzenia kopii zapasowej. Jeśli wybierzesz codziennej pełnej kopii, nie można utworzyć różnicowe kopie zapasowe.

   ![codzienne ustawienia interwału](./media/backup-azure-sql-database/daily-interval.png)

    Jeśli wybierzesz co tydzień, wybierz dzień tygodnia, godzinę i strefę czasową, po rozpoczęciu zadania tworzenia kopii zapasowej.

   ![ustawienia interwału co tydzień](./media/backup-azure-sql-database/weekly-interval.png)

4. Domyślnie wybrane są wszystkie opcje zakresu przechowywania (dzienne, tygodniowe, miesięczne i roczne). Usuń zaznaczenie pola wyboru limitu zakresu przechowywania, nie ma i ustalonych odstępach czasu do użycia. W menu zasad pełnej kopii zapasowej, kliknij przycisk **OK** aby zaakceptować ustawienia.

   ![ustawienia interwału zakres przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

    Punkty odzyskiwania są oznaczone do przechowywania danych, w oparciu o ich zakresu przechowywania. Na przykład wybranie codziennie pełna kopia zapasowa tylko jedną pełną kopię zapasową jest wyzwalany, każdego dnia. W zależności od Twojego tygodniowe przechowywanie kopii zapasowych w określonym dniu jest oznaczone i przechowywane na podstawie zakresu przechowywania co tydzień. Zakres przechowywania miesięcznych i rocznych zachowuje się podobnie.

5. Aby dodać różnicowej zasad tworzenia kopii zapasowej, kliknij **różnicowej kopii zapasowej** otworzyć jego menu. 

   ![Otwórz zasady różnicowe](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Wybierz z menu zasad różnicowej kopii zapasowej **Włącz** do otwierania kontrolki częstotliwość i przechowywania. Możesz wyzwolić, co najwyżej jeden różnicowa kopia zapasowa każdego dnia.
    > [!Important] 
    > Co najwyżej różnicowe kopie zapasowe będą przechowywane przez 180 dni. Jeśli potrzebujesz dłuższy okres przechowywania, możesz korzystać tylko pełne kopie zapasowe, różnicowe kopie zapasowe nie można użyć.
    >

   ![Edytuj zasady dotyczące różnicowej](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Kliknij przycisk **OK** do zapisania zasad i wróć do menu głównego zasad tworzenia kopii zapasowej.

6. Aby dodać zasady kopii zapasowej dziennika transakcji, kliknij **kopii zapasowej dziennika** otworzyć jego menu. Wybierz z menu kopia zapasowa dziennika **Włącz**i Ustaw częstotliwość i przechowywanie kontrolek. Kopie zapasowe dziennika może wystąpić nawet co 15 minut, a także mogą być zachowywane przez maksymalnie 35 dni. Kliknij przycisk **OK** do zapisania zasad i wróć do menu głównego zasad tworzenia kopii zapasowej.

   ![Edytuj zasady tworzenia kopii zapasowych dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Wybierz, czy włączyć kompresję kopii zapasowej SQL. Kompresja jest domyślnie wyłączona.

    Do wewnętrznej bazy danych usługa Azure Backup używa natywnego kompresja kopii zapasowej SQL.

8. Gdy wszystkie zmiany zostały wprowadzone do zasad tworzenia kopii zapasowej, kliknij przycisk **OK**. 

   ![zaakceptować nowe zasady](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Przywracanie bazy danych SQL

Usługa Azure Backup udostępnia funkcje, aby przywrócić pojedyncze bazy danych określone daty lub godziny, aż określony po drugie, przy użyciu kopii zapasowej dziennika transakcji. Na podstawie zostaną podane, usługi Azure Backup automatycznie ustala odpowiedni czas ich przywracania pełnej, różnicowej i łańcuch kopii zapasowych dziennika wymaganych do przywrócenia danych.

Alternatywnie można wybrać określonej Full lub różnicowej kopii zapasowej do przywrócenia określony punkt odzyskiwania, a nie w określonym czasie.
 > [!Note]
 > Przed wyzwoleniem przywracania bazy danych "master". Uruchom program SQL Server w trybie jednego użytkownika z opcję uruchamiania "-m AzureWorkloadBackup". Argument -m jest nazwa klienta, tylko ten klient będzie można otworzyć połączenia. Dla wszystkich systemowych baz danych (modelu master, msdb) Zatrzymaj usługę agenta SQL przed wyzwoleniem przywracania. Zamknij wszelkie aplikacje, które mogą próbować wykraść połączenia do dowolnego z tych baz danych.
>

Przywracanie bazy danych

1. Otwórz magazyn usługi Recovery Services zarejestrowane przy użyciu maszyny wirtualnej SQL.

2. Na pulpicie nawigacyjnym magazynu wybierz **użycia** elementy kopii zapasowej, aby otworzyć menu elementy kopii zapasowej.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. W **elementów kopii zapasowych** menu, wybierz typ zarządzania kopiami zapasowymi **SQL na maszynie Wirtualnej platformy Azure**. 

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Lista elementów kopii zapasowej dostosowuje Aby wyświetlić listę baz danych SQL. 

4. Z listy baz danych SQL wybierz bazę danych, którą chcesz przywrócić.

    ![z listy wybierz SQL na maszynie Wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych, zostanie otwarte menu. To menu zawiera szczegóły kopii zapasowej bazy danych, w tym:

    * Najstarsza i Przywróć punkty
    * stan kopii zapasowej dziennika, w ciągu ostatnich 24 godzin (w przypadku baz danych w pełnej i zbiorcze modelu odzyskiwania rejestrowane, jeśli skonfigurowane dla kopii zapasowych dziennika transakcji)

5. W menu wybranej bazy danych kliknij **przywracania bazy danych** aby otworzyć menu przywracania.

    ![Wybieranie operacji przywrócenia bazy danych](./media/backup-azure-sql-database/restore-db-button.png)

    **Przywrócić** zostanie otwarte menu i dlatego nie **przywracania konfiguracji** menu. **Przywracania konfiguracji** menu jest pierwszym etapem konfigurowania przywracania. W tym menu Wybierz, które chcesz przywrócić dane. Dostępne opcje to:
    * Lokalizacja alternatywna — Użyj tej opcji, jeśli chcesz przywrócić bazę danych do alternatywnej lokalizacji, przy jednoczesnym zachowaniu oryginalnej źródłowej bazy danych.
    * Zastąpienie bazy danych — przywraca dane do tego samego wystąpienia programu SQL Server jako oryginalnego źródła. To powoduje, że zastąpić oryginalną bazę danych.

    > [!Important]
    > Jeśli wybrana baza danych, należy do grupy dostępności Always On programu SQL nie zezwala na bazy danych zostaną zastąpione. W tym przypadku tylko **lokalizacji alternatywnej** opcja jest włączona.
    >

    ![Kliknij przycisk Konfiguruj, aby otworzyć menu konfiguracji przywracania](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Przywróć do lokalizacji alternatywnej

Ta procedura przeprowadzi przywracanie danych do alternatywnej lokalizacji. Jeśli chcesz zastąpić bazy danych, podczas przywracania, przejdź do sekcji [przywrócić i zastąpienia bazy danych](backup-azure-sql-database.md#restore-and-overwrite-the-database). Ta procedura zakłada, magazyn usługi Recovery Services otwartych i w menu przywracania konfiguracji. Jeśli nie masz, Rozpocznij od sekcji [przywrócić bazę danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Przywracania bazy danych programu SQL Server, w tym samym regionie platformy Azure, a serwer docelowy musi być zarejestrowany w magazynie usług odzyskiwania. 
>

**Serwera** menu rozwijane zawiera tylko serwery SQL zarejestrowanych w magazynie usługi Recovery Services. Jeśli serwer ma nie znajduje się w **serwera** listę, zobacz sekcję [odnajdywania serwera baz danych programu SQL](backup-azure-sql-database.md#discover-sql-server-databases) można znaleźć serwera. W procesie odnajdowania bazy danych nowych serwerów są rejestrowane w magazynie usługi Recovery Services.

1. W **przywracania konfiguracji** menu:

    * Wybierz **lokalizacji alternatywnej**,
    * Aby uzyskać **serwera**, wybierz programu SQL server, w której chcesz przywrócić bazę danych.
    * w **wystąpienia** menu rozwijanego wybierz wystąpienie serwera SQL
    * w **przywrócić Nazwa bazy danych** okna dialogowego, podaj nazwę docelowej bazy danych.
    * w razie potrzeby wybierz **zastąpić, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu SQL**.
    * Kliknij przycisk **OK** ukończenie konfigurowania miejsca docelowego i przejdź do wybierania punktu przywracania.

    ![Wybierz alternatywną lokalizację, wystąpienia i nazwę w menu konfiguracji przywracania](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. W **wybierz punkt przywracania** menu, możesz wybrać dzienniki (punkt w czasie) lub pełna i różnicowa punkt przywracania. Jeśli chcesz przywrócić do określonego dziennika w momencie nadal z tym krokiem. Jeśli chcesz przywrócić punkt przywracania pełne i różnicowe, przejdź do kroku 3.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Punkt w czasie przywracania jest dostępna tylko dla kopii zapasowych dzienników dla baz danych z pełnej & niepełnym modelu odzyskiwania. Aby przywrócić do określonego punktu w czasie:

    1. Wybierz **dzienniki (punkt w czasie)** jako opcja przywracania.

        ![Wybierz typ punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **Data/Godzina przywracania**, kliknij ikonę kalendarza, aby otworzyć kalendarz. Pogrubione daty zawiera punkty odzyskiwania, a bieżąca data jest wyróżniona. Wybierz datę z kalendarza z punktami odzyskiwania. Nie można wybrać dat za żadne punkty odzyskiwania.

        ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu daty, wykres osi czasu wyświetla dostępne punkty odzyskiwania ciągłego zakresu.

    3. Za pomocą wykres osi czasu lub okna dialogowego, należy określić godzinę punktu odzyskiwania, a następnie kliknij przycisk **OK** aby ukończyć etap tworzenia punktu przywracania.
    
       ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Wybierz punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Advanced Configuration** menu:

        * W celu zachowania niedziałającą po przywróceniu, na **Przywróć z opcją NORECOVERY** menu, wybierz opcję **włączone**.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, należy podać nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** aby zatwierdzić ustawienia i zamknąć **Advanced Configuration**.

    5. Na **przywrócić** menu, kliknij przycisk **przywrócić** można uruchomić zadania przywracania. Postęp można śledzić w obszarze powiadomień. Można także śledzić postęp zadania przywracania bazy danych.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-job-notification.png)

3. W **wybierz punkt przywracania** menu, wybierz punkt odzyskiwania. Możesz wybrać dzienniki (punkt w czasie) lub pełna i różnicowa. Jeśli chcesz przywrócić dziennika w momencie, wróć do kroku 2. Ten krok spowoduje przywrócenie punktu określonego pełna lub różnicowa przywracania. Po wybraniu tej opcji widać wszystkie pełne i różnicowe punktów odzyskiwania z ostatnich 30 dni. Jeśli chcesz wyświetlić punkty odzyskiwania starsze niż 30 dni, kliknij przycisk **filtru** otworzyć **punktów przywracania filtru** menu. Wybranie punktu odzyskiwania różnicowa kopia zapasowa Azure najpierw przywraca punkt odpowiednie pełnego odzyskiwania, a następnie stosuje wybrany różnicowej punkt odzyskiwania.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W **wybierz punkt przywracania** menu, wybierz **pełna i różnicowa**.

       ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zostanie wyświetlona lista dostępnych punktów odzyskiwania.

    2. Z listy punktów odzyskiwania, wybierz punkt odzyskiwania, a następnie kliknij przycisk **OK** do wykonania procedury punktu przywracania. 

        ![Wybierz punkt pełnego odzyskiwania](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Punktu przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

        ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Advanced Configuration** menu:

        * W celu zachowania niedziałającą po przywróceniu, na **Przywróć z opcją NORECOVERY** menu, wybierz opcję **włączone**. **Przywróć z opcją NORECOVERY** jest domyślnie wyłączona.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, należy podać nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** aby zatwierdzić ustawienia i zamknąć **Advanced Configuration**.

    4. Na **przywrócić** menu, kliknij przycisk **przywrócić** można uruchomić zadania przywracania. Postęp można śledzić w obszarze powiadomień. Można także śledzić postęp zadania przywracania bazy danych.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Przywracanie i zastąpienia bazy danych

Ta procedura zawiera opis sposobu przywracania danych i zastępowanie bazy danych. Jeśli chcesz przywrócić do innej lokalizacji, przejdź do sekcji [Przywróć do lokalizacji alternatywnej](backup-azure-sql-database.md#restore-to-an-alternate-location). W tej procedurze założono magazynu usługi Recovery Services otwartych, a w **przywracania konfiguracji** menu (zobacz poniższy obraz). Jeśli nie masz, Rozpocznij od sekcji [przywrócić bazę danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Kliknij przycisk Konfiguruj, aby otworzyć menu konfiguracji przywracania](./media/backup-azure-sql-database/restore-overwrite-db.png)

**Serwera** menu rozwijane zawiera tylko serwery SQL zarejestrowanych w magazynie usługi Recovery Services. Jeśli serwer ma nie znajduje się w **serwera** listę, zobacz sekcję [odnajdywania serwera baz danych programu SQL](backup-azure-sql-database.md#discover-sql-server-databases) można znaleźć serwera. W procesie odnajdowania bazy danych nowych serwerów są rejestrowane w magazynie usługi Recovery Services.

1. W **przywracania konfiguracji** menu, wybierz opcję **zastąpić DB** i kliknij przycisk **OK** można ukończyć konfigurowania miejsca docelowego. 

   ![Kliknij przycisk Zastąp bazy danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **Serwera**, **wystąpienia**, i **przywrócić Nazwa bazy danych** okien dialogowych nie są konieczne.

2. W **wybierz punkt przywracania** menu, możesz wybrać dzienniki (punkt w czasie) lub pełna i różnicowa punkt przywracania. Jeśli chcesz przywrócić dziennika w momencie, przejdź do tego kroku. Jeśli chcesz przywrócić punkt przywracania pełna i różnicowa przejść od razu do kroku 3.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Punkt w czasie przywracania jest dostępna tylko dla kopii zapasowych dzienników dla baz danych z pełnej & niepełnym modelu odzyskiwania. Aby wybrać punkt w czasie przywracania do określonego drugie:

    1. Wybierz **dzienniki (punkt w czasie)** jako opcja przywracania.

        ![Wybierz typ punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **Data/Godzina przywracania**, kliknij ikonę kalendarza, aby otworzyć kalendarz. Pogrubione daty zawiera punkty odzyskiwania, a bieżąca data jest wyróżniona. Wybierz datę z kalendarza z punktami odzyskiwania. Nie można wybrać dat za żadne punkty odzyskiwania.

        ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu daty, wykres osi czasu wyświetla dostępne punkty odzyskiwania.

    3. Za pomocą wykres osi czasu lub okna dialogowego, należy określić godzinę punktu odzyskiwania, a następnie kliknij przycisk **OK** aby ukończyć etap tworzenia punktu przywracania.
    
       ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Wybierz punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Z **Advanced Configuration** menu:

        * W celu zachowania niedziałającą po przywróceniu, na **Przywróć z opcją NORECOVERY** menu, wybierz opcję **włączone**.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, należy podać nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** aby zatwierdzić ustawienia i zamknąć **Advanced Configuration**.

    5. Na **przywrócić** menu, kliknij przycisk **przywrócić** można uruchomić zadania przywracania. Postęp można śledzić w obszarze powiadomień. Można także śledzić postęp zadania przywracania bazy danych.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-job-notification.png)

3. W **wybierz punkt przywracania** menu, wybierz punkt odzyskiwania. Możesz wybrać dzienniki (punkt w czasie) lub pełna i różnicowa. Jeśli chcesz przywrócić dziennika w momencie, wróć do kroku 2. Ten krok spowoduje przywrócenie punktu określonego pełna lub różnicowa przywracania. Po wybraniu tej opcji widać wszystkie pełne i różnicowe punktów odzyskiwania z ostatnich 30 dni. Jeśli chcesz wyświetlić punkty odzyskiwania starsze niż 30 dni, kliknij przycisk **filtru** otworzyć **punktów przywracania filtru** menu. Wybranie punktu odzyskiwania różnicowa kopia zapasowa Azure najpierw przywraca punkt odpowiednie pełnego odzyskiwania, a następnie stosuje wybrany różnicowej punkt odzyskiwania.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W **wybierz punkt przywracania** menu, wybierz **pełna i różnicowa**.

       ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Zostanie wyświetlona lista dostępnych punktów odzyskiwania.

    2. Z listy punktów odzyskiwania, wybierz punkt odzyskiwania, a następnie kliknij przycisk **OK** do wykonania procedury punktu przywracania. 

        ![Wybierz punkt pełnego odzyskiwania](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Punktu przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

        ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Z **Advanced Configuration** menu:

        * W celu zachowania niedziałającą po przywróceniu, na **Przywróć z opcją NORECOVERY** menu, wybierz opcję **włączone**. **Przywróć z opcją NORECOVERY** jest domyślnie wyłączona.
        * Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, należy podać nową ścieżkę w **docelowej** kolumny.
        * Kliknij przycisk **OK** aby zatwierdzić ustawienia i zamknąć **Advanced Configuration**.

    4. Na **przywrócić** menu, kliknij przycisk **przywrócić** można uruchomić zadania przywracania. Postęp można śledzić w obszarze powiadomień. Można także śledzić postęp zadania przywracania bazy danych.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Zarządzanie operacjami usługi Azure Backup dla programu SQL na maszynach wirtualnych platformy Azure

Ta sekcja zawiera informacje o różnych kopia zapasowa Azure management operacje dostępne dla programu SQL na maszynach wirtualnych platformy Azure. Istnieją następujące operacje wysokiego poziomu:

* Monitorowanie zadań
* Alerty kopii zapasowej
* Zatrzymanie ochrony bazy danych SQL
* Wznawianie ochrony bazy danych SQL
* Wyzwolić zadanie tworzenia kopii zapasowej ad hoc
* Wyrejestrowywanie serwera SQL

### <a name="monitor-jobs"></a>Monitorowanie zadań
Usługa Azure Backup jest klasy przedsiębiorstwa zapewnia zaawansowane kopii zapasowej alertów i powiadomień pod kątem awarii (zobacz poniższą sekcję alerty kopii zapasowej). Jeśli nadal chcesz monitorować określonego zadania można użyć dowolnej z następujących opcji, zgodnie z wymaganiami:

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>Użyj witryny Azure portal dla wszystkich operacji ad hoc
Azure Backup pokazuje wszystkie ręcznie wyzwolony lub ad hoc, zadania w portalu zadania kopii zapasowej. Zadania dostępne w portalu dołączania: wszystkie konfigurowania operacje tworzenia kopii zapasowej, ręcznie wyzwolone operacje tworzenia kopii zapasowej, operacje przywracania rejestracji i odnajdywania operacji bazy danych i zatrzymać operacje tworzenia kopii zapasowej. 
![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Wszystkich zaplanowanych zadań kopii zapasowej w tym pełnej, różnicowej i dziennika kopii zapasowej nie będą wyświetlane w portalu i mogą być monitorowane, przy użyciu programu SQL Server Management Studio, zgodnie z poniższym opisem.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Użyj programu SQL Server Management Studio zadań tworzenia kopii zapasowej
Usługa Azure Backup używa natywnych interfejsów API SQL dla wszystkich operacji tworzenia kopii zapasowej. Przy użyciu macierzystych interfejsów API, możesz pobrać wszystkie informacje o zadaniu z [tabeli zestawu kopii zapasowych SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) w bazie danych msdb.

Poniższy przykład to zapytanie, aby pobrać wszystkie zadania tworzenia kopii zapasowej bazy danych o nazwie **DB1**. Dostosowywanie zapytania w przypadku bardziej zaawansowanego monitorowania.
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

Za pomocą kopii zapasowych dziennika występuje co 15 minut od czasu do czasu monitorowania zadań tworzenia kopii zapasowej może być żmudne. Usługa Azure Backup planowanych sytuacji potencjalnie uciążliwe, zapewniając alerty e-mail wyzwalane przez jakiekolwiek niepowodzenie tworzenia kopii zapasowej. Alerty są konsolidowane na poziomie bazy danych przez kod błędu. Na przykład jeśli baza danych ma wiele błędów tworzenia kopii zapasowych, nie odbiera alert dla każdego błędu, otrzymasz wiadomość e-mail dla pierwszego niepowodzenia. Następnie można zalogować się do witryny Azure portal do monitorowania kolejnych niepowodzeń dla tej bazy danych. 

Aby monitorować alerty kopii zapasowej:

1. Zaloguj się do subskrypcji platformy Azure w [witryny Azure portal](https://portal.azure.com).

2. Otwórz magazyn usługi Recovery Services zarejestrowane przy użyciu maszyny wirtualnej SQL.

3. W menu magazynu usługi Recovery Services, wybierz **alerty i zdarzenia**. 

   ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. W **alerty i zdarzenia** menu, wybierz opcję **alerty kopii zapasowej** do wyświetlania na liście alertów.

   ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Zatrzymanie ochrony bazy danych programu SQL Server

Zatrzymanie ochrony bazy danych programu SQL Server, usługi Azure Backup pyta, czy do przechowywania punktów odzyskiwania. Istnieją dwa sposoby na zatrzymanie ochrony bazy danych SQL:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej i Usuń wszystkie punkty odzyskiwania
* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej, ale pozostawienie punktów odzyskiwania 

Pozostawienia punktów odzyskiwania niesie ze sobą koszt, ponieważ punkty odzyskiwania dla programu SQL wykonuje chronionego wystąpienia SQL cen opłaty, a także zajmowanego w magazynie. Aby uzyskać więcej informacji na temat cen usługi Azure Backup SQL, zobacz [stronę z cennikiem usługi Kopia zapasowa Azure](https://azure.microsoft.com/pricing/details/backup/). Aby zatrzymać ochronę dla bazy danych:

1. Otwórz magazyn usługi Recovery Services zarejestrowane przy użyciu maszyny wirtualnej SQL.

2. Na pulpicie nawigacyjnym magazynu wybierz **użycia** elementy kopii zapasowej, aby otworzyć menu elementy kopii zapasowej.

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. W **elementów kopii zapasowych** menu, wybierz typ zarządzania kopiami zapasowymi **SQL na maszynie Wirtualnej platformy Azure**. 

    ![Kliknij pozycję + Kopia zapasowa, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Lista elementów kopii zapasowej dostosowuje Aby wyświetlić listę baz danych SQL. 

4. Z listy baz danych SQL wybierz bazę danych, które na zatrzymanie ochrony.

    ![z listy wybierz SQL na maszynie Wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych, zostanie otwarte menu. 

5. W menu wybranej bazy danych kliknij **Zatrzymaj kopię zapasową** na zatrzymanie ochrony bazy danych.

    ![Wybieranie operacji przywrócenia bazy danych](./media/backup-azure-sql-database/stop-db-button.png)

    **Zatrzymaj tworzenie kopii zapasowej** zostanie otwarte menu.

6. W **Zatrzymaj tworzenie kopii zapasowej** menu wybrać opcję Zachowaj dane kopii zapasowej lub usuwanie danych kopii zapasowej. Opcjonalnie możesz podać przyczynę zatrzymanie ochrony i komentarz.

    ![Wybieranie operacji przywrócenia bazy danych](./media/backup-azure-sql-database/stop-backup-button.png)

7. Kliknij przycisk **Zatrzymaj kopię zapasową** zatrzymanie ochrony bazy danych. 

### <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Jeśli **Zachowaj dane kopii zapasowej** wybrano opcję podczas zatrzymywania ochrony dla usługi SQL database, jest możliwe wznowić ochronę. Jeśli dane kopii zapasowej nie została zachowana, nie można wznowić ochronę. 

1. Aby wznowić ochronę bazy danych SQL, otwórz element kopii zapasowej, a następnie kliknij przycisk **wznowienie kopii zapasowej**.

    ![wznawianie ochrony bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

   Zostanie otwarte menu zasady tworzenia kopii zapasowych.

2. Z **zasad tworzenia kopii zapasowej** menu, wybierz zasady, a następnie kliknij przycisk **Zapisz**.

### <a name="trigger-an-adhoc-backup"></a>Wyzwalanie tworzenia kopii zapasowej ad hoc

Zawsze, gdy chcesz, możesz wyzwolić kopię zapasową adhoc. Istnieją cztery typy kopii zapasowych ad hoc. Szczegółowe informacje na temat każdego typu, zobacz artykuł [kopii zapasowych typów SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Pełna kopia zapasowa
* Kopiuj tylko pełną kopię zapasową
* Różnicowa kopia zapasowa
* Kopia zapasowa dziennika

### <a name="unregister-a-sql-server"></a>Wyrejestrowywanie serwera SQL

Aby wyrejestrować programu SQL server, po usunięciu ochrony, ale przed usunięciem magazynu

1. Otwórz magazyn usługi Recovery Services zarejestrowane przy użyciu maszyny wirtualnej SQL.

2. W **Zarządzaj** sekcji menu magazynu kliknij **infrastruktura zapasowa**.  

   ![wznawianie ochrony bazy danych](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. W **serwerów zarządzania** kliknij **serwerów chronionych**.

   ![wznawianie ochrony bazy danych](./media/backup-azure-sql-database/protected-servers.png)

    Zostanie otwarte menu serwerów chronionych. 

4. W **serwerów chronionych** menu, wybierz serwer, na którym chcesz wyrejestrować. Jeśli chcesz usunąć magazynu, musisz wyrejestrować wszystkie serwery.

5. W menu chronionych serwerów kliknij prawym przyciskiem myszy serwer chroniony i wybierz **Usuń**. 

   ![wznawianie ochrony bazy danych](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>SQL database backup — często zadawane pytania

W poniższej sekcji przedstawiono dodatkowe informacje na temat kopii zapasowej bazy danych SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>Więc minimalizuje wpływ na program SQL server czy mogę ograniczyć szybkość zasad tworzenia kopii zapasowej SQL

Tak, możesz ograniczyć szybkość jaką wykonuje zasad tworzenia kopii zapasowej. Aby zmienić ustawienia:

1. Na serwerze SQL w `C:\Program Files\Azure Workload Backup\bin` folder, otwórz **TaskThrottlerSettings.json**.

2. W **TaskThrottlerSettings.json** pliku, zmień **DefaultBackupTasksThreshold** niższą wartość, na przykład 5.

3. Zapisać zmiany i zamknij plik.

4. Na serwerze SQL, otwórz Menedżera zadań, a następnie ponownie uruchom **usługę koordynatora obciążenia kopia zapasowa Azure**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełnej kopii zapasowej z repliki pomocniczej

Nie, ta funkcja nie jest obsługiwana.

### <a name="do-successful-backup-jobs-create-alerts"></a>Zadania tworzenia kopii zapasowej zakończone pomyślnie, należy tworzyć alerty

Nie. Pomyślne zadania tworzenia kopii zapasowej nie generują alerty. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powieść.

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>Zaplanowane zadanie tworzenia kopii zapasowej szczegółowe informacje przedstawiono w menu zadania

Nie. W menu zadania pokazuje szczegóły zadania ad hoc, ale nie uwzględnia zaplanowanych zadań kopii zapasowej. Jeśli wszelkich zaplanowanych zadań kopii zapasowej nie powiedzie się, wszystkie szczegółowe informacje można znaleźć w alertach zadania zakończonego niepowodzeniem. Jeśli chcesz monitorować wszystkie zaplanowane i zadania tworzenia kopii zapasowej ad hoc, [używania programu SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>Zaznaczenie programu SQL server zostanie przyszłych baz danych automatycznie dodana

Nie. Podczas konfigurowania ochrony dla programu SQL server, jeśli pole wyboru na poziomie serwera, dodaje wszystkie bazy danych. Jednak jeśli dodasz po skonfigurowaniu ochrony baz danych programu SQL Server, należy ręcznie dodać nowych baz danych, aby chronić je. Bazy danych nie są automatycznie uwzględnione w skonfigurowanym ochrony.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Zmiana modelu odzyskiwania jak ponownie rozpocząć ochrony

W przypadku zmiany modelu odzyskiwania Wyzwól tworzenie pełnej kopii zapasowej, a następnie rozpoczyna się tworzenie kopii zapasowych dzienników, zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup, zobacz przykładowy skrypt programu PowerShell umożliwiający tworzenie kopii zapasowej zaszyfrowanych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Back up encrypted VM (Tworzenie kopii zapasowej zaszyfrowanej maszyny wirtualnej)](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
