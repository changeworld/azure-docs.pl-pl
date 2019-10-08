---
title: Tworzenie kopii zapasowych baz danych SQL Server na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się, jak tworzyć kopie zapasowe baz danych SQL Server na maszynach wirtualnych platformy Azure
ms.reviewer: vijayts
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: dacurwin
ms.openlocfilehash: 847a4ec7da3c9b00753e5d07baf2952b31d2b5bb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934848"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure

Bazy danych SQL Server są obciążeniami krytycznymi, które wymagają niewielkiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową SQL Server baz danych uruchomionych na maszynach wirtualnych platformy Azure, korzystając z [Azure Backup](backup-overview.md).

W tym artykule pokazano, jak utworzyć kopię zapasową bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure z magazynem Azure Backup Recovery Services.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie i konfigurowanie magazynu.
> * Odkryj bazy danych i skonfiguruj kopie zapasowe.
> * Konfigurowanie ochrony automatycznej dla baz danych.


## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kopii zapasowej bazy danych SQL Server należy sprawdzić następujące kryteria:

1. Zidentyfikuj lub Utwórz [magazyn Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) w tym samym regionie lub ustawieniach regionalnych co maszyna wirtualna hostującym wystąpienie SQL Server.
2. Sprawdź, czy maszyna wirtualna ma [łączność sieciową](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Upewnij się, że bazy danych SQL Server są zgodne z [wytycznymi nazewnictwa bazy danych dla Azure Backup](#database-naming-guidelines-for-azure-backup).
4. Sprawdź, czy nie masz włączonych rozwiązań do tworzenia kopii zapasowych dla bazy danych. Przed utworzeniem kopii zapasowej bazy danych Wyłącz wszystkie inne SQL Server kopie zapasowe.

> [!NOTE]
> Możesz włączyć Azure Backup dla maszyny wirtualnej platformy Azure, a także dla SQL Serverj bazy danych działającej na maszynie wirtualnej bez konfliktu.


### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Dla wszystkich operacji maszyna wirtualna SQL Server wymaga łączności z publicznymi adresami IP platformy Azure. Operacje maszyny wirtualnej (Odnajdywanie bazy danych, konfigurowanie kopii zapasowych, Planowanie kopii zapasowych, przywracanie punktów odzyskiwania itd.) kończą się niepowodzeniem bez łączności z publicznymi adresami IP platformy Azure.

Ustanów łączność przy użyciu jednej z następujących opcji:

- **Zezwalaj na zakresy adresów IP centrum danych platformy Azure**. Ta opcja zezwala na pobieranie [zakresów adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) . Aby uzyskać dostęp do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), użyj polecenia cmdlet Set-AzureNetworkSecurityRule. Jeśli jesteś adresatami tylko adresów IP specyficznych dla regionu, musisz również zaktualizować Azure Active Directory listę bezpiecznych adresatów, aby włączyć uwierzytelnianie.

- **Zezwalaj na dostęp za pomocą tagów sieciowej grupy zabezpieczeń**.  Jeśli używasz sieciowej grupy zabezpieczeń do ograniczenia łączności, należy użyć znacznika usługi AzureBackup w celu zezwalania na dostęp wychodzący do Azure Backup. Ponadto należy również zezwolić na łączność z uwierzytelnianiem i transferem danych przy użyciu [reguł](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) dla usług Azure AD i Azure Storage. Można to zrobić za pomocą portalu lub programu PowerShell.

    Aby utworzyć regułę przy użyciu portalu:
    
    - W obszarze **wszystkie usługi**przejdź do pozycji **sieciowe grupy zabezpieczeń** i wybierz grupę zabezpieczeń sieci.
    - W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu wychodzącego** .
    - Wybierz pozycję **Dodaj**. Wprowadź wszystkie wymagane szczegóły dotyczące tworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Upewnij się, że opcja **miejsce docelowe** jest ustawiona na **tag usługi** i **znacznik usługi docelowej** jest ustawiony na **AzureBackup**.
    - Kliknij przycisk **Dodaj**, aby zapisać nowo utworzoną regułę zabezpieczeń dla ruchu wychodzącego.
    
   Aby utworzyć regułę przy użyciu programu PowerShell:

   - Dodawanie poświadczeń konta platformy Azure i aktualizowanie chmur narodowych<br/>
    ``Add-AzureRmAccount``
  - Wybierz subskrypcję usługi sieciowej grupy zabezpieczeń<br/>
    ``Select-AzureRmSubscription "<Subscription Id>"``
  - Wybierz sieciowej grupy zabezpieczeń<br/>
    ```$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"```
  - Dodaj regułę zezwalającą na ruch wychodzący dla tagu usługi Azure Backup<br/>
   ```Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"```
  - Zapisz sieciowej grupy zabezpieczeń<br/>
    ```Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg```

   
- **Zezwalaj na dostęp za pomocą tagów zapory platformy Azure**. Jeśli używasz zapory platformy Azure, Utwórz regułę aplikacji przy użyciu [znacznika FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)AzureBackup. Umożliwia to wychodzący dostęp do Azure Backup.
- **Wdróż serwer proxy HTTP, aby kierować ruchem**. Podczas tworzenia kopii zapasowej bazy danych SQL Server na maszynie wirtualnej platformy Azure rozszerzenie kopii zapasowej na maszynie wirtualnej używa interfejsów API HTTPS do wysyłania poleceń zarządzania do Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej używa także usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Rozszerzenia są jedynym składnikiem skonfigurowanym do uzyskiwania dostępu do publicznego Internetu.

Opcje łączności obejmują następujące zalety i wady:

**Option** | **Zalety** | **Wady**
--- | --- | ---
Zezwolenie na zakresy adresów IP | Brak dodatkowych kosztów | Skomplikowane do zarządzania, ponieważ zakresy adresów IP zmieniają się w czasie <br/><br/> Zapewnia dostęp do całości platformy Azure, a nie tylko usługi Azure Storage
Użyj tagów usługi sieciowej grupy zabezpieczeń | Łatwiejsze zarządzanie, ponieważ zmiany zakresu są automatycznie scalane <br/><br/> Brak dodatkowych kosztów <br/><br/> | Może być używany tylko z sieciowych grup zabezpieczeń <br/><br/> Zapewnia dostęp do całej usługi
Używanie tagów nazwy FQDN zapory platformy Azure | Łatwiejsze zarządzanie, ponieważ wymagane są automatycznie zarządzane nazwy FQDN | Może być używany tylko z zaporą platformy Azure
Używanie serwera proxy HTTP | Szczegółowa kontrola w serwerze proxy za pośrednictwem adresów URL magazynu jest dozwolona <br/><br/> Dostęp do maszyn wirtualnych w jednym punkcie dostępu do Internetu <br/><br/> Nie podlegają zmianom adresów IP platformy Azure | Dodatkowe koszty związane z uruchamianiem maszyny wirtualnej za pomocą oprogramowania serwera proxy

### <a name="database-naming-guidelines-for-azure-backup"></a>Wskazówki dotyczące nazewnictwa baz danych dla Azure Backup

Należy unikać używania następujących elementów w nazwach baz danych:

  * Spacje końcowe i początkowe
  * Końcowe znaki wykrzyknika (!)
  * Zamykające nawiasy kwadratowe (])
  * Średnik ";"
  * Ukośnik "/"

Aliasowanie jest dostępne dla nieobsługiwanych znaków, ale zalecamy ich uniknięcie. Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Jak odnajdywać bazy danych uruchomione na maszynie wirtualnej:

1. W [witrynie Azure Portal](https://portal.azure.com) otwórz magazyn usługi Recovery Services używany do tworzenia kopii zapasowych bazy danych.

2. Na pulpicie nawigacyjnym **magazynu Recovery Services** wybierz pozycję **kopia zapasowa**.

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W obszarze **cel kopii zapasowej**ustaw, **gdzie działa Twoje obciążenie?** na **platformę Azure**.

4. W obszarze **Co ma zawierać kopia zapasowa** wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. W obszarze **Cel kopii zapasowej** > **Odnajdywanie baz danych na maszynach wirtualnych** wybierz pozycję **Uruchom odnajdywanie**, aby wyszukać niechronione maszyny wirtualne w subskrypcji. To wyszukiwanie może chwilę potrwać, w zależności od liczby niechronionych maszyn wirtualnych w subskrypcji.

   - Niechronione maszyny wirtualne powinny zostać wyświetlone na liście po odnalezieniu, uporządkowane według nazwy i grupy zasobów.
   - Jeśli maszyna wirtualna nie jest wyświetlana w oczekiwany sposób, sprawdź, czy kopia zapasowa została już utworzona w magazynie.
   - Wiele maszyn wirtualnych może mieć taką samą nazwę, ale będą należeć do różnych grup zasobów.

     ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na liście maszyn wirtualnych wybierz maszynę wirtualną z uruchomioną bazą danych programu SQL Server i wybierz pozycję **Odnajdź bazy danych**.

7. Śledź odnajdywanie bazy danych w **powiadomieniach**. Czas wymagany dla tej akcji zależy od liczby baz danych maszyn wirtualnych. Po odnalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Usługa Azure Backup odnajduje wszystkie bazy danych programu SQL Server na maszynie wirtualnej. Podczas odnajdywania następujące elementy są wykonywane w tle:

    - Azure Backup rejestruje maszynę wirtualną z magazynem na potrzeby tworzenia kopii zapasowych obciążeń. Kopie zapasowe wszystkich baz danych na zarejestrowanej maszynie wirtualnej można tworzyć tylko w tym magazynie.
    - Azure Backup instaluje rozszerzenie AzureBackupWindowsWorkload na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych SQL.
    - Azure Backup tworzy konto usługi NT Service\AzureWLBackupPluginSvc na maszynie wirtualnej.
      - Wszystkie operacje tworzenia kopii zapasowych i przywracania korzystają z konta usługi.
      - NT Service\AzureWLBackupPluginSvc wymaga uprawnień administratora systemu SQL. Wszystkie SQL Server maszyny wirtualne utworzone w portalu Marketplace pochodzą z zainstalowanym SqlIaaSExtension. Rozszerzenie AzureBackupWindowsWorkload używa SQLIaaSExtension do automatycznego pobierania wymaganych uprawnień.
    - Jeśli maszyna wirtualna nie została utworzona z poziomu portalu Marketplace lub jeśli korzystasz z programu SQL 2008 i 2008 R2, maszyna wirtualna może nie mieć zainstalowanych SqlIaaSExtension i operacja odnajdywania zakończy się niepowodzeniem z komunikatem o błędzie UserErrorSQLNoSysAdminMembership. Aby rozwiązać ten problem, postępuj zgodnie z instrukcjami w obszarze [Ustaw uprawnienia maszyny wirtualnej](backup-azure-sql-database.md#set-vm-permissions).

        ![Wybieranie maszyny wirtualnej i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

1. W obszarze **cel kopii zapasowej** > **krok 2: Skonfiguruj kopię zapasową**, wybierz pozycję **Konfiguruj kopię zapasową**.

   ![Wybieranie pozycji Konfiguruj kopię zapasową](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**są wyświetlane wszystkie zarejestrowane grupy dostępności i autonomiczne wystąpienia SQL Server. Wybierz strzałkę z lewej strony wiersza, aby rozwinąć listę wszystkich niechronionych baz danych w tym wystąpieniu lub zawsze włączona Grupa dostępności.  

    ![Wyświetlanie wszystkich wystąpień programu SQL Server z autonomicznymi bazami danych](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Wybierz wszystkie bazy danych, które chcesz chronić, a następnie wybierz przycisk **OK**.

   ![Chronienie bazy danych](./media/backup-azure-sql-database/select-database-to-protect.png)

   Aby zoptymalizować obciążenia kopii zapasowych, usługa Azure Backup ustawia maksymalną liczbę baz danych w jednym zadaniu tworzenia kopii zapasowej na 50.

     * Aby chronić więcej niż 50 baz danych, skonfiguruj wiele kopii zapasowych.
     * Aby [włączyć](#enable-auto-protection) całe wystąpienie lub zawsze włączona Grupa dostępności, na liście rozwijanej **Autoochrona** wybierz pozycję **włączone**, a następnie wybierz przycisk **OK**.

    > [!NOTE]
    > Funkcja [automatycznej ochrony](#enable-auto-protection) nie tylko włącza ochronę wszystkich istniejących baz danych, ale również automatycznie chroni wszystkie nowe bazy danych dodane do tego wystąpienia lub grupy dostępności.  

4. Wybierz **przycisk OK** , aby otworzyć przystawkę **zasady kopii zapasowych**.

    ![Włącz funkcję autoochrony dla grupy dostępności Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. W obszarze **zasady tworzenia kopii zapasowych**wybierz zasady, a następnie wybierz przycisk **OK**.

   - Wybierz zasady domyślne jako HourlyLogBackup.
   - Wybrać istniejące zasady kopii zapasowych utworzone wcześniej dla środowiska SQL.
   - Zdefiniuj nowe zasady na podstawie celu punktu odzyskiwania i zakresu przechowywania.

     ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-azure-sql-database/select-backup-policy.png)

6. W obszarze **kopia zapasowa**wybierz pozycję **Włącz kopię zapasową**.

    ![Włączanie wybranych zasad kopii zapasowych](./media/backup-azure-sql-database/enable-backup-button.png)

7. Śledź postęp konfiguracji w obszarze **Powiadomienia** portalu.

    ![Obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady kopii zapasowych określają, kiedy są tworzone kopie zapasowe i jak długo są przechowywane.

- Zasady są tworzone na poziomie magazynu.
- Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.
- Po utworzeniu zasad kopii zapasowych ustawieniem domyślnym będzie pełna kopia zapasowa tworzona codziennie.
- Możesz dodać różnicową kopię zapasową, ale tylko jeśli skonfigurujesz tworzenie pełnych kopii zapasowych co tydzień.
- Dowiedz się więcej o [różnych typach zasad tworzenia kopii zapasowych](backup-architecture.md#sql-server-backup-types).

Aby utworzyć nowe zasady kopii zapasowych:

1. W magazynie wybierz pozycję >  **zasady tworzenia kopii zapasowych** **Dodaj**.
2. W obszarze **Dodaj**wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure** , aby zdefiniować typ zasad.

   ![Wybieranie typu nowych zasad kopii zapasowych](./media/backup-azure-sql-database/policy-type-details.png)

3. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.
4. W obszarze **zasady pełnej kopii zapasowej**wybierz **częstotliwość tworzenia kopii zapasowych**. Wybierz opcję **codziennie** lub **co tydzień**.

   - W przypadku opcji **Codziennie** wybierz godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.
   - W przypadku opcji **Co tydzień** wybierz dzień tygodnia, godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.
   - Uruchom pełną kopię zapasową, ponieważ nie można wyłączyć opcji **pełnej kopii zapasowej** .
   - Wybierz pozycję **pełna kopia zapasowa** , aby wyświetlić zasady.
   - Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.

     ![Pola nowych zasad kopii zapasowych](./media/backup-azure-sql-database/full-backup-policy.png)  

5. W obszarze **Zakres przechowywania**wszystkie opcje są domyślnie zaznaczone. Wyczyść limity zakresu przechowywania, które nie są potrzebne, a następnie ustaw interwały, które mają być używane.

    - Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny, różnicowa i log) wynosi siedem dni.
    - Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    - Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania oraz ustawienia przechowywania tygodniowego.
    - Zakresy przechowywania miesięczne i roczne działają w podobny sposób.

       ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

6. W menu **zasad pełnej kopii zapasowej** wybierz pozycję **OK**, aby zaakceptować ustawienia.
7. Aby dodać zasady tworzenia różnicowej kopii zapasowej, wybierz pozycję **Różnicowa kopia zapasowa**.

   ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Otwieranie menu zasad różnicowej kopii zapasowej](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.

    - Można wyzwolić tylko jedną różnicową kopię zapasową dziennie.
    - Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Aby przechować więcej, użyj pełnych kopii zapasowych.

9. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

10. Aby dodać zasady kopii zapasowej dziennika transakcji, wybierz pozycję **Kopia zapasowa dziennika**.
11. W obszarze **Kopia zapasowa dziennika** wybierz pozycję **Włącz**, a następnie ustaw kontrolki częstotliwości i przechowywania. Kopie zapasowe dzienników mogą odbywać się przez co 15 minut i mogą być przechowywane przez maksymalnie 35 dni.
12. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

    ![Edytowanie zasad kopii zapasowej dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. W menu **Zasady kopii zapasowych** wybierz, czy chcesz włączyć **kompresję kopii zapasowej SQL**.
    - Kompresja jest domyślnie wyłączona.
    - Na zapleczu usługa Azure Backup używa natywnej kompresji kopii zapasowej SQL.

14. Po zakończeniu edycji zasad kopii zapasowych wybierz pozycję **OK**.

## <a name="enable-auto-protection"></a>Włączanie ochrony automatycznej  

Można włączyć automatyczną ochronę, aby automatycznie tworzyć kopie zapasowe wszystkich istniejących i przyszłych baz danych w autonomicznym wystąpieniu SQL Server lub do zawsze włączonej grupy dostępności.

- Nie ma żadnego limitu liczby baz danych, które można wybrać do ochrony w tym samym czasie.
- Nie można wybiórczo chronić ani wykluczać baz danych z ochrony w wystąpieniu w momencie włączenia ochrony autoprotection.
- Jeśli wystąpienie zawiera już pewne chronione bazy danych, pozostaną one chronione w ramach odpowiednich zasad nawet po włączeniu ochrony autoprotection. Wszystkie niechronione bazy danych dodane później będą mieć tylko pojedyncze zasady zdefiniowane w momencie włączania ochrony automatycznie, wymienione w obszarze **Konfiguruj kopię zapasową**. Można jednak później zmienić zasady skojarzone z chronioną bazą danych.  

Aby włączyć ochronę autoprotection:

  1. W obszarze **Wybieranie elementów do wykonania kopii zapasowej** wybierz wystąpienie, dla którego chcesz włączyć ochronę automatyczną.
  2. Wybierz listę rozwijaną w obszarze **Autoochrona**, wybierz pozycję **włączone**, a następnie wybierz przycisk **OK**.

      ![Włącz funkcję autoochrony w grupie dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Tworzenie kopii zapasowych jest konfigurowane wspólnie dla wszystkich baz danych i można je śledzić w obszarze **Zadania kopii zapasowej**.

Jeśli musisz wyłączyć funkcję autoochrony, wybierz nazwę wystąpienia w obszarze **Konfiguruj kopię zapasową**, a następnie wybierz opcję **Wyłącz autoochronę** dla tego wystąpienia. Kopie zapasowe wszystkich baz danych zostaną zachowane, ale przyszłe bazy danych nie będą chronione automatycznie.

![Wyłącz autoochronę dla tego wystąpienia](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Następne kroki

Instrukcje:

- [Przywracanie kopii zapasowych SQL Server baz danych](restore-sql-database-azure-vm.md)
- [Zarządzanie kopiami zapasowymi baz danych SQL Server](manage-monitor-sql-database-backup.md)
