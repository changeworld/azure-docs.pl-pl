---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure | Microsoft Docs
description: W tym samouczku opisano sposób tworzenia kopii zapasowych programu SQL Server na platformie Azure. W artykule objaśniono również proces odzyskiwania programu SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 17ec7723044cec391ebe390bbcfba3aa6f2f29ca
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446855"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure 

Bazy danych programu SQL Server to krytyczne obciążenia, które wymagają niskiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Za pomocą usługi [Azure Backup](backup-overview.md) można tworzyć kopie zapasowe baz danych programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure. 

W tym artykule pokazano, jak utworzyć kopię zapasową bazy danych programu SQL Server uruchomionej na maszynie wirtualnej platformy Azure do magazynu usługi Azure Backup Recovery Services. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Sprawdzanie wymagań wstępnych dotyczących tworzenia kopii zapasowych wystąpienia programu SQL Server.
> * Tworzenie i konfigurowanie magazynu.
> * Odnajdywanie baz danych i konfigurowanie kopii zapasowych.
> * Konfigurowanie ochrony automatycznej dla baz danych.

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem sprawdź następujące kwestie:

1. Upewnij się, że masz wystąpienie programu SQL Server uruchomione na platformie Azure. Możesz [szybko utworzyć wystąpienie programu SQL Server](../sql-database/sql-database-get-started-portal.md) w witrynie Marketplace.
2. Przejrzyj poniższe ograniczenia publicznej wersji zapoznawczej.
3. Przejrzyj obsługę scenariuszy.
4. [Przejrzyj często zadawane pytania](faq-backup-sql-server.md) dotyczące tego scenariusza.


## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Ta publiczna wersja zapoznawcza ma ograniczenia.

- Maszyna wirtualna z uruchomionym programem SQL Server wymaga łączności z Internetem do uzyskiwania dostępu do publicznych adresów IP platformy Azure. 
- Możesz tworzyć kopię zapasową maksymalnie 2000 baz danych programu SQL Server w jednym magazynie. Jeśli masz ich więcej, utwórz kolejny magazyn. 
- Kopie zapasowe [rozproszonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) nie w pełni działają.
- Zawsze włączone wystąpienia klastra trybu failover w programie SQL Server nie są obsługiwane w przypadku tworzenia kopii zapasowej.
- Tworzenie kopii zapasowych programu SQL Server należy skonfigurować w portalu. Obecnie nie można konfigurować kopii zapasowych za pomocą programu Azure PowerShell, interfejsu wiersza polecenia ani interfejsów API REST.
- Nie są obsługiwane operacje tworzenia kopii zapasowych i przywracania dla duplikatów baz danych wystąpienia klastra trybu failover oraz baz danych i ich migawek.
- Nie można chronić baz danych z dużą liczbą plików. Maksymalna liczba obsługiwanych plików nie jest deterministyczna. Zależy ona nie tylko od liczby plików, ale też od długości ścieżki plików. 

Zapoznaj się z [sekcją często zadawanych pytań](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq), aby uzyskać więcej informacji na temat pomocy technicznej/nieobsługiwanych scenariuszy.

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane wdrożenia** | Obsługiwane są maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server).
**Obsługiwane obszary geograficzne** | Australia Południowo-Wschodnia (ASE); Australia Wschodnia (AE); Azja Południowo-Wschodnia (SEA); Azja Wschodnia (EA); Brazylia Południowa (BRS); Europa Północna (NE); Europa Zachodnia (WE); Indie Środkowe (INC); Indie Południowe (INS); Japonia Wschodnia (JPE); Japonia Zachodnia (JPW); Kanada Środkowa (CNC); Kanada Wschodnia (CE); Korea Środkowa (KRC); Korea Południowa (KRS); Południowe Zjednoczone Królestwo (UKS); Środkowe stany USA (CUS); Środkowo-północne stany USA (NCUS); Środkowo-południowe stany USA (SCUS); Środkowo-zachodnie stany USA (WCUS); Wschodnie stany USA (EUS); Wschodnie stany USA 2 (EUS2); Zachodnie stany USA (WUS); Zachodnie stany USA 2 (WUS 2); Zachodnie Zjednoczone Królestwo (UKW)
**Obsługiwane systemy operacyjne** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> System Linux nie jest obecnie obsługiwany.
**Obsługiwane wersje programu SQL Server** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.


## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem kopii zapasowej bazy danych programu SQL Server sprawdź następujące warunki:

1. Zidentyfikuj lub [utwórz](backup-azure-sql-database.md#create-a-recovery-services-vault) magazyn usługi Recovery Services w obrębie tego samego regionu lub ustawień regionalnych co maszyna wirtualna, która hostuje wystąpienie programu SQL Server.
2. [Sprawdź uprawnienia maszyny wirtualnej](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) potrzebne do tworzenia kopii zapasowych baz danych SQL.
3. Sprawdź, czy maszyna wirtualna ma [połączenie sieciowe](backup-azure-sql-database.md#establish-network-connectivity).
4. Sprawdź, czy bazy danych programu SQL Server są nazwane zgodnie ze [wskazówkami dotyczącymi nazewnictwa](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) dla usługi Azure Backup.
5. Sprawdź, czy dla bazy danych nie są włączone inne rozwiązania do tworzenia kopii zapasowych. Wyłącz wszystkie inne kopie zapasowe programu SQL Server przed skonfigurowaniem tego scenariusza. Możesz bez konfliktów włączyć usługę Azure Backup dla maszyny wirtualnej platformy Azure razem z usługą Azure Backup dla bazy danych programu SQL Server uruchomioną na tej maszynie wirtualnej.


### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Podczas wszystkich operacji maszyna wirtualna programu SQL Server musi mieć łączność z publicznymi adresami IP platformy Azure. Operacje maszyny wirtualnej (odnajdywanie baz danych, konfigurowanie kopii zapasowych, planowanie tworzenia kopii zapasowych, przywracanie punktów odzyskiwania itd.) nie powiodą się w przypadku braku łączności z publicznymi adresami IP. Ustanów łączność, używając jednej z następujących opcji:

- **Zezwolenie na zakresy adresów IP centrum danych platformy Azure**: Zezwól na [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) dla pobierania. Na potrzeby dostępu w grupie zabezpieczeń sieci użyj polecenia cmdlet **Set-AzureNetworkSecurityRule**.
- **Wdrożenie serwera proxy HTTP na potrzeby kierowania ruchu**: Gdy tworzysz kopię zapasową bazy danych programu SQL Server na maszynie wirtualnej platformy Azure, rozszerzenie kopii zapasowej na maszynie wirtualnej używa interfejsów API protokołu HTTPS do wysyłania poleceń zarządzania do usługi Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej również używa usługi Azure Active Directory (Azure AD) do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Rozszerzenie to jedyny składnik skonfigurowany do uzyskiwania dostępu do publicznego Internetu.

Każda opcja ma zalety i wady

**Opcja** | **Zalety** | **Wady**
--- | --- | ---
Zezwolenie na zakresy adresów IP | Brak dodatkowych kosztów. | Skomplikowane zarządzanie, ponieważ zakresy adresów IP zmieniają się z upływem czasu. <br/><br/> Zapewnia dostęp do całej platformy Azure, nie tylko do usługi Azure Storage.
Używanie serwera proxy HTTP   | Na serwerze jest dozwolona szczegółowa kontrola adresów URL magazynu. <br/><br/> Pojedynczy punkt dostępu do maszyn wirtualnych w Internecie. <br/><br/> Nie podlega zmianom adresów IP platformy Azure. | Dodatkowe koszty uruchamiania maszyny wirtualnej przy użyciu oprogramowania serwera proxy. 

### <a name="set-vm-permissions"></a>Ustawianie uprawnień maszyny wirtualnej

Usługa Azure Backup wykonuje różne operacje podczas konfigurowania kopii zapasowych dla bazy danych programu SQL Server:

- Dodaje rozszerzenie **AzureBackupWindowsWorkload**.
- Aby odnaleźć bazy danych na maszynie wirtualnej, usługa Azure Backup tworzy konto **NT SERVICE\AzureWLBackupPluginSvc**. To konto służy do tworzenia kopii zapasowych i przywracania oraz wymaga uprawnień administratora systemu SQL.
- Usługa Azure Backup korzysta z konta **NT AUTHORITY\SYSTEM** na potrzeby odnajdywania/zapytań dotyczących bazy danych, dlatego to konto musi być publicznym logowaniem w środowisku SQL.

Jeśli nie utworzono maszyny wirtualnej programu SQL Server z witryny Azure Marketplace, może wystąpić błąd **UserErrorSQLNoSysadminMembership**. W takim przypadku [wykonaj te instrukcje](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Wskazówki dotyczące weryfikowania nazewnictwa baz danych dla usługi Azure Backup

Unikaj następujących elementów w nazwach baz danych:

  * Spacje końcowe/początkowe
  * Końcowy znak „!”
  * Zamykający nawias kwadratowy „]”

Oferujemy aliasy nieobsługiwanych znaków tabel platformy Azure, ale zalecamy ich unikanie. [Dowiedz się więcej](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Odnajdź bazy danych uruchomione na maszynie wirtualnej. 
1. W [witrynie Azure Portal](https://portal.azure.com) otwórz magazyn usługi Recovery Services używany do tworzenia kopii zapasowych bazy danych.

2. Na pulpicie nawigacyjnym **magazynu usługi Recovery Services** wybierz pozycję **Utwórz kopię zapasową**. 

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W obszarze **Cel kopii zapasowej** ustaw pozycję **Gdzie działa Twoje obciążenie** na wartość **Azure** (wartość domyślna).

4. W obszarze **Co ma zawierać kopia zapasowa** wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


5. W obszarze **Cel kopii zapasowej** > **Odnajdywanie baz danych na maszynach wirtualnych** wybierz pozycję **Uruchom odnajdywanie**, aby wyszukać niechronione maszyny wirtualne w subskrypcji. Może to trochę potrwać, zależnie od liczby niechronionych maszyn wirtualnych w subskrypcji.

    ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na liście maszyn wirtualnych wybierz maszynę wirtualną z uruchomioną bazą danych programu SQL Server i wybierz pozycję **Odnajdź bazy danych**.

7. Śledź odnajdywanie baz danych w obszarze **Powiadomienia**. Wykonanie zadania może trochę potrwać, zależnie od liczby baz danych na maszynie wirtualnej. Po odnalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - Niechronione maszyny wirtualne powinny zostać wyświetlone na liście po odnalezieniu, uporządkowane według nazwy i grupy zasobów.
    - Jeśli którakolwiek maszyna wirtualna nie jest wyświetlana zgodnie z oczekiwaniami, sprawdź, czy jej kopie zapasowe są już tworzone w magazynie.
    - Wiele maszyn wirtualnych może mieć tę samą nazwę, ale należeć do różnych grup zasobów. 

9. Wybierz maszynę wirtualną z uruchomioną bazą danych programu SQL Server i wybierz pozycję **Odnajdź bazy danych**. 
10. Usługa Azure Backup odnajduje wszystkie bazy danych programu SQL Server na maszynie wirtualnej. Podczas odnajdywania są wykonywane następujące operacje w tle:

    - Usługa Azure Backup rejestruje maszynę wirtualną w magazynie na potrzeby kopii zapasowych obciążenia. Dla wszystkich baz danych na zarejestrowanej maszynie wirtualnej można tworzyć kopie zapasowe tylko w tym magazynie.
    - Usługa Azure Backup instaluje rozszerzenie **AzureBackupWindowsWorkload** na maszynie wirtualnej. Żaden agent nie jest instalowany w bazie danych SQL.
    - Usługa Azure Backup tworzy konto usługi **NT Service\AzureWLBackupPluginSvc** na maszynie wirtualnej.
      - Wszystkie operacje tworzenia kopii zapasowych i przywracania korzystają z konta usługi.
      - Usługa **NT Service\AzureWLBackupPluginSvc** musi mieć uprawnienia administratora systemu SQL. Wszystkie maszyny wirtualne programu SQL Server utworzone w witrynie Azure Marketplace mają zainstalowane rozszerzenie **SqlIaaSExtension**. Rozszerzenie **AzureBackupWindowsWorkload** używa rozszerzenia **SQLIaaSExtension** w celu automatycznego uzyskania wymaganych uprawnień.
    - Jeśli nie utworzono maszyny wirtualnej z witryny Marketplace, maszyna wirtualna nie ma zainstalowanego rozszerzenia **SqlIaaSExtension**, a operacja odnajdywania kończy się niepowodzeniem z komunikatem o błędzie **UserErrorSQLNoSysAdminMembership**. Wykonaj instrukcje podane w sekcji [#fix-sql-sysadmin-permissions], aby rozwiązać ten problem.

        ![Wybieranie maszyny wirtualnej i bazy danych](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych 

Aby zoptymalizować obciążenia kopii zapasowych, usługa Azure Backup ustawia maksymalną liczbę baz danych w jednym zadaniu tworzenia kopii zapasowej na 50.

- Aby chronić więcej niż 50 baz danych, skonfiguruj wiele kopii zapasowych.
- Możesz też włączyć ochronę automatyczną. Ochrona automatyczna równocześnie włącza ochronę wszystkich istniejących baz danych oraz automatycznie chroni nowe bazy danych dodawane do wystąpienia grupy dostępności.


Skonfiguruj tworzenie kopii zapasowych w następujący sposób:

1. Na pulpicie nawigacyjnym magazynu wybierz pozycję **Kopia zapasowa**. 

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W menu **Cel kopii zapasowej** ustaw pozycję **Gdzie działa Twoje obciążenie** na wartość **Azure**.

4. W obszarze **Co ma zawierać kopia zapasowa** wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
5. W obszarze **Cel kopii zapasowej** wybierz pozycję **Konfiguruj kopię zapasową**.

    ![Wybieranie pozycji Konfiguruj kopię zapasową](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![Wyświetlanie wszystkich wystąpień programu SQL Server z autonomicznymi bazami danych](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Wybierz wszystkie bazy danych, które chcesz chronić, i wybierz pozycję **OK**.

    ![Chronienie bazy danych](./media/backup-azure-sql-database/select-database-to-protect.png)

    - Wyświetlane są wszystkie wystąpienia programu SQL Server (autonomiczne i grupy dostępności).
    - Aby zastosować filtr, wybierz strzałkę w dół po lewej stronie nazwy wystąpienia lub grupy dostępności.

7. W menu **Kopia zapasowa** wybierz pozycję **Zasady kopii zapasowych**. 

    ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-azure-sql-database/select-backup-policy.png)

8. Wybierz zasady w obszarze **Wybierz zasady tworzenia kopii zapasowej**, a następnie kliknij pozycję **OK**.

    - Wybrać zasady domyślne: **HourlyLogBackup**.
    - Wybrać istniejące zasady kopii zapasowych utworzone wcześniej dla środowiska SQL.
    - [Zdefiniować nowe zasady](backup-azure-sql-database.md#define-a-backup-policy) oparte na własnym celu punktu odzyskiwania i zakresie przechowywania.
    - W wersji zapoznawczej nie można edytować istniejących zasad kopii zapasowych.
    
9. W menu **Kopia zapasowa** wybierz pozycję **Włącz kopię zapasową**.

    ![Włączanie wybranych zasad kopii zapasowych](./media/backup-azure-sql-database/enable-backup-button.png)

10. Śledź postęp konfiguracji w obszarze **Powiadomienia** portalu.

    ![Obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady kopii zapasowych określają, kiedy są tworzone kopie zapasowe i jak długo są przechowywane. 

- Zasady są tworzone na poziomie magazynu.
- Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.
- Po utworzeniu zasad kopii zapasowych ustawieniem domyślnym będzie pełna kopia zapasowa tworzona codziennie.
- Możesz dodać różnicową kopię zapasową, ale tylko jeśli skonfigurujesz tworzenie pełnych kopii zapasowych co tydzień.
- [Dowiedz się więcej](backup-architecture.md#sql-server-backup-types) o różnych typach zasad kopii zapasowych.


Aby utworzyć nowe zasady kopii zapasowych:

1. W magazynie kliknij pozycję **Zasady kopii zapasowych** > **Dodaj**.
2. W menu **Dodaj** kliknij polecenie **Program SQL Server na maszynie wirtualnej platformy Azure**. Spowoduje to zdefiniowanie typu zasad.

   ![Wybieranie typu nowych zasad kopii zapasowych](./media/backup-azure-sql-database/policy-type-details.png)

3. W polu **Nazwa zasad** wprowadź nazwę nowych zasad. 
4. W obszarze **Zasady pełnej kopii zapasowej** wybierz opcję **Częstotliwość tworzenia kopii zapasowej** (**Codziennie** lub **Co tydzień**).

    - W przypadku opcji **Codziennie** wybierz godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.
    - Należy tworzyć pełną kopię zapasową. Nie można wyłączyć opcji **Pełna kopia zapasowa**.
    - Kliknij pozycję **Pełna kopia zapasowa**, aby wyświetlić zasady. 
    - Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.
    - W przypadku opcji **Co tydzień** wybierz dzień tygodnia, godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.

    ![Pola nowych zasad kopii zapasowych](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Domyślnie wszystkie opcje **Zakres przechowywania** są wybrane. Wyczyść wszelkie ograniczenia zakresu przechowywania, których nie chcesz używać, oraz ustaw interwały do użycia. 

    - Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    - Kopia zapasowa dla określonego dnia jest oznaczana i przechowywana w oparciu o tygodniowy zakres przechowywania oraz ustawienie przechowywania co tydzień.
    - Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

   ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

    

6. W menu **zasad pełnej kopii zapasowej** wybierz pozycję **OK**, aby zaakceptować ustawienia.
7. Aby dodać zasady tworzenia różnicowej kopii zapasowej, wybierz pozycję **Różnicowa kopia zapasowa**. 

   ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Otwieranie menu zasad różnicowej kopii zapasowej](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania. 

    - Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.
    - Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

9. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

10. Aby dodać zasady kopii zapasowej dziennika transakcji, wybierz pozycję **Kopia zapasowa dziennika**.
11. W obszarze **Kopia zapasowa dziennika** wybierz pozycję **Włącz**, a następnie ustaw kontrolki częstotliwości i przechowywania. Kopie zapasowe dziennika można tworzyć nawet co 15 minut. Można je również zachowywać przez maksymalnie 35 dni.
12. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

   ![Edytowanie zasad kopii zapasowej dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. W menu **Zasady kopii zapasowych** wybierz, czy chcesz włączyć **kompresję kopii zapasowej SQL**.
    - Kompresja jest domyślnie wyłączona.
    - Na zapleczu usługa Azure Backup używa natywnej kompresji kopii zapasowej SQL.

14. Po zakończeniu edycji zasad kopii zapasowych wybierz pozycję **OK**.



## <a name="enable-auto-protection"></a>Włączanie ochrony automatycznej  

Włącz ochronę automatyczną, aby automatycznie tworzyć kopie zapasowe wszystkich baz danych, które już istnieją albo zostaną dodane w przyszłości do autonomicznego wystąpienia programu SQL Server lub zawsze włączonej grupy dostępności programu SQL Server. 

- Gdy włączysz ochronę automatyczną i wybierzesz zasady, istniejące chronione bazy danych będą nadal używać poprzednich zasad.
- Nie ma ograniczenia liczby baz danych, które można wybrać do równoczesnego włączenia ochrony automatycznej.

Włącz ochronę automatyczną w następujący sposób:

1. W obszarze **Wybieranie elementów do wykonania kopii zapasowej** wybierz wystąpienie, dla którego chcesz włączyć ochronę automatyczną.
2. Wybierz listę rozwijaną w obszarze **Automatyczna ochrona** i ustaw pozycję **Włączona**. Następnie kliknij przycisk **OK**.

    ![Włączanie automatycznej ochrony zawsze włączonej grupy dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

3. Tworzenie kopii zapasowych jest konfigurowane wspólnie dla wszystkich baz danych i można je śledzić w obszarze **Zadania kopii zapasowej**.


Jeśli chcesz wyłączyć ochronę automatyczną, kliknij nazwę wystąpienia w obszarze **Konfiguruj kopię zapasową** i wybierz pozycję **Wyłącz automatyczną ochronę** dla tego wystąpienia. Tworzenie kopii zapasowych będzie kontynuowane dla wszystkich baz danych. Jednak przyszłe bazy danych nie będą automatycznie chronione.

![Wyłączanie automatycznej ochrony wystąpienia](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>Naprawianie uprawnień administratora systemu SQL

Aby rozwiązać problem z uprawnieniami spowodowany błędem **UserErrorSQLNoSysadminMembership**, wykonaj następujące czynności:

1. Zaloguj się do programu SQL Server Management Studio (SSMS), używając konta z uprawnieniami administratora systemu SQL Server. Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelnianie systemu Windows powinno działać.
2. W programie SQL Server otwórz folder **Security/Logins**.

    ![Otwieranie folderu Security/Logins w celu wyświetlenia kont](./media/backup-azure-sql-database/security-login-list.png)

3. Kliknij prawym przyciskiem myszy folder **Logins**, a następnie wybierz pozycję **Nowa nazwa logowania**. W oknie **Nazwa logowania — nowa** wybierz pozycję **Wyszukaj**.

    ![Wybieranie pozycji Wyszukaj w oknie dialogowym Nazwa logowania — nowa](./media/backup-azure-sql-database/new-login-search.png)

3. Konto usługi wirtualnej systemu Windows **NT SERVICE\AzureWLBackupPluginSvc** zostało utworzone podczas rejestracji maszyny wirtualnej i fazy odnajdywania SQL. Wprowadź nazwę konta, jak pokazano w polu **Wprowadź nazwę obiektu do wybrania**. Wybierz pozycję **Sprawdź nazwy** w celu rozpoznania nazwy. Kliknij przycisk **OK**.

    ![Wybieranie pozycji Sprawdź nazwy w celu rozpoznania nieznanej nazwy](./media/backup-azure-sql-database/check-name.png)


4. W obszarze **Role serwera** sprawdź, czy wybrano rolę **sysadmin**. Kliknij przycisk **OK**. Wymagane uprawnienia powinny teraz istnieć.

    ![Sprawdzanie, czy wybrano rolę sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

5. Skojarz bazę danych z magazynem usługi Recovery Services. W witrynie Azure Portal na liście **Serwery chronione** kliknij prawym przyciskiem myszy serwer w stanie błędu i wybierz pozycję **Ponownie znajdź bazy danych**.

    ![Sprawdzanie, czy serwer ma odpowiednie uprawnienia](./media/backup-azure-sql-database/check-erroneous-server.png)

6. Sprawdź postęp w obszarze **Powiadomienia**. Po znalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

Możesz również włączyć [automatyczną ochronę](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) całego wystąpienia lub zawsze włączonej grupy dostępności, wybierając opcję **WŁ.** na odpowiedniej liście rozwijanej w kolumnie **AUTOMATYCZNA OCHRONA**. Funkcja [automatycznej ochrony](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) nie tylko równocześnie włącza ochronę wszystkich istniejących baz danych, ale również automatycznie chroni wszystkie nowe bazy danych, które w przyszłości zostaną dodane do wystąpienia lub grupy dostępności.  

   ![Włączanie automatycznej ochrony zawsze włączonej grupy dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](restore-sql-database-azure-vm.md) o przywracaniu baz danych programu SQL Server z kopii zapasowych.
- [Dowiedz się więcej](manage-monitor-sql-database-backup.md) o zarządzaniu bazami danych programu SQL Server, dla których są tworzone kopie zapasowe.

