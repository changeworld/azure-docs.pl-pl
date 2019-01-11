---
title: Przygotowanie do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z usługą Azure Backup
description: Zawiera opis sposobu przygotowania maszyn wirtualnych platformy Azure do utworzenia kopii zapasowej za pomocą usługi Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: a7a2d8729e1abdafa89eff912faf84d8f247b442
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215443"
---
# <a name="prepare-to-back-up-azure-vms"></a>Przygotowanie do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure

W tym artykule opisano sposób przygotowania do tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu [kopia zapasowa Azure](backup-introduction-to-azure-backup.md) magazyn usługi Recovery Services. Trwa przygotowywanie do tworzenia kopii zapasowych zawiera:


> [!div class="checklist"]
> * Przed rozpoczęciem Sprawdź obsługiwane scenariusze i ograniczenia.
> * Sprawdź wymagania wstępne, w tym wymagań maszyny Wirtualnej platformy Azure i połączenie sieciowe.
> * Utwórz magazyn.
> * Wybieranie sposobu replikacji magazynu.
> * Odnajdywanie maszyn wirtualnych, skonfiguruj ustawienia kopii zapasowych i zasady.
> * Włącz wykonywanie kopii zapasowej dla wybranych maszyn wirtualnych


> [!NOTE]
   > W tym artykule opisano sposób tworzenia kopii zapasowej maszyn wirtualnych platformy Azure, konfigurowania magazynu i wybierając pozycję maszyny wirtualne, aby utworzyć kopię zapasową. Jest to przydatne, jeśli chcesz utworzyć kopię zapasową wielu maszyn wirtualnych. Można również wykonać kopię zapasową maszyny Wirtualnej platformy Azure bezpośrednio z jego ustawienia maszyny Wirtualnej. [Dowiedz się więcej](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

1. [Zapoznaj się z omówieniem](backup-azure-vms-introduction.md) usługi Azure Backup dla maszyn wirtualnych platformy Azure.
2. Przejrzyj szczegóły dotyczące obsługi i ograniczenia poniżej.

   **Obsługa/ograniczenie** | **Szczegóły**
   --- | ---
   **System operacyjny Windows** | Windows Server 2008 R2 64-bitowe lub nowszej.<br/><br/> Klient Windows 7, 64-bitowej lub nowszym.
   **System operacyjny Linux** | Utworzeniem kopii zapasowej są dystrybucje systemu Linux w 64-bitowych [obsługiwanym przez platformę Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), z wyjątkiem systemu CoreOS w systemie Linux.<br/><br/> Sprawdź systemy operacyjne Linux, [obsługują funkcję przywracania pliku](backup-azure-restore-files-from-vm.md#for-linux-os).<br/><br/> Inne dystrybucje systemu Linux może działać, dopóki agent maszyny Wirtualnej jest dostępny na maszynie Wirtualnej i obsługiwany język Python. Jednak te dystrybucji nie są obsługiwane.
   **Region** | Można utworzyć kopię zapasową maszyn wirtualnych platformy Azure we wszystkich [obsługiwane regiony](https://azure.microsoft.com/regions/#services). Jeśli region jest nieobsługiwane, nie będzie można wybrać podczas tworzenia magazynu.<br/><br/> Nie można utworzyć kopię zapasową i przywracania w różnych regionach platformy Azure. Tylko w jednym regionie.
   **Limit dysku danych** | Nie można utworzyć kopii zapasowych maszyn wirtualnych z więcej niż 16 dysków danych.
   **Magazyn udostępniony** | Nie zaleca się wykonywanie kopii zapasowych maszyn wirtualnych przy użyciu pliku CSV lub serwera plików skalowalnego w poziomie. Autorzy CSV są prawdopodobnie nastąpi ich awaria.
   **Szyfrowanie systemu Linux** | Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux, szyfrowane przy użyciu systemu Linux Unified klucz instalacji (LUKS) nie jest obsługiwane.
   **Spójność maszyn wirtualnych** | Usługa Azure Backup nie obsługuje spójność wielu maszyn wirtualnych.
   **Sieć** | Kopie zapasowe danych nie zawiera sieciowych zainstalowane dyski dołączone do maszyny Wirtualnej.<br/><br/>
   **Migawki** | Tworzenie migawek na dysku z włączonym akceleratorem zapisu nie jest obsługiwane. Kopia zapasowa Azure zablokuje możliwość wykonywania migawki spójności aplikacji, wszystkich dysków maszyny Wirtualnej.
   **Program PowerShell** | Istnieje kilka akcji, które są dostępne tylko z programu PowerShell:<br/><br/> -Przywracania maszyn wirtualnych zarządzanych przez usługi równoważenia obciążenia wewnętrznej/zewnętrznej lub z wielu zastrzeżonych adresów IP lub kart. [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> -Kontroler domeny maszyny Wirtualnej w wielu konfiguracji kontrolera domeny. [Dowiedz się więcej](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
   **Czas systemowy** | Usługa Azure Backup nie obsługuje zegara automatycznego dopasowania dla-letniego zmiany dotyczące kopii zapasowych maszyn wirtualnych platformy Azure. Modyfikowanie zasad tworzenia kopii zapasowej ręcznego zgodnie z potrzebami.
   **Konta magazynu** | Jeśli używasz konta magazynu z ograniczeniami sieci, upewnij się, Włączyłeś **dozwolonych zaufanych usług firmy Microsoft dostęp do tego konta magazynu** tak, aby usługa Azure Backup można uzyskać dostępu do konta. Odzyskiwanie na poziomie elementu nie jest obsługiwana dla kont magazynu w sieci z ograniczeniami.<br/><br/> Upewnij się, że na koncie magazynu **zapory i sieci wirtualne** ustawienia Zezwalaj na dostęp z **wszystkie sieci**.


## <a name="prerequisites"></a>Wymagania wstępne

- W tym samym regionie, co maszyny wirtualne platformy Azure, aby utworzyć kopię zapasową, należy utworzyć magazyn.
- Przed rozpoczęciem, należy sprawdzić regiony maszyn wirtualnych platformy Azure.
    - Jeśli masz maszyny wirtualne w wielu regionach, utwórz magazyn w każdym regionie.
    - Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn i usługa Azure Backup określają, automatycznie.
- Sprawdź, czy agent maszyny Wirtualnej jest zainstalowany na maszynach wirtualnych platformy Azure, który chcesz utworzyć kopię zapasową.



### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej

Włączyć tworzenie kopii zapasowych, usługa Azure Backup instaluje rozszerzenie kopii zapasowej (migawka maszyny Wirtualnej lub migawki maszyny Wirtualnej w systemie Linux) do agenta maszyny Wirtualnej, które jest uruchamiane na maszynie Wirtualnej platformy Azure.
    -  Agent maszyny Wirtualnej platformy Azure jest instalowany domyślnie na dowolnej maszynie Wirtualnej z Windows wdrażanych za pomocą obrazu portalu Azure Marketplace. Podczas wdrażania obrazu z witryny Azure Marketplace w portalu, programu PowerShell, interfejsu wiersza polecenia lub szablonu usługi Azure Resource Manager, jest również instalowany Agent maszyny Wirtualnej platformy Azure.
    - Jeśli wykonano migrację maszyny Wirtualnej ze środowiska lokalnego, nie zainstalowano agenta i trzeba go zainstalować, aby można było włączyć kopii zapasowej dla maszyny Wirtualnej.

Jeśli to konieczne, zainstaluj agenta w następujący sposób.

**VM** | **Szczegóły**
--- | ---
**Maszyny wirtualne z systemem Windows** | [Pobierz i zainstaluj](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agenta z uprawnieniami administratora na komputerze.<br/><br/> Aby zweryfikować instalację, w *C:\WindowsAzure\Packages* na maszynie Wirtualnej, kliknij prawym przyciskiem myszy WaAppAgent.exe > **właściwości**, > **szczegóły** kartę. **Wersja produktu** powinien znajdować się wartość 2.6.1198.718 lub wyższa.
**Maszyny wirtualne z systemem Linux** | Instalację przy użyciu RPM lub DEB pakietu z repozytorium pakietów w Twojej dystrybucji jest preferowaną metodą instalacji i uaktualniania agenta systemu Linux dla platformy Azure. Wszystkie [zatwierdzonego dla dostawców dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Zintegruj pakiet Azure Linux agent repozytoriów i obrazów. Agent jest dostępna w [GitHub](https://github.com/Azure/WALinuxAgent), ale nie jest zalecane instalowanie z tego miejsca.
Jeśli masz problemy z tworzeniem kopii zapasowej maszyny Wirtualnej platformy Azure, skorzystaj z poniższej tabeli, aby sprawdzić, czy agent maszyny Wirtualnej platformy Azure został poprawnie zainstalowany na maszynie wirtualnej. W tabeli przedstawiono dodatkowe informacje na temat agenta maszyny Wirtualnej dla Windows i maszyn wirtualnych systemu Linux.

### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Zapasowy numer wewnętrzny, uruchomione na maszynie Wirtualnej musi mieć dostęp ruchu wychodzącego do platformy Azure z publicznymi adresami IP. Aby zezwolić na dostęp możesz wykonywać następujące czynności:


- **Reguły sieciowej grupy zabezpieczeń**: Zezwalaj na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Można dodać regułę, która zezwala na dostęp do usługi Azure Backup przy użyciu [tag usługi](../virtual-network/security-overview.md#service-tags), a nie indywidualnie dzięki czemu każdy zakres adresów i zarządzanie nimi wraz z upływem czasu.
- **Serwer proxy**: Wdrażanie serwera proxy HTTP dla routingu ruchu.
- **Zapora usługi Azure**: Zezwalaj na ruch przez zaporę platformy Azure na maszynie Wirtualnej za pomocą tagu w pełni kwalifikowaną nazwę domeny dla usługi Azure Backup.

Wybierając jedną z opcji, należy wziąć pod uwagę skutków ubocznych.

**Opcja** | **Zalety** | **Wady**
--- | --- | ---
**SIECIOWA GRUPA ZABEZPIECZEŃ** | Brak dodatkowych kosztów. Łatwo zarządzać za pomocą tagów usługi | Zapewnia dostęp do całej platformy Azure i nie tylko magazyn. |
**Serwer proxy HTTP** | Ścisła kontrola nad adresy URL magazynu jest dozwolone.<br/><br/> Pojedynczy punkt internet access dla maszyn wirtualnych.<br/><br/> Dodatkowe koszty dla serwera proxy.
**Nazwa FQDN tagów** | Łatwa w użyciu, jeśli masz zapory usługi Azure w podsieci sieci wirtualnej | Nie można utworzyć własne tagi nazwy FQDN lub zmodyfikować nazwy FQDN w tagu.



Jeśli używasz usługi Azure Managed Disks, może być konieczne otwarcie dodatkowych portów (na porcie 8443) na zaporach.



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Skonfiguruj regułę sieciowej grupy zabezpieczeń, aby zezwolić na dostęp ruchu wychodzącego do usługi Azure

Jeśli maszyna wirtualna platformy Azure ma dostęp do zarządzanych przez sieciową grupę zabezpieczeń, należy zezwolić na dostęp ruchu wychodzącego w magazynie kopii zapasowej do wymaganych zakresów adresów i portów.



1. Na maszynie wirtualnej > **sieć**, kliknij przycisk **Dodaj regułę portu wyjściowego**.
- W przypadku regułę odmawiającą dostępu nową Zezwalaj na reguła musi być wyższa. Na przykład, jeśli masz **Deny_All** reguły priorytetem 1000, nową regułę, musi być ustawione na mniej niż 1000.
2. W **Dodawanie reguły zabezpieczeń dla ruchu wychodzącego**, kliknij przycisk **zaawansowane**.
3. W źródle, wybierz **VirtualNetwork**.
4. W **zakresów portów źródłowych**, wpisz znak gwiazdki (*), aby zezwolić na dostęp ruchu wychodzącego z dowolnego portu.
5. W **docelowy**, wybierz opcję **Tag usługi**. Z listy wybierz magazyn. <region>. Region to region, w którym magazyn i maszyny wirtualne, aby utworzyć kopię zapasową, znajdują się.
6. W **docelowe zakresy portów**, wybierz numer portu.

    - Maszyny Wirtualnej przy użyciu dysków niezarządzanych i niezaszyfrowanego konta magazynu: 80
    - Maszyny Wirtualnej przy użyciu dysków niezarządzanych i zaszyfrowanego konta magazynu: 443 (ustawienie domyślne)
    - Zarządzanych maszyn wirtualnych: 8443.
1. W **protokołu**, wybierz opcję **TCP**.
2. W **priorytet**, nadaj mu wartość priorytetu jest mniejsza niż wyżej reguł odmowy.
3. Podaj nazwę i opis reguły, a następnie kliknij przycisk **OK**.

Reguły sieciowej grupy zabezpieczeń można zastosować do wielu maszyn wirtualnych, aby zezwolić na dostęp ruchu wychodzącego do platformy Azure dla usługi Azure Backup.

W tym wideo przeprowadzi Cię przez proces.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>Kierowanie ruchu kopii zapasowych za pośrednictwem serwera proxy

Można kierować ruchem kopii zapasowych za pośrednictwem serwera proxy, a następnie udzielić dostępu do serwera proxy do wymaganych zakresów platformy Azure.

Należy skonfigurować serwer proxy maszyny Wirtualnej, aby umożliwić następujące czynności:

- Maszyna wirtualna platformy Azure należy kierować cały ruch HTTP do publicznej sieci internet za pośrednictwem serwera proxy.
- Serwer proxy powinien zezwalać na ruch przychodzący z maszyn wirtualnych w odpowiednich sieci wirtualnych (VNet).
- Sieciowa grupa zabezpieczeń **blokady NF** musi regułę zezwalającą na ruch wychodzący z Internetu z maszyną Wirtualną serwera proxy.

Oto, jak należy skonfigurować serwer proxy. Używane przykładowe wartości. Należy je zastąpić własnymi.

#### <a name="set-up-a-system-account-proxy"></a>Konfigurowanie serwera proxy konto systemowe
Jeśli nie masz konta serwera proxy systemu skonfigurowane w następujący sposób:

1. Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Uruchom **PsExec.exe -i -s cmd.exe** do uruchamiania wiersza polecenia przy użyciu konta system.
3. Uruchom przeglądarkę w kontekście systemowym. Na przykład: **ProgramFiles%\Internet Explorer\iexplore.exe** programu Internet Explorer.  
4. Zdefiniuj ustawienia serwera proxy.
    - Na maszynach z systemem Linux:
        - Dodaj następujący wiersz do **/etc/środowisko** pliku:
            - **że =http://proxy IP adres: port serwera proxy**
        - Dodaj następujące wiersze do **/etc/waagent.conf** pliku:
            - **Adres IP HttpProxy.Host=proxy**
            - **HttpProxy.Port=proxy port**
    - Na komputerach Windows, w ustawieniach przeglądarki należy określić, że powinien być używany serwer proxy. Jeśli obecnie używasz serwera proxy na koncie użytkownika, umożliwia ten skrypt zastosować ustawienie poziomie konto systemu.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>Zezwalaj na połączenia przychodzące na serwerze proxy

1. Zezwalaj na połączenia przychodzące w ustawień serwera proxy.
2. Na przykład otworzyć **Zapora Windows z zabezpieczeniami zaawansowanymi**.
    - Kliknij prawym przyciskiem myszy **reguły dla ruchu przychodzącego** > **nową regułę**.
    - W **typ reguły** wybierz **niestandardowe** > **dalej**.
    - W **Program**, wybierz opcję **wszystkie programy** > **dalej**.
    - W **protokoły i porty** Ustaw typ **TCP**, **portów lokalnych** do **określonych portów**, i **port zdalny**do **wszystkie porty**.
    - Zakończ pracę kreatora, a następnie określ nazwę dla tej reguły.

#### <a name="add-an-exception-rule-to-the-nsg"></a>Dodaj regułę wyjątku do sieciowej grupy zabezpieczeń

W sieciowej grupie zabezpieczeń **blokady NF**, zezwalają na ruch z dowolnego portu na 10.0.0.5 do dowolnego adresu internetowego na porcie 80 (HTTP) lub 443 (HTTPS).

- Poniższy skrypt programu PowerShell zawiera przykładowy zezwolenia na ruch.
- Zamiast zezwalać na ruch wychodzący do wszystkich publicznych adresów internetowych, można określić zakres adresów IP (-DestinationPortRange), lub użyj storage.region tag usługi.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>Zezwalaj na dostęp przez zaporę za pomocą tagu w pełni kwalifikowaną nazwę domeny

Możesz skonfigurować zaporę usługi Azure, aby zezwolić na dostęp ruchu wychodzącego ruchu sieciowego do usługi Azure Backup.

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) wdrożenie zapory usługi Azure.
- [Przeczytaj o](https://docs.microsoft.com/azure/firewall/fqdn-tags) tagów w pełni kwalifikowaną nazwę domeny.


## <a name="create-a-vault"></a>Tworzenie magazynu

Magazyn usługi Recovery Services do tworzenia kopii zapasowych są przechowywane kopie zapasowe i punkty odzyskiwania utworzone wraz z upływem czasu i przechowuje zasady tworzenia kopii zapasowych, skojarzonych kopii zapasowych maszyn. Utwórz magazyn w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na **Centrum** menu, wybierz opcję **Przeglądaj**i wpisz **usługi Recovery Services**. Po rozpoczęciu wpisywania, dane wejściowe filtrowanie listy zasobów. Wybierz **Magazyny usługi Recovery Services**.

    ![Wpisując w polu i wybierając polecenie "Magazynów usługi Recovery Services", w wynikach](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Zostanie wyświetlona lista magazynów Usług odzyskiwania.
3. Na **Magazyny usługi Recovery Services** menu, wybierz opcję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **Magazyny usługi Recovery Services** zostanie otwarte okienko. Monit o podanie informacji dla **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji**.

    ![Okienko "Magazyny usługi recovery Services"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn.
    - Nazwa musi być unikalna w tej subskrypcji platformy Azure.
    - Może on zawierać 2 do 50 znaków.
    - Musi zaczynać się literą i może zawierać tylko litery, cyfry i łączniki.
5. Wybierz **subskrypcji** Aby wyświetlić listę dostępnych subskrypcji. Jeśli nie masz pewności, której subskrypcji użyć, użyj wartości domyślnej (lub sugerowane) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy pracy lub nauki jest skojarzony z wieloma subskrypcjami platformy Azure.
6. Wybierz **grupy zasobów** Aby wyświetlić listę dostępnych grup zasobów lub wybierz **New** do tworzenia nowej grupy zasobów. [Dowiedz się więcej](../azure-resource-manager/resource-group-overview.md) temat grup zasobów.
7. Wybierz **lokalizacji** do wybierz region geograficzny magazynu. Magazyn *musi* należeć do tego samego regionu maszyn wirtualnych, które chcesz utworzyć kopię zapasową.
8. Wybierz pozycję **Utwórz**.
    - Może upłynąć trochę czasu utworzenie magazynu.
    - Monitoruj powiadomienia o stanie wyświetlane w obszarze prawym górnym rogu portalu.
    ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Po utworzeniu magazynu pojawi się na liście magazynów usługi Recovery Services. Jeśli nie widzisz swojego magazynu wybierz **Odśwież**.

## <a name="set-up-storage-replication"></a>Konfigurowanie replikacji magazynu

Domyślnie Magazyn jest [magazyn geograficznie nadmiarowy (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Zalecamy użycie konta GRS dla Twoja podstawowa kopia zapasowa, ale można użyć[magazyn lokalnie nadmiarowy](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tańszych opcji. 

Zmodyfikuj replikacja magazynu w następujący sposób:

1. W magazynie > **infrastruktura zapasowa**, kliknij przycisk **konfiguracji kopii zapasowej**

   ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. W **konfiguracji kopii zapasowej**, zmodyfikuj metodę nadmiarowości magazynu jako wymagane i wybierz **Zapisz**.


## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Odnajdywanie maszyn wirtualnych w ramach subskrypcji i skonfiguruj kopię zapasową.

1. W magazynie > **Przegląd**, kliknij przycisk **+ kopia zapasowa**

   ![Przycisk tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Kopii zapasowej** i **cel kopii zapasowej** otwieranie okienka.

2. W **cel kopii zapasowej**> **gdzie jest uruchomione Twoje obciążenie?**, wybierz opcję **Azure**. W **jakich elementów chcesz utworzyć kopię zapasową?**, wybierz opcję **maszyny wirtualnej** >  **OK**. Rejestruje to rozszerzenie maszyny Wirtualnej w magazynie.

   ![Kopia zapasowa i cel kopii zapasowej okienka](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   W tym kroku rejestruje rozszerzenie maszyny Wirtualnej w magazynie. **Cel kopii zapasowej** okienko zostanie zamknięte, a **zasady tworzenia kopii zapasowej** zostanie otwarte okienko.

3. W **zasady tworzenia kopii zapasowej**, wybierz zasady, które chcesz skojarzyć z magazynem. Następnie kliknij przycisk **OK**.
    - Szczegóły domyślnych zasad znajdują się w menu rozwijanym.
    - Kliknij przycisk **Utwórz nowy** tworzenia zasad. [Dowiedz się więcej](backup-azure-vms-first-look-arm.md#defining-a-backup-policy) o definiowaniu zasad.

    !["Kopia zapasowa" i "Zasady kopii zapasowych" okienek](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. W **wybierz maszyny wirtualne** okienku wybierz maszyny wirtualne, korzystających z określonych zasadach kopii zapasowych > **OK**.

    - Wybranej maszyny Wirtualnej jest weryfikowana.
    - Można wybrać tylko maszyny wirtualne w tym samym regionie co magazyn. Maszyny wirtualne mogą być tylko kopii zapasowej w jednym magazynie.

   ![Okienko "Wybierz maszyny wirtualne"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. W **kopii zapasowej**, wybierz opcję **Włącz kopię zapasową**.

   - Wdraża zasady dla magazynu i maszyn wirtualnych i instaluje rozszerzenie kopii zapasowej na agencie maszyn wirtualnych uruchomionych na maszynie Wirtualnej platformy Azure.
   - Ten krok nie utworzyć początkowy punkt odzyskiwania dla maszyny Wirtualnej.

   ![Przycisk "Włącz kopię zapasową"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po włączeniu kopii zapasowej:

- Zasady kopii zapasowych działa zgodnie z harmonogramem kopii zapasowej.
- Usługa Backup instaluje rozszerzenie kopii zapasowej, czy maszyna wirtualna jest uruchomiona.
    - Uruchomiona maszyna wirtualna zapewnia największe prawdopodobieństwo uzyskania punktu odzyskiwania spójnego z aplikacją.
    -  Jednak maszyna wirtualna jest kopii zapasowej, nawet jeśli jest ona wyłączona i nie można zainstalować rozszerzenia. Jest to nazywane *maszyny Wirtualnej w trybie offline*. W takim przypadku punkt odzyskiwania będzie *awaryjnie spójny*.
- Jeśli chcesz od razu, generowanie kopii zapasowej na żądanie dla maszyny Wirtualnej w **elementów kopii zapasowych**, kliknij przycisk wielokropka (...) obok maszyna wirtualna > **Utwórz teraz kopię zapasową**.


## <a name="next-steps"></a>Kolejne kroki

- Rozwiązanie wszelkich problemów, które występują w [agentów maszyny Wirtualnej platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) lub [kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-troubleshoot.md).
- [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-first-look-arm.md)
