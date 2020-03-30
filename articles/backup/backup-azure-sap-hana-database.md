---
title: Tworzenie kopii zapasowej bazy danych SAP HANA na platformie Azure za pomocą usługi Azure Backup
description: W tym artykule dowiesz się, jak wykonać kopię zapasową bazy danych SAP HANA na maszynach wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248062"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure

Bazy danych SAP HANA są krytycznymi obciążeniami, które wymagają celu niskiego punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można wykonać kopię zapasową baz danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure przy użyciu [usługi Azure Backup.](backup-overview.md)

W tym artykule pokazano, jak wykonać kopię zapasową baz danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure do magazynu usług Azure Backup Recovery Services.

W tym artykule dowiesz się, jak:
> [!div class="checklist"]
>
> * Tworzenie i konfigurowanie przechowalni
> * Odnajduj bazy danych
> * Konfigurowanie kopii zapasowych
> * Uruchamianie zadania tworzenia kopii zapasowej na żądanie

>[!NOTE]
>**Usuwanie trędowak dla serwera SQL w usłudze Azure VM i usuwanie nietrwałe dla sap HANA w obciążeniach maszyn wirtualnych platformy Azure** jest teraz dostępne w wersji zapoznawczej.<br>
>Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i [sekcjami Co skrypt rejestracji wstępnej,](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) aby skonfigurować bazę danych do tworzenia kopii zapasowych.

### <a name="set-up-network-connectivity"></a>Konfigurowanie łączności sieciowej

Dla wszystkich operacji SAP HANA VM wymaga łączności z publicznych adresów IP platformy Azure. Operacje maszyn wirtualnych (odnajdowanie bazy danych, konfigurowanie kopii zapasowych, planowanie tworzenia kopii zapasowych, przywracanie punktów odzyskiwania itd.) kończą się niepowodzeniem bez łączności z publicznymi adresami IP platformy Azure.

Nawiązuj łączność przy użyciu jednej z następujących opcji:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Zezwalaj na zakresy adresów IP centrum danych platformy Azure

Ta opcja umożliwia [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) w pobranym pliku. Aby uzyskać dostęp do sieciowej grupy zabezpieczeń (NSG), należy użyć polecenia cmdlet Set-AzureNetworkSecurityRule. Jeśli lista bezpiecznych adresatów zawiera tylko adresy IP specyficzne dla regionu, należy również zaktualizować listę bezpiecznych adresatów tagu usługi Azure Active Directory (Azure AD), aby włączyć uwierzytelnianie.

#### <a name="allow-access-using-nsg-tags"></a>Zezwalaj na dostęp przy użyciu znaczników NSG

Jeśli używasz sieciowej sieciowej sieciowej do ograniczania łączności, należy użyć tagu usługi AzureBackup, aby zezwalać na dostęp wychodzący do usługi Azure Backup. Ponadto należy również zezwolić na łączność dla uwierzytelniania i transferu danych przy użyciu [reguł](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) dla usługi Azure AD i usługi Azure Storage. Można to zrobić za pośrednictwem witryny Azure portal lub za pośrednictwem programu PowerShell.

Aby utworzyć regułę za pomocą portalu:

  1. W **obszarze Wszystkie usługi**przejdź do grup zabezpieczeń **Sieć** i wybierz grupę zabezpieczeń sieci.
  2. Wybierz **pozycję Reguły zabezpieczeń wychodzących** w obszarze **Ustawienia**.
  3. Wybierz pozycję **Dodaj**. Wprowadź wszystkie szczegóły wymagane do utworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Upewnij się, że opcja **Miejsce docelowe** jest **ustawiona** na Tag usługi i **Tag usługi docelowej** jest ustawiony na **AzureBackup**.
  4. Kliknij **przycisk Dodaj**, aby zapisać nowo utworzoną regułę zabezpieczeń wychodzących.

Aby utworzyć regułę przy użyciu programu PowerShell:

 1. Dodawanie poświadczeń konta platformy Azure i aktualizowanie chmur krajowych<br/>
      `Add-AzureRmAccount`<br/>

 2. Wybierz subskrypcję nsg<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Wybierz nsg<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Dodaj regułę zezwalania na ruch wychodzący dla tagu usługi Azure Backup<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Dodaj regułę zezwalania na ruch wychodzący dla tagu usługi Magazyn<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Dodaj regułę zezwalania na ruch wychodzący dla tagu usługi AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Zapisz nsg<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Zezwól na dostęp przy użyciu tagów Zapory platformy Azure**. Jeśli używasz Zapory platformy Azure, utwórz regułę aplikacji przy użyciu [tagu FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)usługi AzureBackup . Dzięki temu dostęp wychodzący do usługi Azure Backup.

**Wdrażanie serwera proxy HTTP w celu kierowania ruchu**. Podczas tworzenia kopii zapasowej bazy danych SAP HANA na maszynie Wirtualnej platformy Azure rozszerzenie kopii zapasowej na maszynie Wirtualnej używa interfejsów API HTTPS do wysyłania poleceń zarządzania do usługi Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej używa również usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Rozszerzenia są jedynym składnikiem skonfigurowanym do uzyskiwania dostępu do publicznego internetu.

Opcje łączności obejmują następujące zalety i wady:

**Opcja** | **Zalety** | **Wady**
--- | --- | ---
Zezwolenie na zakresy adresów IP | Brak dodatkowych kosztów | Skomplikowane zarządzanie, ponieważ zakresy adresów IP zmieniają się w czasie <br/><br/> Zapewnia dostęp do całej platformy Azure, nie tylko usługi Azure Storage
Używanie tagów usług NSG | Łatwiejsze zarządzanie, ponieważ zmiany zakresu są automatycznie scalane <br/><br/> Brak dodatkowych kosztów <br/><br/> | Może być używany tylko z nsgs <br/><br/> Zapewnia dostęp do całej usługi
Używanie tagów FQDN zapory platformy Azure | Łatwiejsze zarządzanie, ponieważ wymagane nazwy FQDN są automatycznie zarządzane | Może być używany tylko z Zaporą platformy Azure
Używanie serwera proxy HTTP | Szczegółowa kontrola serwera proxy nad adresami URL magazynu jest dozwolona <br/><br/> Pojedynczy punkt dostępu do Internetu dla maszyn wirtualnych <br/><br/> Nie podlega zmianom adresu IP platformy Azure | Dodatkowe koszty uruchomienia maszyny wirtualnej z oprogramowaniem proxy

#### <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Odnajduj bazy danych

1. W przechowalni w obszarze **Wprowadzenie**kliknij pozycję **Kopia zapasowa**. W **gdzie jest uruchomione obciążenie?**, wybierz SAP **HANA w usłudze Azure VM**.
2. Kliknij **pozycję Rozpocznij odnajdowanie**. Inicjuje to odnajdowanie niechronionych maszyn wirtualnych z systemem Linux w regionie przechowalni.

   * Po odnajdowaniu niechronione maszyny wirtualne są wyświetlane w portalu, wymienione według nazwy i grupy zasobów.
   * Jeśli maszyna wirtualna nie jest wymieniona zgodnie z oczekiwaniami, sprawdź, czy jest już utworzona kopia zapasowa w magazynie.
   * Wiele maszyn wirtualnych może mieć taką samą nazwę, ale należą do różnych grup zasobów.

3. W **wybierz maszyny wirtualne**kliknij łącze, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup, aby uzyskać dostęp do maszyn wirtualnych SAP HANA do odnajdowania bazy danych.
4. Uruchom skrypt na każdej maszynie wirtualnej obsługującej bazy danych SAP HANA, których chcesz poprzeć.
5. Po uruchomieniu skryptu na maszynach wirtualnych w **obszarze Wybierz maszyny wirtualne**wybierz maszyny wirtualne. Następnie kliknij przycisk **Odkryj kule.**
6. Usługa Azure Backup odnajduje wszystkie bazy danych SAP HANA na maszynie Wirtualnej. Podczas odnajdywania usługa Azure Backup rejestruje maszynę wirtualną z magazynem i instaluje rozszerzenie na maszynie Wirtualnej. Żaden agent nie jest zainstalowany w bazie danych.

    ![Odnajduj bazy danych SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

Teraz włącz tworzenie kopii zapasowych.

1. W kroku 2 kliknij pozycję **Konfiguruj kopię zapasową**.

    ![Skonfiguruj kopię zapasową](./media/backup-azure-sap-hana-database/configure-backup.png)
2. W **obszarze Wybierz elementy do utworzenia kopii zapasowej**wybierz wszystkie bazy danych, które chcesz chronić > **OK**.

    ![Wybieranie elementów do utworzenia kopii zapasowej](./media/backup-azure-sap-hana-database/select-items.png)
3. W **obszarze Zasady** > tworzenia kopii**zapasowych Wybierz zasady tworzenia kopii zapasowych**, utwórz nową zasadę tworzenia kopii zapasowych dla baz danych, zgodnie z poniższymi instrukcjami.

    ![Wybieranie zasad tworzenia kopii zapasowych](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Po utworzeniu zasad w menu **Kopia zapasowa** kliknij polecenie **Włącz tworzenie kopii zapasowej**.

    ![Włączanie tworzenia kopii zapasowych](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Śledzenie postępu konfiguracji kopii zapasowej w obszarze **Powiadomienia** portalu.

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają, kiedy kopie zapasowe są pobierane i jak długo są zachowywane.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ następujące ustawienia zasad:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.

   ![Wprowadź nazwę zasad](./media/backup-azure-sap-hana-database/policy-name.png)
2. W obszarze **Zasady pełnej kopii zapasowej** wybierz opcję **Częstotliwość tworzenia kopii zapasowej** (**Codziennie** lub **Co tydzień**).
   * **Codziennie:** Wybierz godzinę i strefę czasową, w której rozpoczyna się zadanie tworzenia kopii zapasowej.
       * Należy uruchomić pełną kopię zapasową. Nie można wyłączyć tej opcji.
       * Kliknij pozycję **Pełna kopia zapasowa**, aby wyświetlić zasady.
       * Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.
   * **Co tydzień:** wybierz dzień tygodnia, godzinę i strefę czasową, w której jest uruchamiane zadanie tworzenia kopii zapasowej.

   ![Wybieranie częstotliwości tworzenia kopii zapasowych](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. W **obszarze Zakres przechowywania**skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
    * Domyślnie wszystkie opcje są zaznaczone. Wyczyść wszystkie limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które chcesz wykonać.
    * Minimalny okres przechowywania dla każdego typu kopii zapasowej (pełna/różnicowa/dziennik) wynosi siedem dni.
    * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    * Kopia zapasowa dla określonego dnia jest oznaczana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
    * Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

4. W menu **Zasady pełnej kopii zapasowej** kliknij przycisk **OK,** aby zaakceptować ustawienia.
5. Wybierz **opcję Kopia zapasowa różnicowa,** aby dodać zasady różnicowe.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.
    * Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.
    * Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    ![Zasady różnicowego tworzenia kopii zapasowych](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Przyrostowe kopie zapasowe nie są obecnie obsługiwane.

7. Kliknij **przycisk OK,** aby zapisać zasady i powrócić do głównego menu **zasad kopii zapasowej.**
8. Wybierz **log backup,** aby dodać zasady tworzenia kopii zapasowych dziennika transakcyjnego,
    * W **obszarze Kopia zapasowa**dziennika wybierz pozycję **Włącz**.  Nie można tego wyłączyć, ponieważ sap HANA zarządza wszystkimi kopiami zapasowymi dziennika.
    * Ustaw kontrolki częstotliwości i przechowywania.

    > [!NOTE]
    > Kopie zapasowe dziennika zaczynają przepływać dopiero po pomyślnym zakończeniu pełnej kopii zapasowej.

9. Kliknij **przycisk OK,** aby zapisać zasady i powrócić do głównego menu **zasad kopii zapasowej.**
10. Po zakończeniu definiowania zasad tworzenia kopii zapasowych kliknij przycisk **OK**.

> [!NOTE]
> Każda kopia zapasowa dziennika jest przywiązywalem do poprzedniej pełnej kopii zapasowej w celu utworzenia łańcucha odzyskiwania. Ta pełna kopia zapasowa zostanie zachowana do czasu wygaśnięcia przechowywania ostatniej kopii zapasowej dziennika. Może to oznaczać, że pełna kopia zapasowa jest przechowywana przez dodatkowy okres, aby upewnić się, że wszystkie dzienniki można odzyskać. Załóżmy, że użytkownik ma cotygodniową pełną kopię zapasową, dzienną różnicę i 2-godzinne dzienniki. Wszystkie z nich są przechowywane przez 30 dni. Ale, tygodniowy pełny może być naprawdę oczyszczone / usunięte tylko po następnej pełnej kopii zapasowej jest dostępna, czyli po 30 + 7 dni. Powiedzmy, że cotygodniowa pełna kopia zapasowa ma miejsce 16 listopada. Zgodnie z zasadami przechowywania, należy zachować do 16 grudnia. Ostatnia kopia zapasowa dziennika dla tej pełnej dzieje się przed następnym zaplanowane pełne, na 22 listopada. Dopóki ten dziennik nie będzie dostępny do 22 grudnia, 16 listopada nie można usunąć pełnej. Tak, 16 listopada pełne jest zachowane do 22 grudnia.

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:

1. W menu przechowalni kliknij polecenie **Zapasy elementów**.
2. W **obszarze Elementy kopii zapasowej**wybierz maszynę wirtualną z uruchomieniem bazy danych SAP HANA, a następnie kliknij przycisk Kopia **zapasowa teraz**.
3. W **obszarze Kopia zapasowa teraz**użyj formantu kalendarza, aby wybrać ostatni dzień, w którym punkt odzyskiwania powinien zostać zachowany. Następnie kliknij przycisk **OK**.
4. Monitorowanie powiadomień portalu. Postęp zadania na pulpicie nawigacyjnym magazynu można monitorować > **Zadania** > kopii zapasowej**w toku**. W zależności od rozmiaru bazy danych utworzenie początkowej kopii zapasowej może chwilę potrwać.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Uruchamianie kopii zapasowej SAP HANA Studio w bazie danych z włączoną obsługą usługi Azure Backup

Jeśli chcesz wykonać lokalną kopię zapasową (przy użyciu HANA Studio) bazy danych, której kopię zapasową jest kopia zapasowa za pomocą usługi Azure Backup, wykonaj następujące czynności:

1. Poczekaj na pełne lub dziennikowe kopie zapasowe dla bazy danych, aby zakończyć. Sprawdź stan w SAP HANA Studio / Cockpit.
2. Wyłącz kopie zapasowe dziennika i ustaw katalog kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
3. Aby to zrobić, kliknij dwukrotnie opcję **Systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
4. Ustaw **enable_auto_log_backup** na **Nie**.
5. Ustaw **log_backup_using_backint** na **False**.
6. Wykonaj pełną kopię zapasową bazy danych na żądanie.
7. Poczekaj na zakończenie pełnej kopii zapasowej i katalogu.
8. Przywróć poprzednie ustawienia do tych dla platformy Azure:
    * Ustaw **enable_auto_log_backup** **tak**.
    * Ustaw **log_backup_using_backint** **true**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się, jak [zarządzać bazami danych SAP HANA, których kopia zapasowa jest archiwizna przy użyciu usługi Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
