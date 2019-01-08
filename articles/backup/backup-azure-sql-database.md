---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure | Microsoft Docs
description: W tym samouczku opisano sposób tworzenia kopii zapasowych programu SQL Server na platformie Azure. W artykule objaśniono również proces odzyskiwania programu SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: raynew
ms.openlocfilehash: 50085336c59f2284f357e32b875eae08ff90d30f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790178"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure

Bazy danych programu SQL Server to krytyczne obciążenia, które wymagają niskiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Usługa Azure Backup udostępnia rozwiązanie do tworzenia kopii zapasowych programu SQL Server, które nie wymaga infrastruktury: nie potrzeba skomplikowanego serwera kopii zapasowych, agenta zarządzania ani magazynu kopii zapasowych do zarządzania. Usługa Azure Backup umożliwia scentralizowane zarządzanie kopiami zapasowymi na wszystkich serwerach, na których działa program SQL Server, a nawet w różnych obciążeniach.

W tym artykule omówiono następujące zagadnienia:

> [!div class="checklist"]
> * Wymagania wstępne dotyczące tworzenia kopii zapasowej wystąpienia programu SQL Server na platformie Azure.
> * Sposób tworzenia i używania magazynu usługi Recovery Services.
> * Sposób konfigurowania kopii zapasowych baz danych programu SQL Server.
> * Sposób ustawiania zasad tworzenia kopii zapasowej (lub przechowywania) dla punktów odzyskiwania.
> * Sposób przywracania bazy danych.

Przed rozpoczęciem wykonywania procedur opisanych w tym artykule musisz mieć wystąpienie programu SQL Server, które działa na platformie Azure. [Używanie maszyn wirtualnych usługi SQL Marketplace w celu szybkiego tworzenia wystąpienia programu SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Poniżej przedstawiono znane ograniczenia dotyczące publicznej wersji zapoznawczej:

- Maszyna wirtualna SQL wymaga łączności z Internetem do uzyskiwania dostępu do publicznych adresów IP platformy Azure. Aby uzyskać więcej informacji, zobacz [Ustanawianie łączności sieciowej](backup-azure-sql-database.md#establish-network-connectivity).
- W jednym magazynie usług Recovery Services można chronić maksymalnie 2000 baz danych SQL. Dodatkowe bazy danych SQL powinny być przechowywane w osobnym magazynie usługi Recovery Services.
- [Kopie zapasowe rozproszonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) podlegają ograniczeniom.
- Zawsze włączone wystąpienia klastra trybu failover w programie SQL Server nie są obsługiwane w przypadku tworzenia kopii zapasowej.
- Do skonfigurowania usługi Azure Backup na potrzeby chronienia baz danych programu SQL Server należy użyć witryny Azure Portal. Program Azure PowerShell, interfejs wiersza polecenia platformy Azure i interfejsy API REST nie są obecnie obsługiwane.
- Operacje tworzenia kopii zapasowej i przywracania dla duplikatów baz danych wystąpienia klastra trybu failover, migawek baz danych i baz danych nie są obsługiwane.
- Nie można chronić bazy danych z dużą liczbą plików. Maksymalna liczba obsługiwanych plików nie jest bardzo deterministyczna, ponieważ nie tylko zależy od liczby plików, a także od długości ścieżki plików. Takie przypadki są jednak rzadsze. Obecnie pracujemy nad rozwiązaniem do zastosowania w takiej sytuacji.

Zapoznaj się z [sekcją często zadawanych pytań](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq), aby uzyskać więcej informacji na temat pomocy technicznej/nieobsługiwanych scenariuszy.

## <a name="support-for-azure-geos"></a>Obsługa obszarów geograficznych platformy Azure

Usługa Azure Backup jest obsługiwana w następujących obszarach geograficznych:

- Australia Południowo-Wschodnia (ASE)
- Brazylia Południowa (BRS)
- Kanada Środkowa (CNC)
- Kanada Wschodnia (CE)
- Środkowe stany USA (CUS)
- Azja Wschodnia (EA)
- Australia Wschodnia (AE)
- Wschodnie stany USA (EUS)
- Wschodnie stany USA 2 (EUS2)
- Indie Środkowe (INC)
- Indie Południowe (INS)
- Japonia Wschodnia (JPE)
- Japonia Zachodnia (JPW)
- Korea Środkowa (KRC)
- Korea Południowa (KRS)
- Północno-środkowe stany USA (NCUS)
- Europa Północna (NE)
- Południowo-środkowe stany USA (SCUS)
- Azja Południowo-Wschodnia (SEA)
- Południowe Zjednoczone Królestwo (UKS)
- Zachodnie Zjednoczone Królestwo (UKW)
- Zachodnio-środkowe stany USA (WCUS)
- Europa Zachodnia (WE)
- Zachodnie stany USA (WUS)
- Zachodnie stany USA 2 (WUS 2)

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Obsługa systemów operacyjnych i wersji programu SQL Server

W tej sekcji przedstawiono systemy operacyjne i wersje programu SQL Server obsługiwane przez usługę Azure Backup. Maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server) są obsługiwane.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

System Linux nie jest obecnie obsługiwany.

### <a name="supported-sql-server-versions-and-editions"></a>Obsługiwane wersje i wydania programu SQL Server

- SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem kopii zapasowej bazy danych programu SQL Server sprawdź następujące warunki:

- Zidentyfikuj lub [utwórz magazyn usługi Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) w obrębie tego samego regionu lub ustawień regionalnych jako maszynę wirtualną, która hostuje wystąpienie programu SQL Server.
- [Sprawdź uprawnienia na maszynie wirtualnej](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) wymagane do tworzenia kopii zapasowych baz danych SQL.
- Upewnij się, że [maszyna wirtualna SQL ma łączność sieciową](backup-azure-sql-database.md#establish-network-connectivity).
- Sprawdź, czy bazy danych SQL zostały nazwane zgodnie ze [wskazówkami dotyczącymi nazewnictwa](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) dla usługi Azure Backup, co pozwoli na pomyślne tworzenie kopii zapasowych.

> [!NOTE]
> W danym momencie możesz mieć tylko jedno rozwiązanie do tworzenia kopii zapasowych, które będzie służyć do tworzenia kopii zapasowych baz danych programu SQL Server. Wyłącz wszystkie pozostałe kopie zapasowe SQL przed użyciem tej funkcji; w przeciwnym razie kopie zapasowe będą kolidować i ich tworzenie zakończy się niepowodzeniem. Możesz włączyć usługę Azure Backup dla maszyn wirtualnych usługi IaaS przy użyciu kopii zapasowej SQL bez konfliktów.
>

Jeśli środowisko spełnia te warunki, przejdź do sekcji [Konfigurowanie kopii zapasowej baz danych programu SQL Server](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Jeśli dowolne wymaganie wstępne nie istnieje, czytaj dalej.


## <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Podczas wszystkich operacji maszyna wirtualna SQL musi mieć łączność z publicznymi adresami IP platformy Azure. Operacje maszyny wirtualnej SQL (takie jak odnajdowanie bazy danych, konfigurowanie kopii zapasowych, planowanie tworzenia kopii zapasowych, przywracanie punktów odzyskiwania itd.) nie powiodą się w przypadku braku łączności z publicznymi adresami IP. W celu zapewnienia czytelnej ścieżki ruchu kopii zapasowej należy użyć dowolnej z poniższych opcji:

- Umieszczenie zakresów adresów IP centrów danych platformy Azure na liście dozwolonych: aby umieścić zakresy adresów IP centrów danych platformy Azure na liście dozwolonych, użyj [strony Centrum pobierania w celu uzyskania szczegółowych informacji i instrukcji dotyczących zakresów adresów IP](https://www.microsoft.com/download/details.aspx?id=41653).
- Wdrożenie serwera proxy HTTP na potrzeby kierowania ruchu: gdy tworzysz kopię zapasową bazy danych SQL na maszynie wirtualnej, rozszerzenie kopii zapasowej na maszynie wirtualnej używa interfejsów API protokołu HTTPS do wysyłania poleceń zarządzania do usługi Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej również używa usługi Azure Active Directory (Azure AD) do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Rozszerzenie to jedyny składnik skonfigurowany do uzyskiwania dostępu do publicznego Internetu.

Kompromisy między opcjami dotyczą możliwości zarządzania, szczegółowej kontroli i kosztu.

> [!NOTE]
> Tagi usługi Azure Backup powinny być ogólnie dostępne.
>

| Opcja | Zalety | Wady |
| ------ | ---------- | ------------- |
| Umieszczanie zakresów adresów IP na liście dozwolonych | Brak dodatkowych kosztów. <br/> Aby uzyskać dostęp do operacji otwierania w sieciowej grupie zabezpieczeń, użyj polecenia cmdlet **Set-AzureNetworkSecurityRule**. | Skomplikowane zarządzanie, ponieważ uwzględnione zakresy adresów IP zmieniają się wraz z upływem czasu. <br/>Zapewnia dostęp do całej platformy Azure, nie tylko do usługi Azure Storage.|
| Używanie serwera proxy HTTP   | Na serwerze jest dozwolona szczegółowa kontrola adresów URL magazynu. <br/>Pojedynczy punkt dostępu do maszyn wirtualnych w Internecie. <br/> Nie podlega zmianom adresów IP platformy Azure. | Dodatkowe koszty uruchamiania maszyny wirtualnej przy użyciu oprogramowania serwera proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ustawianie uprawnień maszyn wirtualnych SQL spoza portalu Marketplace

Aby utworzyć kopię zapasową maszyny wirtualnej, usługa Azure Backup wymaga zainstalowania rozszerzenia **AzureBackupWindowsWorkload**. Jeśli korzystasz z maszyn wirtualnych witryny Azure Marketplace, przejdź do sekcji [Odnajdywanie baz danych programu SQL Server](backup-azure-sql-database.md#discover-sql-server-databases). Jeśli maszyna wirtualna hostująca bazy danych SQL nie została utworzona w witrynie Azure Marketplace, należy wykonać następującą procedurę, aby zainstalować rozszerzenie i ustawić odpowiednie uprawnienia. Oprócz rozszerzenia **AzureBackupWindowsWorkload** usługa Azure Backup wymaga uprawnień administratora systemu SQL do ochrony baz danych SQL. Aby odnaleźć bazy danych na maszynie wirtualnej, usługa Azure Backup tworzy konto **NT SERVICE\AzureWLBackupPluginSvc**. To konto służy do tworzenia kopii zapasowych i przywracania oraz musi mieć uprawnienie administratora systemu SQL. Ponadto usługa Azure Backup będzie korzystać z konta **NT AUTHORITY\SYSTEM** na potrzeby odnajdywania/zapytań dotyczących bazy danych, dlatego to konto musi być publicznym logowaniem w środowisku SQL.

Aby skonfigurować uprawnienia:

1. W witrynie [Azure Portal](https://portal.azure.com) otwórz magazyn usługi Recovery Services używany do chronienia baz danych SQL.

2. Na pulpicie nawigacyjnym **magazynu usługi Recovery Services** wybierz pozycję **Utwórz kopię zapasową**. Zostanie otwarte menu **Cel kopii zapasowej**.

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W menu **Cel kopii zapasowej** ustaw pozycję **Gdzie działa Twoje obciążenie** na wartość domyślną: **Azure**.

4. Rozwiń pole listy rozwijanej **Dla których elementów chcesz utworzyć kopię zapasową** i wybierz pozycję **Program SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    W menu **Cel kopii zapasowej** zostaną wyświetlone dwa kroki: **Odnajdywanie baz danych na maszynach wirtualnych** i **Konfigurowanie kopii zapasowej**. Krok **Odnajdywanie baz danych na maszynach wirtualnych** rozpoczyna wyszukiwanie maszyn wirtualnych platformy Azure.

    ![Przeglądanie dwóch kroków z menu Cel kopii zapasowej](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. W obszarze **Odnajdywanie baz danych na maszynach wirtualnych** wybierz pozycję **Uruchom odnajdywanie**, aby wyszukać niechronione maszyny wirtualne w subskrypcji. Wyszukanie wszystkich maszyn wirtualnych może zająć pewien czas. Czas wyszukiwania zależy od liczby niechronionych maszyn wirtualnych w subskrypcji.

    ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Z listy maszyn wirtualnych wybierz maszynę wirtualną z bazą danych SQL do utworzenia kopii zapasowej, a następnie wybierz pozycję **Znajdź bazy danych**.

    Proces odnajdywania instaluje rozszerzenie **AzureBackupWindowsWorkload** na maszynie wirtualnej. Rozszerzenie umożliwia usłudze Azure Backup komunikację z maszyną wirtualną, co pozwala na tworzenie kopii zapasowych baz danych SQL. Po zainstalowaniu rozszerzenia usługa Azure Backup tworzy konto usługi wirtualnej systemu Windows **NT Service\AzureWLBackupPluginSvc** na maszynie wirtualnej. Konto usługi wirtualnej wymaga uprawnienia administratora systemu SQL. Jeśli w procesie instalacji konta usługi wirtualnej wystąpi błąd `UserErrorSQLNoSysadminMembership`, zobacz sekcję [Naprawianie uprawnień administratora systemu SQL](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    Obszar **Powiadomienia** przedstawia postęp odnajdywania baz danych. Ukończenie zadania może potrwać pewien czas. Czas wykonywania zadania zależy od liczby baz danych na maszynie wirtualnej. Po odnalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

Następnym krokiem po skojarzeniu bazy danych z magazynem usługi Recovery Services jest [skonfigurowanie zadania tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Naprawianie uprawnień administratora systemu SQL

Jeśli podczas procesu instalacji wystąpi błąd `UserErrorSQLNoSysadminMembership`, użyj konta z uprawnieniami administratora systemu programu SQL Server, aby zalogować się do programu SQL Server Management Studio (SSMS). Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelnianie systemu Windows powinno działać.

1. W programie SQL Server otwórz folder Security/Logins.

    ![Otwieranie folderu Security/Logins w celu wyświetlenia kont](./media/backup-azure-sql-database/security-login-list.png)

2. Kliknij prawym przyciskiem myszy folder Logins, a następnie wybierz pozycję **Nowa nazwa logowania**. W oknie dialogowym **Nazwa logowania — nowa** wybierz pozycję **Wyszukaj**.

    ![Wybieranie pozycji Wyszukaj w oknie dialogowym Nazwa logowania — nowa](./media/backup-azure-sql-database/new-login-search.png)

3. Konto usługi wirtualnej systemu Windows **NT SERVICE\AzureWLBackupPluginSvc** zostało utworzone podczas rejestracji maszyny wirtualnej i fazy odnajdywania SQL. Wprowadź nazwę konta, jak pokazano w polu **Wprowadź nazwę obiektu do wybrania**. Wybierz pozycję **Sprawdź nazwy** w celu rozpoznania nazwy.

    ![Wybieranie pozycji Sprawdź nazwy w celu rozpoznania nieznanej nazwy](./media/backup-azure-sql-database/check-name.png)

4. Wybierz przycisk **OK**, aby zamknąć okno dialogowe.

5. W oknie **Role serwera** upewnij się, że zaznaczono rolę **sysadmin**. Wybierz przycisk **OK**, aby zamknąć okno dialogowe.

    ![Sprawdzanie, czy wybrano rolę sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Wymagane uprawnienia powinny teraz istnieć.

6. Mimo że naprawiono błąd uprawnień, trzeba skojarzyć bazę danych z magazynem usługi Recovery Services. W witrynie Azure Portal na liście **Serwery chronione** kliknij prawym przyciskiem myszy serwer w stanie błędu i wybierz pozycję **Ponownie znajdź bazy danych**.

    ![Sprawdzanie, czy serwer ma odpowiednie uprawnienia](./media/backup-azure-sql-database/check-erroneous-server.png)

    Obszar **Powiadomienia** przedstawia postęp odnajdywania baz danych. Ukończenie zadania może potrwać pewien czas. Czas wykonywania zadania zależy od liczby baz danych na maszynie wirtualnej. Po znalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

Następnym krokiem po skojarzeniu bazy danych z magazynem usługi Recovery Services jest [skonfigurowanie zadania tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

## <a name="sql-database-naming-guidelines-for-azure-backup"></a>Wskazówki dotyczące nazewnictwa baz danych SQL dla usługi Azure Backup
W celu zapewnienia bezproblemowego tworzenia kopii zapasowych programu SQL Server na maszynie wirtualnej IaaS przy użyciu usługi Azure Backup, należy unikać następujących elementów podczas wybierania nazw baz danych:

  * Spacje końcowe/początkowe
  * Końcowy znak „!”

Oferujemy aliasy nieobsługiwanych znaków tabel platformy Azure, ale zalecamy również ich unikanie. Więcej informacji znajduje się w [tym](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) artykule.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Usługa Azure Backup odnajduje wszystkie bazy danych w wystąpieniu programu SQL Server. Bazy danych można chronić zgodnie z wymaganiami dotyczącymi kopii zapasowych. Poniższa procedura umożliwia zidentyfikowanie maszyny wirtualnej, który jest hostem baz danych SQL. Po zidentyfikowaniu maszyny wirtualnej usługa Azure Backup instaluje uproszczone rozszerzenie uproszczone, aby odnaleźć bazy danych programu SQL Server.

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie pozycji Wszystkie usługi](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. W oknie dialogowym **Wszystkie usługi** wprowadź frazę **Recovery Services**. Podczas pisania dane wejściowe są filtrowane na liście zasobów. Na liście wybierz pozycję **Magazyny usługi Recovery Services**.

  ![Wprowadzanie i wybieranie pozycji Magazyny usługi Recovery Services](./media/backup-azure-sql-database/all-services.png) <br/>

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Na liście magazynów usługi Recovery Services wybierz magazyn do użycia na potrzeby ochrony baz danych SQL.

5. Na pulpicie nawigacyjnym **magazynu usługi Recovery Services** wybierz pozycję **Utwórz kopię zapasową**. Zostanie otwarte menu **Cel kopii zapasowej**.

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

6. W menu **Cel kopii zapasowej** ustaw pozycję **Gdzie działa Twoje obciążenie** na wartość domyślną: **Azure**.

7. Rozwiń pole listy rozwijanej **Dla których elementów chcesz utworzyć kopię zapasową** i wybierz pozycję **Program SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    W menu **Cel kopii zapasowej** zostaną wyświetlone dwa kroki: **Odnajdywanie baz danych na maszynach wirtualnych** i **Konfigurowanie kopii zapasowej**.

    ![Przeglądanie dwóch kroków z menu Cel kopii zapasowej](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. W obszarze **Odnajdywanie baz danych na maszynach wirtualnych** wybierz pozycję **Uruchom odnajdywanie**, aby wyszukać niechronione maszyny wirtualne w subskrypcji. Przeszukiwanie wszystkich maszyn wirtualnych może potrwać pewien czas. Czas wyszukiwania zależy od liczby niechronionych maszyn wirtualnych w subskrypcji.

    ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)

    Odnaleziona niechroniona maszyna wirtualna pojawi się na liście. Wiele maszyn wirtualnych może mieć tę samą nazwę. Jednak maszyny wirtualne o tej samej nazwie należą do różnych grup zasobów. Niechronione maszyny wirtualne są wyświetlane według nazwy maszyny wirtualnej i grupy zasobów. Jeśli oczekiwanej maszyny wirtualnej nie ma na liście, zobacz, czy ta maszyna jest już chroniona w magazynie.

9. Z listy maszyn wirtualnych wybierz maszynę wirtualną z bazą danych SQL do utworzenia kopii zapasowej, a następnie wybierz pozycję **Znajdź bazy danych**.

    Usługa Azure Backup odnajduje wszystkie bazy danych SQL na maszynie wirtualnej. Aby dowiedzieć się, co dzieje się w trakcie fazy odnajdywania bazy danych, zobacz sekcję [Operacje w tle](backup-azure-sql-database.md#background-operations). Po odnalezieniu baz danych SQL możesz rozpocząć [konfigurowanie zadania tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Operacje w tle

Jeśli używasz narzędzia **Znajdź bazy danych**, usługa Azure Backup wykonuje następujące operacje w tle:

- Zarejestrowanie maszyny wirtualnej przy użyciu magazynu usługi Recovery Services na potrzeby tworzenia kopii zapasowych obciążeń. Kopie zapasowe wszystkich baz danych na zarejestrowanej maszynie wirtualnej można tworzyć tylko w tym magazynie usługi Recovery Services.

- Zainstalowanie rozszerzenia **AzureBackupWindowsWorkload** na maszynie wirtualnej. Tworzenie kopii zapasowej bazy danych SQL to rozwiązanie nieużywające agentów. To rozszerzenie jest instalowane na maszynie wirtualnej, a w bazie danych SQL nie są instalowani żadni agenci.

- Utworzenie konta usługi **NT Service\AzureWLBackupPluginSvc** na maszynie wirtualnej. Wszystkie operacje tworzenia kopii zapasowych i przywracania korzystają z konta usługi. Usługa **NT Service\AzureWLBackupPluginSvc** musi mieć uprawnienia administratora systemu SQL. Wszystkie maszyny wirtualne SQL Marketplace maja zainstalowane rozszerzenie **SqlIaaSExtension**. Rozszerzenie **AzureBackupWindowsWorkload** używa rozszerzenia **SQLIaaSExtension** w celu automatycznego uzyskania wymaganych uprawnień. Jeśli maszyna wirtualna nie ma zainstalowanego rozszerzenia **SqlIaaSExtension**, operacja odnajdywania baz danych kończy się niepowodzeniem z komunikatem o błędzie `UserErrorSQLNoSysAdminMembership`. Aby dodać uprawnienia administratora systemu do tworzenia kopii zapasowej, postępuj zgodnie z instrukcjami w sekcji [Konfigurowanie uprawnień usługi Azure Backup dla maszyn wirtualnych SQL spoza witryny Marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Wybieranie maszyny wirtualnej i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Konfigurowanie tworzenia kopii zapasowych baz danych programu SQL Server

Usługa Azure Backup udostępnia usługi zarządzania służące do ochrony baz danych programu SQL Server i zarządzania zadaniami tworzenia kopii zapasowej. Funkcje zarządzania i monitorowania zależą od magazynu usługi Recovery Services.

> [!NOTE]
> W danym momencie możesz mieć tylko jedno rozwiązanie do tworzenia kopii zapasowych, które będzie służyć do tworzenia kopii zapasowych baz danych programu SQL Server. Wyłącz wszystkie pozostałe kopie zapasowe SQL przed użyciem tej funkcji; w przeciwnym razie kopie zapasowe będą kolidować i ich tworzenie zakończy się niepowodzeniem. Możesz włączyć usługę Azure Backup dla maszyn wirtualnych usługi IaaS przy użyciu kopii zapasowej SQL bez konfliktów.
>

Aby skonfigurować ochronę dla bazy danych SQL:

1. Otwórz magazyn usługi Recovery Services, który został zarejestrowany na maszynie wirtualnej SQL.

2. Na pulpicie nawigacyjnym **magazynu usługi Recovery Services** wybierz pozycję **Utwórz kopię zapasową**. Zostanie otwarte menu **Cel kopii zapasowej**.

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W menu **Cel kopii zapasowej** ustaw pozycję **Gdzie działa Twoje obciążenie** na wartość domyślną: **Azure**.

4. Rozwiń pole listy rozwijanej **Dla których elementów chcesz utworzyć kopię zapasową** i wybierz pozycję **Program SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    W menu **Cel kopii zapasowej** zostaną wyświetlone dwa kroki: **Odnajdywanie baz danych na maszynach wirtualnych** i **Konfigurowanie kopii zapasowej**.

    Jeśli czynności opisane w tym artykule zostały wykonane w podanej kolejności, odnaleziono niechronione maszyny wirtualne, a magazyn został zarejestrowany przy użyciu maszyny wirtualnej. Teraz możesz przystąpić do konfigurowania ochrony baz danych SQL.

5. Z menu **Cel kopii zapasowej** wybierz pozycję **Konfiguruj kopię zapasową**.

    ![Wybieranie pozycji Konfiguruj kopię zapasową](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Usługa Azure Backup wyświetla wszystkie wystąpienia programu SQL Server z autonomicznymi bazami danych i zawsze włączonym grupami dostępności programu SQL Server. Aby wyświetlić autonomiczne bazy danych w wystąpieniu programu SQL Server, wybierz podwójną strzałkę z lewej strony nazwy wystąpienia. W podobny sposób wybierz podwójną strzałkę z lewej strony zawsze włączonej grupy dostępności, aby wyświetlić listę baz danych. Poniższa ilustracja przedstawia przykład wystąpienia autonomicznego i zawsze włączonej grupy dostępności.

      ![Wyświetlanie wszystkich wystąpień programu SQL Server z autonomicznymi bazami danych](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Na liście baz danych wybierz wszystkie bazy danych, które chcesz chronić, a następnie kliknij przycisk **OK**.

    ![Chronienie bazy danych](./media/backup-azure-sql-database/select-database-to-protect.png)

    W danym momencie możesz wybrać maksymalnie 50 baz danych. Aby chronić więcej niż 50 baz danych, należy wykonać kilka przebiegów. Po objęciu ochroną 50 pierwszych baz danych powtórz ten krok, aby chronić następny zestaw baz danych.

    > [!Note]
    > W celu zoptymalizowania obciążeń kopii zapasowych usługa Azure Backup dzieli duże zadania tworzenia kopii zapasowej na wiele partii. Maksymalna liczba baz danych w jednym zadaniu tworzenia kopii zapasowej to 50.
    >

      Możesz również włączyć [automatyczną ochronę](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) całego wystąpienia lub zawsze włączonej grupy dostępności, wybierając opcję **WŁ.** na odpowiedniej liście rozwijanej w kolumnie **AUTOMATYCZNA OCHRONA**. Funkcja [automatycznej ochrony](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) nie tylko równocześnie włącza ochronę wszystkich istniejących baz danych, ale również automatycznie chroni wszystkie nowe bazy danych, które w przyszłości zostaną dodane do wystąpienia lub grupy dostępności.  

      ![Włączanie automatycznej ochrony zawsze włączonej grupy dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

7. Aby utworzyć lub wybrać zasady kopii zapasowych, z menu **Kopia zapasowa** wybierz pozycję **Zasady kopii zapasowych**. Zostanie otwarte menu **Zasady kopii zapasowych**.

    ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-azure-sql-database/select-backup-policy.png)

8. Z listy rozwijanej **Wybierz zasady tworzenia kopii zapasowej** wybierz zasady kopii zapasowych, a następnie wybierz przycisk **OK**. Aby uzyskać informacje na temat tworzenia zasad kopii zapasowych, zobacz [Definiowanie zasad kopii zapasowych](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > W okresie obowiązywania wersji zapoznawczej nie możesz edytować zasad kopii zapasowych. Jeśli potrzebujesz zasad spoza listy, musisz je utworzyć. Aby uzyskać informacje na temat tworzenia nowych zasad kopii zapasowych, zobacz sekcję [Definiowanie zasad kopii zapasowych](backup-azure-sql-database.md#define-a-backup-policy).

    ![Wybieranie zasad kopii zapasowych z listy](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Z menu **Zasady kopii zapasowych** w polu listy rozwijanej **Wybierz zasady tworzenia kopii zapasowej** możesz:
    - Wybrać zasady domyślne: **HourlyLogBackup**.
    - Wybrać istniejące zasady kopii zapasowych utworzone wcześniej dla środowiska SQL.
    - [Zdefiniować nowe zasady](backup-azure-sql-database.md#define-a-backup-policy) oparte na własnym celu punktu odzyskiwania i zakresie przechowywania.

    > [!Note]
    > Usługa Azure Backup obsługuje długoterminowe przechowywanie, które opiera się na schemacie tworzenia kopii zapasowej typu „dziadek-ojciec-syn”. Schemat optymalizuje wykorzystanie magazynu zaplecza, spełniając równocześnie potrzeby w zakresie zachowania zgodności.
    >

9. Po wybraniu zasad kopii zapasowych z menu **Kopia zapasowa** wybierz pozycję **Włącz kopię zapasową**.

    ![Włączanie wybranych zasad kopii zapasowych](./media/backup-azure-sql-database/enable-backup-button.png)

    Śledź postęp konfiguracji w obszarze **Powiadomienia** portalu.

    ![Obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)


## <a name="auto-protect-sql-server-in-azure-vm"></a>Automatyczna ochrona programu SQL Server na maszynie wirtualnej platformy Azure  

Automatyczna ochrona umożliwia automatyczne chronienie wszystkich istniejących bazy danych i baz danych, które zostaną dodane w przyszłości do autonomicznego wystąpienia programu SQL Server lub zawsze włączonej grupy dostępności programu SQL Server. Włączenie automatycznej ochrony (ustawienie pozycji **WŁ.**) i wybranie zasad kopii zapasowych będzie stosowane w przypadku nowo chronionych baz danych, natomiast istniejące chronione bazy danych będą w dalszym ciągu używać poprzednich zasad.

![Włączanie automatycznej ochrony zawsze włączonej grupy dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

Nie ma limitu liczby baz danych wybieranych jednocześnie za pomocą funkcji automatycznej ochrony. Konfigurowanie tworzenia kopii zapasowej jest wyzwalane dla wszystkich baz danych razem i można je śledzić w obszarze **Zadania kopii zapasowej**.

Jeśli z jakiegoś powodu musisz wyłączyć automatyczną ochronę wystąpienia, kliknij nazwę wystąpienia w obszarze **Konfigurowanie kopii zapasowej**, aby po prawej stronie otworzyć panel informacyjny z obszarem **wyłączania automatycznej ochrony** w górnej części. Kliknij pozycję **Wyłącz automatyczną ochronę**, aby wyłączyć automatyczną ochronę wystąpienia.

![Wyłączanie automatycznej ochrony wystąpienia](./media/backup-azure-sql-database/disable-auto-protection.png)

Wszystkie bazy danych w wystąpieniu będą w dalszym ciągu chronione. Jednak ta akcja spowoduje wyłączenie automatycznej ochrony wszystkich baz danych, które zostaną dodane w przyszłości.

### <a name="define-a-backup-policy"></a>Definiowanie zasad tworzenia kopii zapasowych

Zasady kopii zapasowych określają macierz z informacjami, kiedy są tworzone kopie zapasowe i jak długo są przechowywane. Użyj usługi Azure Backup, aby zaplanować tworzenie trzech typów kopii zapasowych baz danych SQL:

* Pełna kopia zapasowa: pełna kopia zapasowa bazy danych powoduje utworzenie kopii zapasowej całej bazy danych. Pełna kopia zapasowa zawiera wszystkie dane w wybranej bazie danych lub zestawie grup plików albo plików oraz wystarczającą liczbę dzienników do odzyskania danych. Można wyzwalać maksymalnie jedną pełną kopię zapasową dziennie. Pełną kopię zapasową można wykonywać z interwałem dziennym lub tygodniowym.
* Różnicowa kopia zapasowa: różnicowa kopia zapasowa jest oparta na najnowszej, poprzedniej pełnej kopii zapasowej. Różnicowa kopia zapasowa przechwytuje tylko dane, które uległy zmianie od czasu utworzenia pełnej kopii zapasowej. Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie. Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej w tym samym dniu.
* Kopia zapasowa dziennika transakcji: kopia zapasowa dziennika umożliwia przywrócenie do określonego punktu w czasie z dokładnością do sekundy. Maksymalnie można skonfigurować kopie zapasowych dziennika transakcji co 15 minut.

Zasady są tworzone na poziomie magazynu usługi Recovery Services. Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych. Po utworzeniu zasad kopii zapasowych ustawieniem domyślnym będzie pełna kopia zapasowa tworzona codziennie. Możesz dodać różnicową kopię zapasową, ale tylko jeśli skonfigurujesz tworzenie pełnych kopii zapasowych co tydzień. Poniższa procedura wyjaśnia sposób tworzenia zasad kopii zapasowych na potrzeby wystąpienia programu SQL Server na maszynie wirtualnej platformy Azure.

> [!NOTE]
> W wersji zapoznawczej nie można edytować zasad kopii zapasowych. Zamiast tego należy utworzyć nowe zasady, używając żądanych szczegółów.  

Aby utworzyć nowe zasady kopii zapasowych:

1. W magazynie usługi Recovery Services, który chroni bazy danych SQL, kliknij pozycję **Zasady dotyczące kopii zapasowych**, a następnie kliknij przycisk **Dodaj**.

   ![Otwieranie okna dialogowego Tworzenie nowych zasad kopii zapasowych](./media/backup-azure-sql-database/new-policy-workflow.png)

   Zostanie wyświetlone menu **Dodaj**.

2. W menu **Dodawanie** kliknij polecenie **Program SQL Server na maszynie wirtualnej platformy Azure**.

   ![Wybieranie typu nowych zasad kopii zapasowych](./media/backup-azure-sql-database/policy-type-details.png)

   Wybranie programu SQL Server na maszynie wirtualnej platformy Azure spowoduje zdefiniowanie typu zasad i otwarcie menu zasad kopii zapasowych. Menu **Zasady kopii zapasowych** zawiera pola, które są niezbędne w przypadku nowych zasad tworzenia kopii zapasowych programu SQL Server.

3. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.

4. Pełna kopia zapasowa jest obowiązkowa. Opcji **Pełna kopia zapasowa** nie można wyłączyć. Kliknij pozycję **Pełna kopia zapasowa**, aby wyświetlić i edytować zasady. Nawet jeśli nie zmieniasz zasad kopii zapasowych, warto wyświetlić szczegóły zasad.

    ![Pola nowych zasad kopii zapasowych](./media/backup-azure-sql-database/full-backup-policy.png)

    W menu **zasad pełnej kopii zapasowej** w obszarze **Częstotliwość tworzenia kopii zapasowej** wybierz pozycję **Codziennie** lub **Co tydzień**. W przypadku opcji **Codziennie** wybierz godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej. Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.

   ![Ustawienie interwału Codziennie](./media/backup-azure-sql-database/daily-interval.png)

    W przypadku opcji **Co tydzień** wybierz dzień tygodnia, godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.

   ![Ustawienie interwału Co tydzień](./media/backup-azure-sql-database/weekly-interval.png)

5. Domyślnie wszystkie opcje obszaru **Zakres przechowywania** są zaznaczone: codziennie, co tydzień, co miesiąc i co rok. Usuń zaznaczenie wszystkich niepotrzebnych limitów zakresu przechowywania. Ustaw interwały do użycia. W menu **zasad pełnej kopii zapasowej** wybierz pozycję **OK**, aby zaakceptować ustawienia.

   ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

    Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa. Kopia zapasowa dla określonego dnia jest oznaczana i przechowywana w oparciu o tygodniowy zakres przechowywania oraz ustawienie przechowywania co tydzień. Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

6. Aby dodać zasady tworzenia różnicowej kopii zapasowej, wybierz pozycję **Różnicowa kopia zapasowa**. Zostanie otwarte menu **zasad różnicowej kopii zapasowej**.

   ![Otwieranie menu zasad różnicowej kopii zapasowej](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    W menu **zasad różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania. Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.

    > [!Important]
    > Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.
    >

   ![Edytowanie zasad różnicowej kopii zapasowej](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

7. Aby dodać zasady kopii zapasowej dziennika transakcji, wybierz pozycję **Kopia zapasowa dziennika**. Zostanie otwarte menu **Kopia zapasowa dziennika**.

    W menu **Kopia zapasowa dziennika** wybierz pozycję **Włącz**, a następnie ustaw kontrolki częstotliwości i przechowywania. Kopie zapasowe dziennika można tworzyć nawet co 15 minut. Można je również zachowywać przez maksymalnie 35 dni. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

   ![Edytowanie zasad kopii zapasowej dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. W menu **Zasady kopii zapasowych** wybierz, czy chcesz włączyć **kompresję kopii zapasowej SQL**. Kompresja jest domyślnie wyłączona.

    Na zapleczu usługa Azure Backup używa natywnej kompresji kopii zapasowej SQL.

9. Po zakończeniu edycji zasad kopii zapasowych wybierz pozycję **OK**.

   ![Akceptowanie nowych zasad kopii zapasowych](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Przywracanie bazy danych SQL
Usługa Azure Backup oferuje funkcję służącą do przywracania pojedynczych baz danych do określonej daty lub godziny (z dokładnością co do sekundy) przy użyciu kopii zapasowych dziennika transakcji. Usługa Azure Backup automatycznie określa odpowiednie pełne różnicowe kopie zapasowe oraz łańcuch kopii zapasowych dzienników, które są wymagane do przywrócenia danych w oparciu o wybrane czasy przywracania.

Możesz również wybrać określoną pełną lub różnicową kopię zapasową w celu przywrócenia do określonego punktu odzyskiwania, a nie do określonej godziny.

### <a name="pre-requisite-before-triggering-a-restore"></a>Warunek wstępny przed wyzwoleniem przywracania

1. Można przywrócić bazę danych do wystąpienia programu SQL Server w tym samym regionie platformy Azure. Serwer docelowy musi zostać zarejestrowany w tym samym magazynie usługi Recovery Services co źródło.  
2. Aby przywrócić bazę danych zaszyfrowaną przy użyciu technologii TDE do innego programu SQL Server, najpierw przywróć certyfikat na serwerze docelowym, wykonując kroki opisane [tutaj](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Przed wyzwoleniem przywracania bazy danych „master” uruchom wystąpienie programu SQL Server w trybie jednego użytkownika z opcją uruchamiania `-m AzureWorkloadBackup`. Argument opcji `-m` jest nazwą klienta. Tylko ten klient może otworzyć połączenie. W przypadku wszystkich systemowych baz danych (model, master, msdb) należy zatrzymać usługę agenta SQL przed wyzwoleniem przywracania. Zamknij wszystkie aplikacje, które mogą spróbować wykraść połączenie z dowolną z tych baz danych.

### <a name="steps-to-restore-a-database"></a>Kroki przywracania bazy danych:

1. Otwórz magazyn usługi Recovery Services, który został zarejestrowany na maszynie wirtualnej SQL.

2. Na pulpicie nawigacyjnym **Magazyn usługi Recovery Services** w obszarze **Użycie** wybierz pozycję **Elementy kopii zapasowej**, aby otworzyć menu **Elementy kopii zapasowej**.

    ![Otwieranie menu Elementy kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Z menu **Elementy kopii zapasowej** w obszarze **Typ zarządzania kopiami zapasowymi** wybierz pozycję **SQL na maszynie wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Menu **Elementy kopii zapasowej** przedstawia listę baz danych SQL.

4. Na liście baz danych SQL wybierz bazę danych do przywrócenia.

    ![Wybieranie bazy danych do przywrócenia](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych zostanie otwarte jej menu. Menu udostępnia szczegóły kopii zapasowej bazy danych, w tym:

    * Najstarsze i najnowsze punkty przywracania.
    * Stan kopii zapasowej dziennika w ciągu ostatnich 24 godzin (w przypadku baz danych w modelu odzyskiwania pełnego i z niepełnym dziennikiem, jeśli został skonfigurowany dla kopii zapasowych dziennika transakcji).

5. W menu wybranej bazy danych wybierz pozycję **Przywróć bazę danych**. Zostanie otwarte menu **Przywracanie**.

    ![Wybieranie pozycji Przywróć bazę danych](./media/backup-azure-sql-database/restore-db-button.png)

    Po otwarciu menu **Przywracanie** zostanie również otwarte menu **Konfiguracja przywracania**. Menu **Konfiguracja przywracania** jest pierwszym krokiem do skonfigurowania przywracania. Użyj tego menu, aby wybrać lokalizację przywracania danych. Dostępne opcje to:
    - **Lokalizacja alternatywna**: przywróć bazę danych do lokalizacji alternatywnej i zachowaj oryginalną źródłową bazę danych.
    - **Zastąp bazę danych**: przywróć dane do tego samego wystąpienia programu SQL Server co oryginalne źródło. Ta opcja powoduje zastąpienie oryginalnej bazy danych.

    > [!Important]
    > Jeśli wybrana baza danych należy do zawsze włączonej grupy dostępności, program SQL Server nie zezwala na zastąpienie bazy danych. W tym przypadku jest włączona tylko opcja **Lokalizacja alternatywna**.
    >

    ![Menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

Ta procedura przeprowadza Cię przez przywracanie danych do lokalizacji alternatywnej. Aby zastąpić bazę danych podczas przywracania, przejdź do sekcji [Przywracanie i zastępowanie bazy danych](backup-azure-sql-database.md#restore-and-overwrite-the-database). Na tym etapie magazyn usługi Recovery Services jest otwarty, a menu **Konfiguracja przywracania** jest widoczne. Jeśli nie jesteś na tym etapie, rozpocznij od [przywrócenia bazy danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Można przywrócić bazę danych do wystąpienia programu SQL Server w tym samym regionie platformy Azure. Serwer docelowy musi zostać zarejestrowany w magazynie usługi Recovery Services.
>

W menu **Konfiguracja przywracania** w polu listy rozwijanej **Serwer** znajdują się tylko wystąpienia programu SQL Server, które zostały zarejestrowane w magazynie usługi Recovery Services. Jeśli lista nie zawiera potrzebnego serwera, zapoznaj się z sekcją [Odnajdywanie baz danych programu SQL Server](backup-azure-sql-database.md#discover-sql-server-databases), aby znaleźć serwer. Podczas procesu odnajdywania nowe serwery są rejestrowane w magazynie usługi Recovery Services.

1. W menu **Konfiguracja przywracania**:

    * W obszarze **Miejsce docelowe przywracania** wybierz pozycję **Lokalizacja alternatywna**.
    * Otwórz pole listy rozwijanej **Serwer**, a następnie wybierz wystąpienie programu SQL Server, aby przywrócić bazę danych.
    * Otwórz pole listy rozwijanej **Wystąpienie**, a następnie wybierz wystąpienie programu SQL Server.
    * W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.
    * Jeśli ma to zastosowanie, wybierz pozycję **Zastąp, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu SQL**.
    * Wybierz przycisk **OK**, aby ukończyć konfigurację miejsca docelowego, i kontynuuj wybieranie punktu przywracania.

    ![Podawanie wartości menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. W menu **Wybierz punkt przywracania** wybierz pozycję **Dzienniki (punkt w czasie)** lub **Pełne i różnicowe** jako punkt przywracania. Aby przywrócić do dziennika w określonym punkcie w czasie, kontynuuj wykonywanie tego kroku. Aby przywrócić pełny i różnicowy punkt przywracania, przejdź do kroku 3.

    ![Menu wybierania punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Przywracanie do punktu w czasie jest dostępne tylko w przypadku kopii zapasowych dzienników dla baz danych z modelem odzyskiwania pełnego i z niepełnym dziennikiem. Aby przywrócić do określonego punktu w czasie:

    1. Wybierz pozycję **Dzienniki (punkt w czasie)** jako typ przywracania.

        ![Wybieranie typu punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **Data/godzina przywracania**, wybierz mini kalendarz, aby otworzyć **kalendarz**. W **kalendarzu** pogrubione daty mają punkty odzyskiwania, a bieżąca data jest wyróżniona. Wybierz datę z punktami odzyskiwania. Nie można wybrać dat bez punktów odzyskiwania.

        ![Otwieranie kalendarza](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu daty wykres osi czasu zawiera dostępne punkty odzyskiwania w ciągłym zakresie.

    3. Użyj wykresu osi czasu lub okna dialogowego **Czas**, aby określić godzinę dla punktu odzyskiwania. Wybierz przycisk **OK**, aby ukończyć krok tworzenia punktu przywracania.

       ![Otwieranie kalendarza](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Menu **Wybierz punkt przywracania** zostanie zamknięte i pojawi się menu **Konfiguracja zaawansowana**.

       ![Menu Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. W menu **Konfiguracja zaawansowana**:

        * Aby zachować bazę danych jako niedziałającą po przywróceniu, ustaw pozycję **Przywróć przy użyciu opcji NORECOVERY** na **Włączone**.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w kolumnie **Cel**.
        * Wybierz przycisk **OK**, aby zatwierdzić ustawienia. Zamknij menu **Konfiguracja zaawansowana**.

    5. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania. Śledź postęp przywracania w obszarze **Powiadomienia** lub wybierz pozycję **Zadania przywracania** w menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

3. W menu **Wybierz punkt przywracania** wybierz pozycję **Dzienniki (punkt w czasie)** lub **Pełne i różnicowe** jako punkt przywracania. Aby przywrócić dziennik do punktu w czasie, wróć do kroku 2. Ten krok powoduje przywrócenie do pełnego lub różnicowego punktu przywracania. Można wyświetlić wszystkie punkty odzyskiwania pełnego i różnicowego z ostatnich 30 dni. Aby wyświetlić punkty odzyskiwania starsze niż 30 dni, wybierz pozycję **Filtr**, aby otworzyć menu **Filtrowanie punktów przywracania**. W przypadku punktu odzyskiwania różnicowego usługa Azure Backup najpierw przywraca do odpowiedniego punktu pełnego odzyskiwania, a następnie stosuje wybrany punkt odzyskiwania różnicowego.

    ![Menu wybierania punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W menu **Wybierz punkt przywracania** wybierz pozycję **Pełne i różnicowe**.

       ![Wybieranie pozycji Pełne i różnicowe](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        W menu pojawia się lista dostępnych punktów odzyskiwania.

    2. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK**, aby ukończyć procedurę punktu przywracania.

        ![Wybieranie punktu odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Menu **Punkt przywracania** zostanie zamknięte i pojawi się menu **Konfiguracja zaawansowana**.

        ![Meny Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. W menu **Konfiguracja zaawansowana**:

        * Aby zachować bazę danych jako niedziałającą po przywróceniu, ustaw pozycję **Przywróć przy użyciu opcji NORECOVERY** na **Włączone**. Pozycja **Przywróć przy użyciu opcji NORECOVERY** jest domyślnie wyłączona.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w kolumnie **Cel**.
        * Wybierz przycisk **OK**, aby zatwierdzić ustawienia. Zamknij menu **Konfiguracja zaawansowana**.

    4. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania. Śledź postęp przywracania w obszarze **Powiadomienia** lub wybierz pozycję **Zadania przywracania** w menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Przywracanie i zastępowanie bazy danych

Ta procedura przeprowadza Cię przez przywracanie danych i zastępowanie bazy danych. Aby przywrócić do innej lokalizacji, przejdź do sekcji [Przywracanie do lokalizacji alternatywnej](backup-azure-sql-database.md#restore-to-an-alternate-location). Na tym etapie magazyn usługi Recovery Services jest otwarty, a menu **Konfiguracja przywracania** jest widoczna (zobacz poniższy obraz). Jeśli nie jesteś na tym etapie, rozpocznij od [przywrócenia bazy danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-overwrite-db.png)

W menu **Konfiguracja przywracania** w polu listy rozwijanej **Serwer** znajdują się tylko wystąpienia programu SQL Server, które zostały zarejestrowane w magazynie usługi Recovery Services. Jeśli lista nie zawiera potrzebnego serwera, zapoznaj się z sekcją [Odnajdywanie baz danych programu SQL Server](backup-azure-sql-database.md#discover-sql-server-databases), aby znaleźć serwer. Podczas procesu odnajdywania nowe serwery są rejestrowane w magazynie usługi Recovery Services.

1. W menu **Konfiguracja przywracania** wybierz pozycję **Zastąp bazę danych**, a następnie wybierz pozycję **OK**, aby ukończyć konfigurację miejsca docelowego.

   ![Wybieranie pozycji Zastąp bazę danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Ustawienia **Serwer**, **Wystąpienie** i **Nazwa przywróconej bazy danych** nie są potrzebne.

2. W menu **Wybierz punkt przywracania** wybierz pozycję **Dzienniki (punkt w czasie)** lub **Pełne i różnicowe** jako punkt przywracania. Aby przywrócić do dziennika w określonym punkcie w czasie, kontynuuj wykonywanie tego kroku. Aby przywrócić pełny i różnicowy punkt przywracania, przejdź do kroku 3.

    ![menu wybierania punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Przywracanie do punktu w czasie jest dostępne tylko w przypadku kopii zapasowych dzienników dla baz danych z modelem odzyskiwania pełnego i z niepełnym dziennikiem. Aby przywrócić do określonej sekundy:

    1. Wybierz pozycję **Dzienniki (punkt w czasie)** jako punkt przywracania.

        ![Wybieranie typu punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **Data/godzina przywracania**, wybierz mini kalendarz, aby otworzyć **kalendarz**. W **kalendarzu** pogrubione daty mają punkty odzyskiwania, a bieżąca data jest wyróżniona. Wybierz datę z punktami odzyskiwania. Nie można wybrać dat bez punktów odzyskiwania.

        ![Otwieranie kalendarza](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu daty wykres osi czasu zawiera dostępne punkty odzyskiwania.

    3. Użyj wykresu osi czasu lub okna dialogowego **Czas**, aby określić godzinę dla punktu odzyskiwania. Wybierz przycisk **OK**, aby ukończyć krok tworzenia punktu przywracania.

       ![Otwieranie kalendarza](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Menu **Wybierz punkt przywracania** zostanie zamknięte i pojawi się menu **Konfiguracja zaawansowana**.

       ![Menu Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. W menu **Konfiguracja zaawansowana**:

        * Aby zachować bazę danych jako niedziałającą po przywróceniu, ustaw pozycję **Przywróć przy użyciu opcji NORECOVERY** na **Włączone**.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w kolumnie **Cel**.
        * Wybierz przycisk **OK**, aby zatwierdzić ustawienia. Zamknij menu **Konfiguracja zaawansowana**.

    5. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania. Śledź postęp przywracania w obszarze **Powiadomienia** lub wybierz pozycję **Zadania przywracania** w menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

3. W menu **Wybierz punkt przywracania** wybierz pozycję **Dzienniki (punkt w czasie)** lub **Pełne i różnicowe** jako punkt przywracania. Aby przywrócić dziennik do punktu w czasie, wróć do kroku 2. Ten krok powoduje przywrócenie do pełnego lub różnicowego punktu przywracania. Można wyświetlić wszystkie punkty odzyskiwania pełnego i różnicowego z ostatnich 30 dni. Aby wyświetlić punkty odzyskiwania starsze niż 30 dni, wybierz pozycję **Filtr**, aby otworzyć menu **Filtrowanie punktów przywracania**. W przypadku punktu odzyskiwania różnicowego usługa Azure Backup najpierw przywraca do odpowiedniego punktu pełnego odzyskiwania, a następnie stosuje wybrany punkt odzyskiwania różnicowego.

    ![Menu wybierania punktu przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W menu **Wybierz punkt przywracania** wybierz pozycję **Pełne i różnicowe**.

       ![Wybieranie pozycji Pełne i różnicowe](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        W menu pojawia się lista dostępnych punktów odzyskiwania.

    2. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK**, aby ukończyć procedurę punktu przywracania.

        ![Wybieranie punktu odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Menu **Punkt przywracania** zostanie zamknięte i pojawi się menu **Konfiguracja zaawansowana**.

        ![Meny Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. W menu **Konfiguracja zaawansowana**:

        * Aby zachować bazę danych jako niedziałającą po przywróceniu, ustaw pozycję **Przywróć przy użyciu opcji NORECOVERY** na **Włączone**. Pozycja **Przywróć przy użyciu opcji NORECOVERY** jest domyślnie wyłączona.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w kolumnie **Cel**.
        * Wybierz przycisk **OK**, aby zatwierdzić ustawienia. Zamknij menu **Konfiguracja zaawansowana**.

    4. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania. Śledź postęp przywracania w obszarze **Powiadomienia** lub wybierając pozycję **Zadania przywracania** w menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Zarządzanie operacjami usługi Azure Backup dla środowiska SQL na maszynach wirtualnych platformy Azure

Ta sekcja zawiera informacje na temat różnych operacji zarządzania usługi Azure Backup, które są dostępne w środowisku SQL na maszynach wirtualnych platformy Azure. Istnieją następujące operacje wysokiego poziomu:

* Monitorowanie zadań
* Alerty kopii zapasowej
* Zatrzymywanie ochrony bazy danych SQL
* Wznawianie ochrony bazy danych SQL
* Wyzwalanie zadania tworzenia kopii zapasowej ad hoc
* Wyrejestrowywanie serwera z uruchomionym programem SQL Server

### <a name="monitor-backup-jobs"></a>Monitorowanie zadań kopii zapasowej
Usługa Azure Backup to rozwiązanie klasy korporacyjnej, które w przypadku wystąpienia błędów oferuje zaawansowane alerty i powiadomienia dotyczące kopii zapasowych. (Zobacz sekcję [Wyświetlanie alertów dotyczących kopii zapasowych](backup-azure-sql-database.md#view-backup-alerts)). Aby monitorować określone zadania, można użyć dowolnej z poniższych opcji zgodnie z wymaganiami.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Korzystanie z witryny Azure Portal w przypadku operacji ad hoc
Usługa Azure Backup wyświetla wszystkie zadania wyzwalane ręcznie lub ad hoc w portalu **Zadania kopii zapasowej**. Zadania, które są dostępne w portalu **Zadania kopii zapasowej**, obejmują:
- Wszystkie operacje konfigurowania tworzenia kopii zapasowych.
- Ręcznie wyzwalane operacje tworzenia kopii zapasowych.
- Operacje przywracania.
- Operacje rejestracji i odnajdywania baz danych.
- Zatrzymywanie operacji tworzenia kopii zapasowych.

![Portal zadań kopii zapasowej](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Nie wszystkie zaplanowane zadania kopii zapasowej, w tym kopii zapasowej pełnej, różnicowej i dziennika, są wyświetlane w portalu **Zadania kopii zapasowej**. Do monitorowania zaplanowanych zadań kopii zapasowych należy użyć programu SQL Server Management Studio zgodnie z opisem w następnej sekcji.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Używanie programu SQL Server Management Studio na potrzeby zadań kopii zapasowej
Usługa Azure Backup używa natywnych interfejsów API SQL na potrzeby wszystkich operacji dotyczących kopii zapasowych. Natywnych interfejsów API należy użyć do pobrania wszystkich informacji o zadaniu z [tabeli zestawu kopii zapasowych SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) w bazie danych msdb.

Poniższy przykład to zapytanie, które pobiera wszystkie zadania kopii zapasowej dla bazy danych o nazwie **DB1**. Dostosowywanie zapytania na potrzeby monitorowania zaawansowanego.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

### <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Ponieważ kopie zapasowe dziennika są tworzone co 15 minut, od czasu do czasu monitorowanie zadań kopii zapasowej może być żmudne. Usługa Azure Backup oferuje pomoc w takiej sytuacji. Alerty dotyczące poczty e-mail są wyzwalane w przypadku wszystkich błędów kopii zapasowych. Alerty są konsolidowane na poziomie bazy danych według kodu błędu. Alerty e-mail są wysyłane tylko w przypadku pierwszego niepowodzenia tworzenia kopii zapasowej bazy danych. Zaloguj się do witryny Azure Portal w celu monitorowania wszystkich błędów bazy danych.

Aby monitorować alerty dotyczące kopii zapasowej:

1. Zaloguj się do subskrypcji platformy Azure w witrynie [Azure Portal](https://portal.azure.com).

2. Otwórz magazyn usługi Recovery Services, który został zarejestrowany na maszynie wirtualnej SQL.

3. Na pulpicie nawigacyjnym **magazynu usługi Recovery Services** wybierz pozycję **Alerty i zdarzenia**.

   ![Wybieranie pozycji Alerty i zdarzenia](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Z menu **Alerty i zdarzenia** wybierz pozycję **Alerty kopii zapasowej** w celu wyświetlenia listy alertów.

   ![Wybieranie pozycji Alerty kopii zapasowej](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Zatrzymywanie ochrony bazy danych programu SQL Server

W przypadku zatrzymywania ochrony bazy danych programu SQL Server usługa Azure Backup żąda decyzji dotyczącej przechowywania punktów odzyskiwania. Istnieją dwa sposoby zatrzymania ochrony bazy danych SQL:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania.
* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych, ale pozostawienie punktów odzyskiwania.

Jeśli wybierzesz opcję zatrzymania tworzenia kopii zapasowej z zachowaniem danych, punkty odzyskiwania zostaną wyczyszczone zgodnie z zasadami kopii zapasowych. Zostaną naliczone opłaty za chronione wystąpienie SQL, a także za magazyn używany do momentu wyczyszczenia wszystkich punktów odzyskiwania. Aby uzyskać więcej informacji na temat cen usługi Azure Backup w środowisku SQL, zobacz [stronę cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Aby zatrzymać ochronę bazy danych:

1. Otwórz magazyn usługi Recovery Services, który został zarejestrowany na maszynie wirtualnej SQL.

2. Na pulpicie nawigacyjnym **Magazyn usługi Recovery Services** w obszarze **Użycie** wybierz pozycję **Elementy kopii zapasowej**, aby otworzyć menu **Elementy kopii zapasowej**.

    ![Otwieranie menu Elementy kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Z menu **Elementy kopii zapasowej** w obszarze **Typ zarządzania kopiami zapasowymi** wybierz pozycję **SQL na maszynie wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Menu **Elementy kopii zapasowej** przedstawia listę baz danych SQL.

4. Na liście baz danych SQL wybierz bazę danych, aby zatrzymać ochronę.

    ![Wybieranie bazy danych w celu zatrzymania ochrony](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych zostanie otwarte jej menu.

5. W menu wybranej bazy danych wybierz pozycję **Zatrzymaj kopię zapasową**.

    ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-db-button.png)

    Zostanie otwarte menu **Zatrzymaj tworzenie kopii zapasowej**.

6. W menu **Zatrzymaj tworzenie kopii zapasowej** wybierz pozycję **Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**. Opcjonalnie podaj przyczynę zatrzymania ochrony i komentarz.

    ![Menu Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-backup-button.png)

7. Wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**, aby zatrzymać ochronę bazy danych.

  Pamiętaj, że opcja **Zatrzymaj tworzenie kopii zapasowej** nie będzie działać dla bazy danych w wystąpieniu z [automatyczną ochroną](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). Jedynym sposobem zatrzymania ochrony tej bazy danych jest tymczasowe wyłączenie [automatycznej ochrony](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) wystąpienia, a następnie wybranie opcji **Zatrzymaj tworzenie kopii zapasowej** w obszarze **Elementy kopii zapasowej** dla tej bazy danych.<br>
  Po wyłączeniu automatycznej ochrony można **zatrzymać tworzenie kopii zapasowej** bazy danych w obszarze **Elementy kopii zapasowej**. Teraz można ponownie włączyć automatyczną ochronę wystąpienia.


### <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Jeśli opcja **Zachowaj dane kopii zapasowej** została wybrana po zatrzymaniu ochrony bazy danych SQL, można wznowić ochronę. Jeśli dane kopii zapasowej nie zostały zachowane, nie można wznowić ochrony.

1. Aby wznowić ochronę bazy danych SQL, otwórz element kopii zapasowej, a następnie wybierz pozycję **Wznów tworzenie kopii zapasowej**.

    ![Wybieranie pozycji Wznów tworzenie kopii zapasowej w celu wznowienia ochrony bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

   Zostanie otwarte menu **Zasady kopii zapasowych**.

2. W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

### <a name="trigger-an-adhoc-backup"></a>Wyzwalanie tworzenia kopii zapasowej ad hoc

W razie potrzeby możesz wyzwalać kopie zapasowe ad hoc. Istnieją cztery typy kopii zapasowych ad hoc:

* Pełna kopia zapasowa
* Pełna kopia zapasowa tylko do kopiowania
* Różnicowa kopia zapasowa
* Kopia zapasowa dziennika

Aby uzyskać szczegółowe informacje dotyczące każdego typu, zobacz [Types of SQL backups (Typy kopii zapasowych SQL)](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Wyrejestrowywanie wystąpienia programu SQL Server

Aby wyrejestrować wystąpienie programu SQL Server po usunięciu ochrony, ale przed usunięciem magazynu:

1. Otwórz magazyn usługi Recovery Services, który został zarejestrowany na maszynie wirtualnej SQL.

2. Na pulpicie nawigacyjnym **magazynu usługi Recovery Services** w obszarze **Zarządzaj** wybierz pozycję **Infrastruktura zapasowa**.  

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. W obszarze **Serwery zarządzania** wybierz pozycję **Serwery chronione**.

   ![Wybieranie pozycji Serwery chronione](./media/backup-azure-sql-database/protected-servers.png)

    Zostanie otwarte menu **Serwery chronione**.

4. W menu **Serwery chronione** wybierz serwer, które chcesz wyrejestrować. Aby usunąć magazyn, musisz wyrejestrować wszystkie serwery.

5. W menu **Serwery chronione** kliknij prawym przyciskiem myszy serwer chroniony, a następnie wybierz pozycję **Usuń**.

   ![Wybieranie pozycji Usuń](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>Często zadawane pytania

W poniższej sekcji przedstawiono dodatkowe informacje na temat kopii zapasowej bazy danych SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>Czy mogę ograniczyć szybkość zasad tworzenia kopii zapasowych programu SQL Server?

Tak. Możesz ograniczyć szybkość wykonywania zasad tworzenia kopii zapasowych, aby zminimalizować wpływ na wystąpienie programu SQL Server.
Aby zmienić ustawienie:
1. W wystąpieniu programu SQL Server w *folderze C:\Program Files\Azure Workload Backup\bin* utwórz plik **ExtensionSettingsOverrides.json**.
2. W pliku **ExtensionSettingsOverrides.json** zmień ustawienie **DefaultBackupTasksThreshold** na niższa wartość (na przykład 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Zapisz zmiany. Zamknij plik.
4. W wystąpieniu programu SQL Server otwórz **Menedżera zadań**. Uruchom ponownie usługę **AzureWLBackupCoordinatorSvc**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełną kopię zapasową z repliki pomocniczej?
Nie. Ta funkcja nie jest obsługiwana.

### <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Czy mogę wyświetlać szczegóły dla zaplanowanych zadań kopii zapasowej w menu Zadania?

Nie. Menu **Zadania kopii zapasowej** pokazuje szczegóły zadania ad hoc, ale nie zaplanowanych zadań kopii zapasowej. Jeśli dowolne zaplanowane zadanie kopii zapasowej nie powiedzie się, szczegóły będą dostępne w alertach dotyczących zadania zakończonego niepowodzeniem. Aby monitorować wszystkie zadania tworzenia kopii zapasowej, zaplanowane i ad hoc, użyj programu [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Czy po wybraniu wystąpienia programu SQL Server przyszłe bazy danych zostaną automatycznie dodane?

Nie. W przypadku konfigurowania ochrony dla wystąpienia programu SQL Server i wybrania opcji poziomu serwera zostaną dodane wszystkie bazy danych. W przypadku dodawania baz danych do wystąpienia programu SQL Server po skonfigurowaniu ochrony należy ręcznie dodać nowe bazy danych w celu objęcia ich ochroną. Bazy danych nie są automatycznie uwzględniane w skonfigurowanej ochronie.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Jeśli zmienię model odzyskiwania, jak mam ponownie uruchomić ochronę?

Wyzwól tworzenie pełnej kopii zapasowej. Kopie zapasowe dziennika rozpoczną się zgodnie z oczekiwaniami.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Czy mogę chronić zawsze włączone grupy dostępności SQL, gdy replika podstawowa znajduje się w środowisku lokalnym?

Nie. Usługa Azure Backup chroni serwery SQL Server działające na platformie Azure. Jeśli grupa dostępności jest rozproszona między maszynami platformy Azure i maszynami lokalnymi, grupy dostępności mogą być chronione tylko wtedy, gdy replika podstawowa zostanie uruchomiona na platformie Azure. Ponadto usługa Azure Backup chroni tylko węzły w tym samym regionie platformy Azure, co magazyn usługi Recovery Services.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>Czy mogę chronić zawsze włączone grupy dostępności SQL, które są rozmieszczone między regionami platformy Azure?

Magazyn usługi Recovery Service w usłudze Azure Backup może wykryć i chronić wszystkie węzły, które znajdują się w tym samym regionie, co magazyn usługi Recovery Services. Jeśli masz zawsze włączoną grupę dostępności SQL obejmującą wiele regionów platformy Azure, musisz skonfigurować tworzenie kopii zapasowej z regionu zawierającego węzeł podstawowy. Usługa Azure Backup będzie mogła wykryć i chronić wszystkie bazy danych w grupie dostępności zgodnie z preferencjami dotyczącymi tworzenia kopii zapasowych. Jeśli preferencje tworzenia kopii zapasowych nie zostaną zaspokojone, tworzenie kopii zapasowych zakończy się niepowodzeniem i zostanie wyświetlony alert o niepowodzeniu.

### <a name="while-i-want-to-protect-most-of-the-databases-in-an-instance-i-would-like-to-exclude-a-few-is-it-possible-to-still-use-the-auto-protection-feature"></a>Chcę chronić większość baz danych w wystąpieniu usługi, ale kilka chcę wykluczyć. Czy mogę nadal korzystać z funkcji automatycznej ochrony?

Nie, [automatyczna ochrona](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) ma zastosowanie do całego wystąpienia. Nie można selektywnie chronić baz danych za pomocą automatycznej ochrony wystąpienia.

### <a name="can-i-have-different-policies-for-different-databases-in-an-auto-protected-instance"></a>Czy w wystąpieniu objętym automatyczną ochroną można mieć różne zasady dla różnych baz danych?

Jeśli masz już niektóre chronione bazy danych w wystąpieniu, będą one nadal chronione przy użyciu odpowiednich zasad, nawet po ustawieniu wartości **WŁ.** dla opcji [automatycznej ochrony](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). Wszystkie niechronione bazy danych oraz te, które zostaną dodane w przyszłości, będą jednak mieć tylko pojedyncze zasady definiowane w obszarze **konfigurowania kopii zapasowej** po wybraniu baz danych. W rzeczywistości, w przeciwieństwie do innych chronionych baz danych, nie można nawet zmienić zasad dla bazy danych w ramach wystąpienia chronionego automatycznie.
Jeśli chcesz to zrobić, jedynym sposobem jest tymczasowe wyłączenie automatycznej ochrony wystąpienia, a następnie zmiana zasad dla tej bazy danych. Możesz teraz ponownie włączyć ochronę automatyczną tego wystąpienia.

### <a name="if-i-delete-a-database-from-an-auto-protected-instance-will-the-backups-for-that-database-also-stop"></a>Jeśli usunę bazę danych z wystąpienia chronionego automatycznie, czy tworzenie kopii zapasowych dla tej bazy danych zostanie również zatrzymane?

Nie, jeśli baza danych zostanie usunięta z wystąpienia chronionego automatycznie, nadal będą podejmowane próby utworzenia kopii zapasowych tej bazy danych. Oznacza to, że usunięta baza danych zacznie pojawiać się jako baza danych w złej kondycji w obszarze **Elementy kopii zapasowej** i nadal będzie traktowana jako chroniona.

Jedynym sposobem zatrzymania ochrony tej bazy danych jest tymczasowe wyłączenie [automatycznej ochrony](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) wystąpienia, a następnie wybranie opcji **Zatrzymaj tworzenie kopii zapasowej** w obszarze **Elementy kopii zapasowej** dla tej bazy danych. Możesz teraz ponownie włączyć ochronę automatyczną tego wystąpienia.

###  <a name="why-cant-i-see-the-newly-added-database-to-an-auto-protected-instance-under-the-protected-items"></a>Dlaczego nie widzę nowo dodanej bazy danych w wystąpieniu chronione automatycznie, w obszarze chronionych elementów?

Możesz nie widzieć od razu bazy danych nowo dodanej do chronionego wystąpienia [objętego automatyczną ochroną](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). Dzieje się tak, ponieważ odnajdywanie jest zazwyczaj uruchamiane co 8 godzin. Użytkownik może jednak uruchamiać odnajdywanie ręczne przy użyciu opcji **Odzyskaj bazy danych** w celu natychmiastowego odnajdywania i chronienia baz danych, jak pokazano na poniższym obrazie:

  ![Wyświetlanie nowo dodanej bazy danych](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup, zobacz przykładowy skrypt programu Azure PowerShell umożliwiający tworzenie kopii zapasowej zaszyfrowanych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie kopii zapasowej zaszyfrowanej maszyny wirtualnej](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
