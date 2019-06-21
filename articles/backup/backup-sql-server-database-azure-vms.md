---
title: Utwórz kopię zapasową bazy danych SQL Server na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię zapasową bazy danych SQL Server na maszynach wirtualnych Azure
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sachdevaswati
ms.openlocfilehash: 28577bfc755d80cd479a40b9e2b653af6ddec319
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204452"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure

Bazy danych programu SQL Server są krytycznych obciążeń, które wymagają cel niski punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową baz danych programu SQL Server uruchomiony na maszynach wirtualnych Azure (maszyny wirtualne) przy użyciu [kopia zapasowa Azure](backup-overview.md).

W tym artykule pokazano, jak wykonać kopię zapasową bazy danych programu SQL Server uruchomionego na Maszynie wirtualnej platformy Azure do magazynu usługi Azure Backup Recovery Services.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie i konfigurowanie magazynu.
> * Odnajdowanie baz danych i skonfigurować kopie zapasowe.
> * Konfigurowanie ochrony automatycznej dla baz danych.


## <a name="prerequisites"></a>Wymagania wstępne

Przed tworzysz kopię zapasową bazy danych programu SQL Server, sprawdź następujące kryteria:

1. Określ lub Utwórz [magazyn usługi Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) w tym samym regionie lub ustawień regionalnych jako maszyny Wirtualnej obsługującego wystąpienie programu SQL Server.
2. Sprawdź, czy maszyna wirtualna ma [połączenia sieciowego](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Upewnij się, że bazy danych programu SQL Server, wykonaj [bazy danych wskazówkami nazewnictwa usługi Azure Backup](#database-naming-guidelines-for-azure-backup).
4. Specjalnie dla programu SQL 2008 i 2008 R2 [Dodaj klucz rejestru](#add-registry-key-to-enable-registration) umożliwiające rejestracji serwera. Ten krok można nie będzie wymagane, gdy funkcja jest ogólnie dostępna.
5. Upewnij się, że masz inne rozwiązania tworzenia kopii zapasowej włączone dla bazy danych. Wyłącz wszystkie pozostałe kopie zapasowe programu SQL Server przed kopii zapasowej bazy danych.

> [!NOTE]
> Usługa Azure Backup można włączyć dla maszyny Wirtualnej platformy Azure, a także dla bazy danych programu SQL Server uruchomiony na maszynie Wirtualnej bez powodowania konfliktów.


### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Dla wszystkich operacji maszynę Wirtualną programu SQL Server wymaga łączności z platformy Azure z publicznymi adresami IP. Operacje maszyny Wirtualnej (odnajdowaniu baz danych, skonfigurowanie kopii zapasowych, Planowanie tworzenia kopii zapasowych, przywracania punktów odzyskiwania i tak dalej) zakończyć się niepowodzeniem bez połączenia z platformy Azure z publicznymi adresami IP.

Należy ustanowić połączenie przy użyciu jednej z następujących opcji:

- **Zezwól na użycie zakresów adresów IP centrum danych platformy Azure**. Ta opcja umożliwia [zakresów adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) do pobrania. Aby uzyskać dostęp do grupy zabezpieczeń sieci (NSG), należy użyć polecenia cmdlet AzureNetworkSecurityRule zestawu. Jeśli bezpieczne adresatów listy tylko adresy IP określonego regionu, należy również zaktualizować listę bezpiecznych adresatów tag usługi Azure Active Directory (Azure AD), aby włączyć uwierzytelnianie.

- **Zezwalaj na dostęp za pomocą sieciowej grupy zabezpieczeń tagów**. Jeśli używasz sieciowych grup zabezpieczeń do ograniczenia łączności, ta opcja dodaje regułę do usługi sieciowej grupy zabezpieczeń, która umożliwia dostęp ruchu wychodzącego do usługi Azure Backup przy użyciu tagu AzureBackup. Oprócz tego tagu potrzebna będzie również odpowiadające [reguły](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) dla usługi Azure AD i Azure Storage, aby zezwolić na połączenia do transmisji danych i uwierzytelniania. AzureBackup tag jest obecnie dostępna w programie PowerShell tylko. Aby utworzyć regułę przy użyciu tagu AzureBackup:

    - Dodawanie poświadczeń konta platformy Azure i aktualizowanie chmur krajowych<br/>
    `Add-AzureRmAccount`

    - Wybierz subskrypcję, sieciowej grupy zabezpieczeń<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Wybierz grupy zabezpieczeń sieci<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Dodaj Zezwalaj reguły ruchu wychodzącego dla tagu usługi Azure Backup<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Zapisywanie sieciowej grupy zabezpieczeń<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Zezwalaj na dostęp przy użyciu tagów zapory usługi Azure**. Jeśli używasz zapory usługi Azure, tworzenie aplikacji za pomocą reguły AzureBackup [FQDN tag](https://docs.microsoft.com/azure/firewall/fqdn-tags). Dzięki temu dostęp ruchu wychodzącego do usługi Azure Backup.
- **Wdrażanie serwera proxy HTTP na potrzeby kierowania ruchu**. Gdy tworzysz kopię zapasową bazy danych programu SQL Server na Maszynie wirtualnej platformy Azure, rozszerzenie kopii zapasowej na maszynie Wirtualnej używa interfejsów API protokołu HTTPS wysyłać polecenia zarządzania usługi Azure Backup i danych do usługi Azure Storage. Zapasowy numer wewnętrzny również używa usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Rozszerzenia są jedynym składnikiem, który jest skonfigurowany do uzyskiwania dostępu do publicznej sieci internet.

Opcje łączności obejmują następujące zalety i wady:

**Option** | **Zalety** | **Wady**
--- | --- | ---
Zezwolenie na zakresy adresów IP | Bez dodatkowych kosztów | Złożone, aby zarządzać, ponieważ zakresy adresów IP zmienia się wraz z upływem czasu <br/><br/> Zapewnia dostęp do całego systemu Azure, nie tylko usługi Azure Storage
Za pomocą tagów usługi sieciowej grupy zabezpieczeń | Łatwiejsza w zarządzaniu jako zakres zmiany są automatycznie łączone. <br/><br/> Bez dodatkowych kosztów <br/><br/> | Może być używane tylko z sieciowymi grupami zabezpieczeń <br/><br/> Zapewnia dostęp do całej usługi
Za pomocą tagów zapory platformy Azure w pełni kwalifikowaną nazwę domeny | Łatwiejsza w zarządzaniu jako nazwy FQDN wymagane są zarządzane automatycznie | Może być używany tylko z zapory usługi Azure
Używanie serwera proxy HTTP | Zapewnia szczegółową kontrolę na serwerze proxy magazynu dozwolone adresy URL <br/><br/> Pojedynczy punkt internet dostępu do maszyn wirtualnych <br/><br/> Niepodlegająca zmiany adresów IP platformy Azure | Dodatkowe koszty, aby uruchomić Maszynę wirtualną z oprogramowaniem serwera proxy

### <a name="database-naming-guidelines-for-azure-backup"></a>Wskazówki dotyczące nazewnictwa dla usługi Azure Backup bazy danych

Należy unikać nazw bazy danych przy użyciu następujących elementów:

  * Końcowe i spacje wiodące
  * Końcowe znaki wykrzyknika (!)
  * Zamykających nawiasów kwadratowych (])
  * Średnik ";"
  * Ukośnik "/"

Tworzenie aliasów jest dostępna dla nieobsługiwane znaki, ale zaleca się ich unikać. Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

### <a name="add-registry-key-to-enable-registration"></a>Dodaj klucz rejestru, aby włączyć rejestrację

1. Open Regedit
2. Utwórz ścieżkę rejestru w katalogu: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (należy utworzyć TestHook "Key" w obszarze WorkloadBackup, który z kolei musi zostać utworzona w ramach firmy Microsoft).
3. W ścieżce rejestru katalogu, Utwórz nową "wartość ciągu" o nazwie ciąg **AzureBackupEnableWin2K8R2SP1** i wartości: **True**

    ![RegEdit włączania rejestracji](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

Alternatywnie możesz zautomatyzować ten krok, uruchamiając plik reg za pomocą następującego polecenia:

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Jak wykryć bazy danych uruchomione na maszynie Wirtualnej:

1. W [witrynie Azure Portal](https://portal.azure.com) otwórz magazyn usługi Recovery Services używany do tworzenia kopii zapasowych bazy danych.

2. W **magazyn usługi Recovery Services** pulpitu nawigacyjnego, wybierz opcję **kopii zapasowej**.

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W **cel kopii zapasowej**ustaw **gdzie jest uruchomione Twoje obciążenie?** do **Azure**.

4. W obszarze **Co ma zawierać kopia zapasowa** wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. W obszarze **Cel kopii zapasowej** > **Odnajdywanie baz danych na maszynach wirtualnych** wybierz pozycję **Uruchom odnajdywanie**, aby wyszukać niechronione maszyny wirtualne w subskrypcji. To wyszukiwanie może wymagać trochę czasu, w zależności od liczby niechronione maszyny wirtualne w subskrypcji.

   - Niechronione maszyny wirtualne powinny zostać wyświetlone na liście po odnalezieniu, uporządkowane według nazwy i grupy zasobów.
   - Jeśli maszyna wirtualna nie ma na liście zgodnie z oczekiwaniami, zobacz, czy już kopii zapasowej w magazynie.
   - Wiele maszyn wirtualnych może mieć takiej samej nazwie, ale będzie należą do różnych grup zasobów.

     ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na liście maszyn wirtualnych wybierz maszynę wirtualną z uruchomioną bazą danych programu SQL Server i wybierz pozycję **Odnajdź bazy danych**.

7. Śledź odnajdowaniu baz danych w **powiadomienia**. Czas wymagany do wykonania tej akcji, zależy od liczby baz danych, maszyny Wirtualnej. Po odnalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Usługa Azure Backup odnajduje wszystkie bazy danych programu SQL Server na maszynie wirtualnej. Podczas odnajdywania następujące elementy występują w tle:

    - Usługa Azure Backup rejestruje maszynę Wirtualną z magazynu dla kopii zapasowej obciążenia. Wszystkie bazy danych na maszynie Wirtualnej zarejestrowanej mogą można utworzyć kopie zapasowe tylko do tego magazynu.
    - Usługa Azure Backup instaluje rozszerzenie AzureBackupWindowsWorkload na maszynie Wirtualnej. Agent nie jest zainstalowany na bazę danych SQL.
    - Usługa Azure Backup tworzy konto usługi NT Service\AzureWLBackupPluginSvc na maszynie Wirtualnej.
      - Wszystkie operacje tworzenia kopii zapasowych i przywracania korzystają z konta usługi.
      - NT Service\AzureWLBackupPluginSvc musi mieć uprawnienia administratora systemu SQL. Wszystkie maszyny wirtualne SQL Server utworzone w portalu Marketplace są dostarczane z SqlIaaSExtension zainstalowane. Rozszerzenie AzureBackupWindowsWorkload używa SQLIaaSExtension, aby automatycznie uzyskać wymaganych uprawnień.
    - Jeśli nie utworzono maszynę Wirtualną z portalu Marketplace lub jeśli korzystasz z programu SQL 2008 i 2008 R2, maszyna wirtualna nie może mieć SqlIaaSExtension zainstalowane, a operacja odnajdywania zakończy się niepowodzeniem z komunikatem o błędzie UserErrorSQLNoSysAdminMembership. Aby rozwiązać ten problem, postępuj zgodnie z instrukcjami zawartymi w sekcji [maszyn wirtualnych zestawu uprawnień](backup-azure-sql-database.md#set-vm-permissions).

        ![Wybieranie maszyny wirtualnej i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

1. W **cel kopii zapasowej** > **krok 2: Konfigurowanie tworzenia kopii zapasowej**, wybierz opcję **konfigurowania kopii zapasowej**.

   ![Wybieranie pozycji Konfiguruj kopię zapasową](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. W **wybierz elementy do wykonywania kopii zapasowych**, możesz zobaczyć wszystkie grupy dostępności zarejestrowanych i autonomicznego wystąpienia programu SQL Server. Wybierz strzałkę w lewo wiersz, aby rozwinąć listę wszystkich niechronionych baz danych w danym wystąpienie lub zawsze włączonej grupy dostępności.  

    ![Wyświetlanie wszystkich wystąpień programu SQL Server z autonomicznymi bazami danych](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Wybierz wszystkie bazy danych chcesz chronić, a następnie wybierz **OK**.

   ![Chronienie bazy danych](./media/backup-azure-sql-database/select-database-to-protect.png)

   Aby zoptymalizować obciążenia kopii zapasowych, usługa Azure Backup ustawia maksymalną liczbę baz danych w jednym zadaniu tworzenia kopii zapasowej na 50.

     * Aby chronić więcej niż 50 baz danych, skonfiguruj wiele kopii zapasowych.
     * Aby włączyć [ ](#enable-auto-protection) całe wystąpienie lub zawsze włączonej grupy dostępności. W **AUTOPROTECT** listy rozwijanej wybierz **ON**, a następnie wybierz pozycję **OK**.

    > [!NOTE]
    > [Automatycznej ochrony](#enable-auto-protection) funkcja nie tylko umożliwia ochronę wszystkich istniejących baz danych tylko raz, ale również automatycznie chronić wszelkie nowe bazy danych dodane do tego wystąpienia lub grupy dostępności.  

4. Wybierz **OK** otworzyć **zasady tworzenia kopii zapasowej**.

    ![Włączanie automatycznej ochrony dla grupy dostępności Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. W **zasady tworzenia kopii zapasowej**, wybierz zasady, a następnie wybierz **OK**.

   - Wybierz domyślną zasadę jako HourlyLogBackup.
   - Wybrać istniejące zasady kopii zapasowych utworzone wcześniej dla środowiska SQL.
   - Definiowanie nowych zasad opartych na swój zakres od celu punktu odzyskiwania i przechowywania.

     ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-azure-sql-database/select-backup-policy.png)

6. W **kopii zapasowej**, wybierz opcję **Włącz kopię zapasową**.

    ![Włączanie wybranych zasad kopii zapasowych](./media/backup-azure-sql-database/enable-backup-button.png)

7. Śledź postęp konfiguracji w **powiadomienia** obszaru portalu.

    ![Obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady kopii zapasowych określają, kiedy są tworzone kopie zapasowe i jak długo są przechowywane.

- Zasady są tworzone na poziomie magazynu.
- Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.
- Po utworzeniu zasad kopii zapasowych ustawieniem domyślnym będzie pełna kopia zapasowa tworzona codziennie.
- Możesz dodać różnicową kopię zapasową, ale tylko jeśli skonfigurujesz tworzenie pełnych kopii zapasowych co tydzień.
- Dowiedz się więcej o [różnego rodzaju zasadami tworzenia kopii zapasowych](backup-architecture.md#sql-server-backup-types).

Aby utworzyć nowe zasady kopii zapasowych:

1. W magazynie, wybierz **zasady tworzenia kopii zapasowych** > **Dodaj**.
2. W **Dodaj**, wybierz opcję **programu SQL Server na maszynie Wirtualnej platformy Azure** do definiowania typu zasad.

   ![Wybieranie typu nowych zasad kopii zapasowych](./media/backup-azure-sql-database/policy-type-details.png)

3. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.
4. W **zasad pełnej kopii zapasowej**, wybierz opcję **częstotliwość wykonywania kopii zapasowych**. Wybierz opcję **codzienne** lub **tygodniowy**.

   - W przypadku opcji **Codziennie** wybierz godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.
   - W przypadku opcji **Co tydzień** wybierz dzień tygodnia, godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.
   - Uruchom pełną kopię zapasową, ponieważ nie można wyłączyć **pełnej kopii zapasowej** opcji.
   - Wybierz **pełnej kopii zapasowej** Aby wyświetlić zasady.
   - Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.

     ![Pola nowych zasad kopii zapasowych](./media/backup-azure-sql-database/full-backup-policy.png)  

5. W **zakres przechowywania**, wszystkie opcje są wybrane domyślnie. Wyczyść dowolny zakres przechowywania ogranicza, że nie chcesz, a następnie ustaw interwały do użycia.

    - Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełnej, różnicowej i dziennika) wynosi siedem dni.
    - Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    - Tworzenie kopii zapasowej dla określonego dnia są oznaczone i przechowywane na podstawie co tydzień zakres przechowywania i co tydzień ustawienia przechowywania.
    - Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

       ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

6. W menu **zasad pełnej kopii zapasowej** wybierz pozycję **OK**, aby zaakceptować ustawienia.
7. Aby dodać zasady tworzenia różnicowej kopii zapasowej, wybierz pozycję **Różnicowa kopia zapasowa**.

   ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Otwieranie menu zasad różnicowej kopii zapasowej](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.

    - Można uruchomić tylko jedną różnicowa kopia zapasowa każdego dnia.
    - Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Dłuższy okres przechowywania można użyć w pełnych kopii zapasowych.

9. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

10. Aby dodać zasady kopii zapasowej dziennika transakcji, wybierz pozycję **Kopia zapasowa dziennika**.
11. W obszarze **Kopia zapasowa dziennika** wybierz pozycję **Włącz**, a następnie ustaw kontrolki częstotliwości i przechowywania. Kopie zapasowe dziennika, może wystąpić nawet co 15 minut i mogą być zachowywane przez maksymalnie 35 dni.
12. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

    ![Edytowanie zasad kopii zapasowej dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. W menu **Zasady kopii zapasowych** wybierz, czy chcesz włączyć **kompresję kopii zapasowej SQL**.
    - Kompresja jest domyślnie wyłączona.
    - Na zapleczu usługa Azure Backup używa natywnej kompresji kopii zapasowej SQL.

14. Po zakończeniu edycji zasad kopii zapasowych wybierz pozycję **OK**.


### <a name="modify-policy"></a>Modyfikowanie zasad
Zmodyfikuj zasady, aby zmienić zakres tworzenia kopii zapasowej częstotliwość lub przechowywania.

> [!NOTE]
> Każdej zmianie w okresie przechowywania będą dotyczyć wsteczną wszystkie starsze punkty odzyskiwania Oprócz deklarowania nowych.

Na pulpicie nawigacyjnym magazynu, przejdź do **Zarządzaj** > **zasady tworzenia kopii zapasowych** i wybierz zasady, którą chcesz edytować.

  ![Zarządzanie zasadami tworzenia kopii zapasowej](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Włączanie ochrony automatycznej  

Można włączyć ochrony automatycznej automatycznego wykonywania kopii zapasowej wszystkich istniejących i przyszłych baz danych, autonomiczne wystąpienie programu SQL Server lub zawsze włączonej grupy dostępności.

- Nie ma żadnego limitu liczby baz danych, które można wybrać do automatycznej ochrony w tym samym czasie.
- Nie można selektywnie chronić lub wykluczyć bazy danych z ochrony w wystąpieniu, w tym czasie włączyć automatyczną ochronę.
- Jeśli wystąpienie zawiera już niektóre chronionej bazy danych, będzie pozostaną chronione w ramach ich odpowiednich zasad nawet w przypadku, po włączeniu automatycznej ochrony. Wszystkie dodane później niechronionych baz danych będzie mieć tylko jedną zasadę, która należy zdefiniować podczas włączania ochrony automatycznej, na liście **konfigurowania kopii zapasowej**. Można jednak zmienić zasady skojarzone z bazą danych ochroną automatyczną później.  

Aby włączyć automatycznej ochrony:

  1. W obszarze **Wybieranie elementów do wykonania kopii zapasowej** wybierz wystąpienie, dla którego chcesz włączyć ochronę automatyczną.
  2. Wybierz z listy rozwijanej w obszarze **AUTOPROTECT**, wybierz **ON**, a następnie wybierz pozycję **OK**.

      ![Włączanie automatycznej ochrony dla grupy dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Tworzenie kopii zapasowych jest konfigurowane wspólnie dla wszystkich baz danych i można je śledzić w obszarze **Zadania kopii zapasowej**.

Jeśli potrzebujesz wyłączyć automatyczną ochronę, wybierz nazwę wystąpienia, w obszarze **konfigurowania kopii zapasowej**, a następnie wybierz **wyłączyć Autoprotect** dla tego wystąpienia. Wszystkie bazy danych będą w dalszym ciągu można utworzyć kopię zapasową, ale nie będą automatycznie chronione przyszłych baz danych.

![Wyłącz automatyczną ochronę w tym wystąpieniu](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Kolejne kroki

Instrukcje:

- [Przywracanie baz danych programu SQL Server kopii zapasowej](restore-sql-database-azure-vm.md)
- [Zarządzanie bazami danych programu SQL Server kopii zapasowej](manage-monitor-sql-database-backup.md)
