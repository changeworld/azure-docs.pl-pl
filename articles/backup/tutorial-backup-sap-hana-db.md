---
title: Samouczek — tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure
description: W tym samouczku dowiesz się, jak wykonać kopię zapasową baz danych SAP HANA uruchomionych na maszynie Wirtualnej platformy Azure w magazynie usług odzyskiwania kopii zapasowych platformy Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f64dd74ad0e038c5cad152e20ae2255de03114e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501454"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Samouczek: Tworzenie kopii zapasowych baz danych SAP HANA na maszynie Wirtualnej platformy Azure

W tym samouczku pokazano, jak wykonać kopię zapasową baz danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure do magazynu usług Azure Backup Recovery Services. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
>
> * Tworzenie i konfigurowanie przechowalni
> * Odnajduj bazy danych
> * Konfigurowanie kopii zapasowych

[Oto](sap-hana-backup-support-matrix.md#scenario-support) wszystkie scenariusze, które obecnie obsługujemy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem kopii zapasowych należy wykonać następujące czynności:

* Zezwalaj na łączność z maszyną wirtualną z Internetem, aby mogła dotrzeć do platformy Azure, zgodnie z opisem w poniższej procedurze [konfigurowania łączności sieciowej.](#set-up-network-connectivity)
* Klucz powinien istnieć w **magazynie hdbuser,** który spełnia następujące kryteria:
  * Powinien być obecny w domyślnym **sklepie hdbuserstore**
  * W przypadku mdc klucz powinien wskazywać port SQL **programu NAMESERVER**. W przypadku SDC należy wskazać port SQL **indexserver**
  * Powinien mieć poświadczenia, aby dodawać i usuwać użytkowników
* Uruchom skrypt konfiguracji kopii zapasowej SAP HANA (skrypt rejestracji wstępnej) na maszynie wirtualnej, na której jest zainstalowany hana, jako użytkownik root. [Ten skrypt](https://aka.ms/scriptforpermsonhana) umożliwia przygotowanie systemu HANA do tworzenia kopii zapasowych. Zapoznaj się z [sekcją Co skrypt rejestracji wstępnej robi,](#what-the-pre-registration-script-does) aby dowiedzieć się więcej o skrypcie rejestracji wstępnej.

## <a name="set-up-network-connectivity"></a>Konfigurowanie łączności sieciowej

Dla wszystkich operacji SAP HANA VM wymaga łączności z publicznych adresów IP platformy Azure. Operacje maszyn wirtualnych (odnajdowanie bazy danych, konfigurowanie kopii zapasowych, planowanie tworzenia kopii zapasowych, przywracanie punktów odzyskiwania itd.) kończą się niepowodzeniem bez łączności z publicznymi adresami IP platformy Azure.

Nawiązuj łączność przy użyciu jednej z następujących opcji:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Zezwalaj na zakresy adresów IP centrum danych platformy Azure

Ta opcja umożliwia [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) w pobranym pliku. Aby uzyskać dostęp do sieciowej grupy zabezpieczeń (NSG), należy użyć polecenia cmdlet Set-AzureNetworkSecurityRule. Jeśli lista bezpiecznych adresatów zawiera tylko adresy IP specyficzne dla regionu, należy również zaktualizować listę bezpiecznych adresatów tagu usługi Azure Active Directory (Azure AD), aby włączyć uwierzytelnianie.

### <a name="allow-access-using-nsg-tags"></a>Zezwalaj na dostęp przy użyciu znaczników NSG

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

## <a name="what-the-pre-registration-script-does"></a>Co robi skrypt rejestracji wstępnej

Uruchomienie skryptu rejestracji wstępnej wykonuje następujące funkcje:

* Instaluje lub aktualizuje wszystkie niezbędne pakiety wymagane przez agenta usługi Azure Backup w twojej dystrybucji.
* Przeprowadza kontrole połączeń wychodzących z siecią wychodzącą z serwerami usługi Azure Backup i usługami zależnymi, takimi jak usługa Azure Active Directory i usługa Azure Storage.
* Loguje się do systemu HANA przy użyciu klucza użytkownika wymienionego jako część [wymagań wstępnych.](#prerequisites) Klucz użytkownika jest używany do tworzenia kopii zapasowej użytkownika (AZUREWLBACKUPHANAUSER) w systemie HANA i klucz użytkownika można usunąć po pomyślnym uruchomieniu skryptu rejestracji wstępnej.
* AzureWLBACKUPHANAUSER jest przypisany te wymagane role i uprawnienia:
  * DATABASE ADMIN (w przypadku MDC) i BACKUP ADMIN (w przypadku SDC): aby utworzyć nowe bazy danych podczas przywracania.
  * CATALOG READ: aby odczytać katalog kopii zapasowych.
  * SAP_INTERNAL_HANA_SUPPORT: dostęp do kilku prywatnych stołów.
* Skrypt dodaje klucz do **magazynu hdbuser** dla AZUREWLBACKUPHANAUSER dla wtyczki kopii zapasowej HANA do obsługi wszystkich operacji (kwerendy bazy danych, operacje przywracania, konfigurowanie i uruchamianie kopii zapasowej).

>[!NOTE]
> Klucz użytkownika wymieniony jako część [wymagań wstępnych](#prerequisites) można jawnie przekazać jako parametr do skryptu rejestracji wstępnej:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Aby dowiedzieć się, jakie inne parametry akceptuje skrypt, użyj polecenia`bash msawb-plugin-config-com-sap-hana.sh --help`

Aby potwierdzić tworzenie klucza, uruchom polecenie HDBSQL na komputerze HANA z poświadczeniami SIDADM:

```hdbsql
hdbuserstore list
```

Dane wyjściowe polecenia powinny być wyświetlane {SID}{DBNAME} klucz, z użytkownikiem pokazany jako AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Upewnij się, że masz unikatowy zestaw `/usr/sap/{SID}/home/.hdb/`plików SSFS w obszarze . W tej ścieżce powinien znajdować się tylko jeden folder.

## <a name="create-a-recovery-service-vault"></a>Tworzenie magazynu usługi odzyskiwania

Magazyn usług odzyskiwania to jednostka, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn usług odzyskiwania zawiera również zasady tworzenia kopii zapasowych skojarzone z chronionymi maszynami wirtualnymi.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz **pozycję Wszystkie usługi**

   ![Wybieranie pozycji Wszystkie usługi](./media/tutorial-backup-sap-hana-db/all-services.png)

3. W oknie dialogowym **Wszystkie usługi** wprowadź frazę **Recovery Services**. Lista filtrów zasobów zgodnie z wejściem. Na liście zasobów wybierz pozycję **Magazyny usług odzyskiwania**.

   ![Wybieranie magazynów usług odzyskiwania](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Na **pulpicie** nawigacyjnym magazynów usług odzyskiwania wybierz pozycję **Dodaj**.

   ![Dodawanie magazynu usług odzyskiwania](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Zostanie otwarte okno dialogowe **Magazyn usług odzyskiwania.** Podaj wartości **nazwy, subskrypcji, grupy zasobów** i **lokalizacji**

   ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Nazwa**: Nazwa jest używana do identyfikowania magazynu usług odzyskiwania i musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa, ale nie więcej niż 50 znaków. Nazwa musi zaczynać się od litery i składać się tylko z liter, cyfr i łączników. W tym samouczku użyliśmy nazwy **SAPHanaVault**.
   * **Subskrypcja**: Wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure. W tym miejscu użyliśmy **subskrypcji laboratorium rozwiązania SAP HANA.**
   * **Grupa zasobów:** Użyj istniejącej grupy zasobów lub utwórz nową. Tutaj użyliśmy **SAPHANADemo**.<br>
   Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącego**, a następnie wybierz zasób z pola listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nowy** i wprowadź jej nazwę. Aby uzyskać pełne informacje o grupach zasobów, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Lokalizacja**: Wybierz region geograficzny przechowalni. Magazyn musi znajdować się w tym samym regionie co maszyna wirtualna z systemem SAP HANA. Użyliśmy **East US 2**.

5. Wybierz **pozycję Recenzja + Utwórz**.

   ![Wybierz pozycję Przejrzyj & utwórz](./media/tutorial-backup-sap-hana-db/review-create.png)

Magazyn usług odzyskiwania jest teraz tworzony.

## <a name="discover-the-databases"></a>Odnajduj bazy danych

1. W przechowalni w obszarze **Wprowadzenie**kliknij pozycję **Kopia zapasowa**. W **gdzie jest uruchomione obciążenie?**, wybierz SAP **HANA w usłudze Azure VM**.
2. Kliknij **pozycję Rozpocznij odnajdowanie**. Inicjuje to odnajdowanie niechronionych maszyn wirtualnych z systemem Linux w regionie przechowalni. Zostanie wyświetlona maszyna wirtualna platformy Azure, którą chcesz chronić.
3. W **wybierz maszyny wirtualne**kliknij łącze, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup, aby uzyskać dostęp do maszyn wirtualnych SAP HANA do odnajdowania bazy danych.
4. Uruchom skrypt na maszynach wirtualnych obsługujących bazy danych SAP HANA, których chcesz poprzeć.
5. Po uruchomieniu skryptu na maszynie wirtualnej, w **wybierz maszyny wirtualne**, wybierz maszynę wirtualną. Następnie kliknij przycisk **Odkryj kule.**
6. Usługa Azure Backup odnajduje wszystkie bazy danych SAP HANA na maszynie Wirtualnej. Podczas odnajdywania usługa Azure Backup rejestruje maszynę wirtualną z magazynem i instaluje rozszerzenie na maszynie Wirtualnej. Żaden agent nie jest zainstalowany w bazie danych.

   ![Odnajduj bazy danych](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Teraz, gdy bazy danych, które chcemy wykonać kopię zapasową, zostaną wykryte, włączmy tworzenie kopii zapasowych.

1. Kliknij pozycję **Konfiguruj kopię zapasową**.

   ![Konfigurowanie kopii zapasowych](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. W **obszarze Wybierz elementy do utworzenia kopii zapasowej**wybierz jedną lub więcej baz danych, które chcesz chronić, a następnie kliknij przycisk **OK**.

   ![Wybieranie elementów do utworzenia kopii zapasowej](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. W **obszarze Zasady tworzenia kopii zapasowych > Wybierz zasady tworzenia kopii zapasowych**, utwórz nową zasadę tworzenia kopii zapasowych dla baz danych zgodnie z instrukcjami w następnej sekcji.

   ![Wybieranie zasad tworzenia kopii zapasowych](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Po utworzeniu zasad w **menu Kopia zapasowa**kliknij polecenie **Włącz tworzenie kopii zapasowej**.

   ![Kliknij pozycję Włącz tworzenie kopii zapasowej](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Śledzenie postępu konfiguracji kopii zapasowej w obszarze **Powiadomienia** portalu.

## <a name="creating-a-backup-policy"></a>Tworzenie zasad tworzenia kopii zapasowych

Zasady tworzenia kopii zapasowych określają, kiedy kopie zapasowe są pobierane i jak długo są zachowywane.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ następujące ustawienia zasad:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad. W takim przypadku wprowadź **saphana**.

   ![Wprowadź nazwę nowej zasady](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. W **obszarze Zasady pełnej kopii zapasowej**wybierz opcję Częstotliwość tworzenia kopii **zapasowych**. Możesz wybrać **opcję Codziennie** lub Co **tydzień**. W tym samouczku wybraliśmy **codzienną** kopię zapasową.

   ![Wybieranie częstotliwości tworzenia kopii zapasowej](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. W **obszarze Zakres przechowywania**skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
   * Domyślnie wszystkie opcje są zaznaczone. Wyczyść wszystkie limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które robisz.
   * Minimalny okres przechowywania dla każdego typu kopii zapasowej (pełna/różnicowa/dziennik) wynosi siedem dni.
   * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
   * Kopia zapasowa dla określonego dnia jest oznaczana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
   * Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.
4. W menu **Zasady pełnej kopii zapasowej** kliknij przycisk **OK,** aby zaakceptować ustawienia.
5. Następnie wybierz **opcję Kopia zapasowa różnicowa,** aby dodać zasady różnicowe.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania. Włączyliśmy różnicową kopię zapasową w każdą **niedzielę** **o godzinie 2:00,** która jest przechowywana przez **30 dni.**

   ![Zasady różnicowego tworzenia kopii zapasowych](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Przyrostowe kopie zapasowe nie są obecnie obsługiwane.
   >

7. Kliknij **przycisk OK,** aby zapisać zasady i powrócić do głównego menu **zasad kopii zapasowej.**
8. Wybierz **log backup,** aby dodać zasady tworzenia kopii zapasowych dziennika transakcyjnego,
   * **Kopia zapasowa dziennika** jest domyślnie **ustawiona**na Włącz . Nie można tego wyłączyć, ponieważ sap HANA zarządza wszystkimi kopiami zapasowymi dziennika.
   * Ustawiliśmy **2 godziny** jako harmonogram tworzenia kopii zapasowych i **15 dni** okresu przechowywania.

    ![Rejestrowanie zasad tworzenia kopii zapasowych](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Kopie zapasowe dziennika zaczynają przepływać dopiero po zakończeniu jednej pomyślnej pełnej kopii zapasowej.
   >

9. Kliknij **przycisk OK,** aby zapisać zasady i powrócić do głównego menu **zasad kopii zapasowej.**
10. Po zakończeniu definiowania zasad tworzenia kopii zapasowych kliknij przycisk **OK**.

Pomyślnie skonfigurowano kopie zapasowe bazy danych SAP HANA.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uruchamiać kopie zapasowe na żądanie w bazach danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](sap-hana-db-restore.md)
* Dowiedz się, jak [zarządzać bazami danych SAP HANA, których kopia zapasowa jest archiwizna przy użyciu usługi Azure Backup](sap-hana-db-manage.md)
* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowej baz danych SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
