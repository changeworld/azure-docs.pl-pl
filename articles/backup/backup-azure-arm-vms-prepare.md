---
title: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services za pomocą usługi Azure Backup
description: W tym artykule opisano sposób tworzenia kopii zapasowej maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services za pomocą usługi Azure Backup
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: 142ffdadf4adb1ee07f3592624cbdddfb310b580
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264560"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services

W tym artykule opisano sposób tworzenia kopii zapasowej maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services za pomocą [kopia zapasowa Azure](backup-overview.md) usługi. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przygotowywanie maszyn wirtualnych platformy Azure.
> * Utwórz magazyn.
> * Odnajdywanie maszyn wirtualnych i konfigurowanie zasad tworzenia kopii zapasowej.
> * Włączanie tworzenia kopii zapasowej maszyn wirtualnych platformy Azure.
> * Uruchom proces tworzenia początkowej kopii zapasowej.


> [!NOTE]
> W tym artykule opisano jak skonfigurować Magazyn i wybrać maszyny wirtualne, aby utworzyć kopię zapasową. Jest to przydatne, jeśli chcesz utworzyć kopię zapasową wielu maszyn wirtualnych. Możesz też [Utwórz kopię zapasową jednej maszyny Wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md) bezpośrednio z poziomu ustawień maszyny Wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem


- [Przegląd](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektura kopii zapasowych maszyn wirtualnych platformy Azure.
- [Dowiedz się więcej o](backup-azure-vms-introduction.md) kopii zapasowych maszyn wirtualnych platformy Azure, a rozszerzenie kopii zapasowej.
- [Przejrzyj macierz obsługi](backup-support-matrix-iaas.md) przed skonfigurowaniem kopii zapasowej.

Ponadto istnieje kilka rzeczy, które może być konieczne w niektórych sytuacjach:

- **Zainstaluj agenta maszyny Wirtualnej na maszynie Wirtualnej**: Usługa Azure Backup tworzy kopie zapasowe maszyn wirtualnych platformy Azure, instalowanie rozszerzenia na maszynie agenta maszyny Wirtualnej platformy Azure. Jeśli maszyna wirtualna została utworzona z obrazu w witrynie Azure marketplace, agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę Wirtualną lub migrowania maszyny w środowisku lokalnym, może być konieczne [Zainstaluj agenta ręcznie w](#install-the-vm-agent).
- **Jawnie zezwolić na dostęp ruchu wychodzącego**: Ogólnie rzecz biorąc nie musisz jawnie zezwolić na dostęp ruchu wychodzącego sieci maszyny wirtualnej platformy Azure w celu użycia go do komunikowania się z usługą Azure Backup. Jednak niektóre maszyny wirtualne mogą wystąpić problemy z połączeniem, przedstawiający **ExtensionSnapshotFailedNoNetwork** wystąpił błąd podczas próby połączenia. W takim przypadku należy [jawnie zezwolić na dostęp ruchu wychodzącego](#explicitly-allow-outbound-access), więc rozszerzenie kopii zapasowej platformy Azure mogą komunikować się za pomocą platformy Azure publiczne adresy IP dla ruchu kopii zapasowej.


## <a name="create-a-vault"></a>Tworzenie magazynu

 Magazyn przechowuje kopie zapasowe i punkty odzyskiwania utworzone wraz z upływem czasu i przechowuje zasady tworzenia kopii zapasowych, skojarzonych kopii zapasowych maszyn. Utwórz magazyn w następujący sposób:    

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).    
2. W polu wyszukiwania wpisz **usługi Recovery Services**. W obszarze **usług**, kliknij przycisk **Magazyny usługi Recovery Services**.   

     ![Wyszukiwanie magazynów usługi Recovery Services](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/> 

3. W **Magazyny usługi Recovery Services** menu, kliknij przycisk **+ Dodaj**.    

     ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)   

4. W **magazyn usługi Recovery Services**, wpisz przyjazną nazwę identyfikującą magazyn.   
    - Nazwa musi być unikalna w tej subskrypcji platformy Azure.   
    - Może on zawierać 2 do 50 znaków.    
    - Musi zaczynać się literą i może zawierać tylko litery, cyfry i łączniki.   
5. Wybierz subskrypcję platformy Azure, grupę zasobów i region geograficzny, w którym można utworzyć magazynu. Następnie kliknij pozycję **Utwórz**.    
    - Może upłynąć trochę czasu utworzenie magazynu.  
    - Monitoruj powiadomienia o stanie wyświetlane w obszarze prawym górnym rogu portalu.   


 Po utworzeniu magazynu pojawi się na liście magazynów usługi Recovery Services. Jeśli nie widzisz swojego magazynu wybierz **Odśwież**.
 
![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)    

### <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie, magazyny użyj [magazyn geograficznie nadmiarowy (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Jeśli magazyn jest Twoje podstawowy mechanizm tworzenia kopii zapasowej, firma Microsoft zaleca, że używasz magazynu GRS.
- Możesz użyć [magazyn lokalnie nadmiarowy (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tańszych opcji.

Zmodyfikuj typ replikacji magazynu w następujący sposób:

1. W nowym magazynie kliknij **właściwości** w **ustawienia** sekcji.
2. W **właściwości**w obszarze **konfiguracji kopii zapasowej**, kliknij przycisk **aktualizacji**.
3. Wybierz typ replikacji magazynu, a następnie kliknij przycisk **Zapisz**.

      ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > Typ replikacji magazynu nie można zmodyfikować po skonfigurowaniu magazynu zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, musisz odtworzyć w magazynie. 

## <a name="apply-a-backup-policy"></a>Stosowanie zasad tworzenia kopii zapasowej

Skonfiguruj zasady tworzenia kopii zapasowej dla magazynu.

1. W magazynie kliknij **+ kopia zapasowa** w **Przegląd** sekcji.

   ![Przycisk tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. W **cel kopii zapasowej** > **gdzie jest uruchomione Twoje obciążenie?** wybierz **Azure**. W **jakich elementów chcesz utworzyć kopię zapasową?** wybierz **maszyny wirtualnej** >  **OK**. Rejestruje to rozszerzenie maszyny Wirtualnej w magazynie.

   ![Kopia zapasowa i cel kopii zapasowej okienka](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. W **zasady tworzenia kopii zapasowej**, wybierz zasady, które chcesz skojarzyć z magazynem. 
    - Domyślna zasada wykonuje kopię zapasową maszyny Wirtualnej raz dziennie. Codzienne kopie zapasowe są przechowywane przez 30 dni. Natychmiastowe odzyskiwanie migawki są przechowywane przez dwa dni.
    - Jeśli nie chcesz użyć domyślnych zasad, wybierz opcję **Utwórz nowy**i utworzyć niestandardowe zasady, zgodnie z opisem w następnej procedurze.

      ![Domyślne zasady kopii zapasowych](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. W **wybierz maszyny wirtualne**, wybierz maszyny wirtualne, aby utworzyć kopię zapasową za pomocą zasad. Następnie kliknij przycisk **OK**.

   - Wybrane maszyny wirtualne są weryfikowane.
   - Można wybrać tylko maszyny wirtualne w tym samym regionie co magazyn.
   - Maszyny wirtualne mogą być tylko kopii zapasowej w jednym magazynie.

     ![Okienko "Wybierz maszyny wirtualne"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. W **kopii zapasowej**, kliknij przycisk **Włącz kopię zapasową**. Wdraża zasady dla magazynu i maszyn wirtualnych i instaluje rozszerzenie kopii zapasowej na agencie maszyn wirtualnych uruchomionych na maszynie Wirtualnej platformy Azure.
     
     ![Przycisk "Włącz kopię zapasową"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po włączeniu kopii zapasowej:

- Usługa Backup instaluje rozszerzenie kopii zapasowej, czy maszyna wirtualna jest uruchomiona.
- Początkowa kopia zapasowa zostanie uruchomione zgodnie z harmonogramem kopii zapasowej.
- Gdy wykonywane kopie zapasowe, należy pamiętać, że:
    - Maszynę Wirtualną, która jest uruchomiona ma największe prawdopodobieństwo przechwytywania punktu odzyskiwania spójnego na poziomie aplikacji.
    - Jednak nawet wtedy, gdy maszyna wirtualna jest wyłączona kopii zapasowej. Maszyna wirtualna jest znany jako Maszynę wirtualną w trybie offline. W tym przypadku punkt odzyskiwania będzie spójna w razie awarii.
    

### <a name="create-a-custom-policy"></a>Tworzenie zasad niestandardowych

Jeśli wybrano, aby utworzyć nowe zasady kopii zapasowych, wprowadź ustawienia zasad.

1. W **nazwa_zasad**, określ nazwę opisową.
2. W **harmonogram tworzenia kopii zapasowych** Określ, kiedy należy podjąć kopii zapasowych. Tworzenie kopii zapasowych codziennie lub co tydzień możesz korzystać z maszyn wirtualnych platformy Azure.
2. W **natychmiastowe Przywracanie**, określ, jak długo mają być przechowywane lokalnie natychmiastowe Przywracanie migawki.
    - Po przywróceniu, kopii zapasowej maszyny Wirtualnej dyski są kopiowane z magazynu, za pośrednictwem sieci do lokalizacji magazynu odzyskiwania. Za pomocą natychmiastowe Przywracanie mogą korzystać z lokalnie przechowywane migawek podczas zadanie tworzenia kopii zapasowej, bez konieczności oczekiwania na dane kopii zapasowej, należy dokonać w magazynie.
    - Można przechowywać migawki natychmiastowe przywracanie dla od jednej do siedmiu dni. Dwa dni jest ustawieniem domyślnym.
3. W **zakres przechowywania**, określ, jak długo chcesz przechowywać punktów kopii zapasowej codziennie lub co tydzień.
4. W **przechowywanie miesięcznego punktu kopii zapasowej**, określ, czy chcesz zachować kopię zapasową kopii zapasowych codziennie lub co tydzień co miesiąc. 
5. Kliknij przycisk **OK** zapisać zasady.

    ![Nowe zasady kopii zapasowych](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Usługa Azure Backup nie obsługuje zegara automatycznego dopasowania dla-letniego zmiany dotyczące kopii zapasowych maszyn wirtualnych platformy Azure. Wraz ze zmianami w czasie, należy zmodyfikować zasady tworzenia kopii zapasowych ręcznego zgodnie z potrzebami.

## <a name="trigger-the-initial-backup"></a>Wyzwalanie tworzenia początkowej kopii zapasowej

Tworzenie początkowej kopii zapasowej zostaną wykonane zgodnie z harmonogramem, ale możesz uruchomić ją od razu, w następujący sposób:

1. W menu magazynu kliknij **kopii zapasowych elementów**.
2. W **elementy kopii zapasowej** kliknij **maszyny wirtualnej platformy Azure**.
3. W **elementy kopii zapasowej** listy, kliknij przycisk z wielokropkiem (...).
4. Kliknij przycisk **Utwórz teraz kopię zapasową**.
5. W **Utwórz teraz kopię zapasową**, wybierz ostatni dzień, który ma być przechowywana punkt odzyskiwania przy użyciu kontrolki kalendarza. Następnie kliknij przycisk **OK**.
6. Monitoruj powiadomienia z portalu. Możesz monitorować postęp zadania na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

## <a name="optional-steps-install-agentallow-outbound"></a>Kroki opcjonalne (Instalowanie agenta/Zezwalaj na wychodzące)
### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej

Usługa Azure Backup tworzy kopie zapasowe maszyn wirtualnych platformy Azure, instalowanie rozszerzenia na maszynie agenta maszyny Wirtualnej platformy Azure. Jeśli maszyna wirtualna została utworzona z obrazu w portalu Azure Marketplace, agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę Wirtualną lub migrowania maszyny w środowisku lokalnym, należy zainstalować agenta ręcznie, zgodnie z opisem w tabeli.

**VM** | **Szczegóły**
--- | ---
**Windows** | 1. [Pobierz i zainstaluj](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) pliku MSI agenta.<br/><br/> 2. Zainstaluj z uprawnieniami administratora na komputerze.<br/><br/> 3. Zweryfikuj instalację. W *C:\WindowsAzure\Packages* na maszynie Wirtualnej, kliknij prawym przyciskiem myszy **WaAppAgent.exe** > **właściwości**. Na **szczegóły** karcie **wersji produktu** powinien znajdować się wartość 2.6.1198.718 lub nowszej.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że żadne operacje tworzenia kopii zapasowej są uruchomione, i [ponownie zainstalować agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instalowanie przy użyciu RPM lub DEB pakietu z repozytorium pakietów w Twojej dystrybucji. Jest to preferowana metoda instalowania i uaktualniania agenta systemu Linux platformy Azure. Wszystkie [zatwierdzonego dla dostawców dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Zintegruj pakiet Azure Linux agent repozytoriów i obrazów. Agent jest dostępna w [GitHub](https://github.com/Azure/WALinuxAgent), ale nie jest zalecane instalowanie z tego miejsca.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, żadne operacje tworzenia kopii zapasowej są uruchomione i aktualizowanie plików binarnych.

### <a name="explicitly-allow-outbound-access"></a>Jawnie zezwolić na dostęp ruchu wychodzącego

Rozszerzenie kopii zapasowej uruchomione na maszynie Wirtualnej musi wychodzący dostęp do platformy Azure publicznych adresów IP.

- Zazwyczaj nie trzeba jawnie zezwolić na dostęp ruchu wychodzącego sieci maszyny wirtualnej platformy Azure w celu użycia go do komunikowania się z usługą Azure Backup.
- Jeśli napotkasz problemy z maszynami wirtualnymi z łączenia lub jeśli zostanie wyświetlony błąd **ExtensionSnapshotFailedNoNetwork** podczas próby połączenia, należy jawnie zezwolisz na dostęp, zapasowy numer wewnętrzny może komunikować się do platformy Azure, publiczny adres IP adresy dla ruchu kopii zapasowej. Metody dostępu są podsumowane w poniższej tabeli.


**Opcja** | **Akcja** | **Szczegóły** 
--- | --- | --- 
**Skonfiguruj reguły sieciowej grupy zabezpieczeń** | Zezwalaj na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Zamiast umożliwiając i zarządzania nimi każdego zakresu adresów, można dodać regułę, która zezwala na dostęp do usługi Azure Backup przy użyciu [tag usługi](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [Dowiedz się więcej](../virtual-network/security-overview.md#service-tags) o tagi usługi.<br/><br/> Tagi usługi uprościć proces zarządzania, a nie pociągnąć za sobą dodatkowe koszty.
**Wdrażanie serwera proxy** | Wdrażanie serwera proxy HTTP dla routingu ruchu. | Zapewnia dostęp do całej platformy Azure i nie tylko magazyn.<br/><br/> Ścisła kontrola nad adresy URL magazynu jest dozwolone.<br/><br/> Pojedynczy punkt internet access dla maszyn wirtualnych.<br/><br/> Dodatkowe koszty dla serwera proxy.
**Konfigurowanie zapory platformy Azure** | Zezwalaj na ruch przez zaporę platformy Azure na maszynie Wirtualnej za pomocą tagu w pełni kwalifikowaną nazwę domeny dla usługi Azure Backup | Łatwa w użyciu, jeśli masz zapory usługi Azure w podsieci sieci wirtualnej.<br/><br/> Nie można utworzyć własne tagi nazwy FQDN lub zmodyfikować nazwy FQDN w tagu.<br/><br/> Jeśli dyski zarządzane maszynach wirtualnych platformy Azure, może być konieczne otwarcie dodatkowych portów (8443) na zaporach.

#### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Nawiązać łączność z usługą sieciową grupę zabezpieczeń, przez serwer proxy lub za pośrednictwem zapory

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Skonfiguruj regułę sieciowej grupy zabezpieczeń, aby zezwolić na dostęp ruchu wychodzącego do usługi Azure

Jeśli sieciowa grupa zabezpieczeń zarządza dostęp do maszyny Wirtualnej, należy zezwolić na dostęp ruchu wychodzącego w magazynie kopii zapasowej do wymaganych zakresów adresów i portów.

1. We właściwościach maszyny Wirtualnej > **sieć**, wybierz opcję **Dodaj regułę portu wyjściowego**.
2. W **Dodawanie reguły zabezpieczeń dla ruchu wychodzącego**, wybierz opcję **zaawansowane**.
3. W **źródła**, wybierz opcję **VirtualNetwork**.
4. W **zakresów portów źródłowych**, wprowadź znak gwiazdki (*), aby zezwolić na dostęp ruchu wychodzącego z dowolnego portu.
5. W **docelowy**, wybierz opcję **Tag usługi**. Wybierz z listy, **Storage.region**. Region jest, gdzie znajdują się magazynu i maszyn wirtualnych, które ma zostać utworzona kopia zapasowa.
6. W **docelowe zakresy portów**, wybierz numer portu.
    - Niezarządzane maszyny Wirtualnej przy użyciu niezaszyfrowanego konta magazynu: 80
    - Niezarządzanej maszyny Wirtualnej za pomocą zaszyfrowanego konta magazynu: 443 (ustawienie domyślne)
    - Zarządzanych maszyn wirtualnych: 8443.
7. W **protokołu**, wybierz opcję **TCP**.
8. W **priorytet**, określ wartość priorytetu z mniej niż wyżej reguł odmowy.
   
   Jeśli masz regułę, która nie zezwala na dostęp, nową Zezwalaj na reguła musi być wyższa. Na przykład, jeśli masz **Deny_All** reguły priorytetem 1000, nową regułę, musi być ustawione na mniej niż 1000.
9. Podaj nazwę i opis dla tej reguły, a następnie wybierz **OK**.

Reguły sieciowej grupy zabezpieczeń można zastosować do wielu maszyn wirtualnych, aby zezwolić na dostęp ruchu wychodzącego. W tym wideo przeprowadzi Cię przez proces.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Kierowanie ruchu kopii zapasowych za pośrednictwem serwera proxy

Można kierować ruchem kopii zapasowych za pośrednictwem serwera proxy, a następnie udzielić dostępu do serwera proxy do wymaganych zakresów platformy Azure. Skonfiguruj serwer proxy maszyny Wirtualnej, aby umożliwić następujące czynności:

- Maszyna wirtualna platformy Azure należy kierować cały ruch HTTP do publicznej sieci internet za pośrednictwem serwera proxy.
- Serwer proxy powinien zezwalać na ruch przychodzący z maszyn wirtualnych w odpowiednich sieci wirtualnej.
- Sieciowa grupa zabezpieczeń **blokady NF** musi regułę zezwalającą na ruch wychodzący z Internetu z maszyną Wirtualną serwera proxy.

###### <a name="set-up-the-proxy"></a>Konfigurowanie serwera proxy

Jeśli nie masz konta serwera proxy systemu skonfigurowane w następujący sposób:

1. Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Uruchom **PsExec.exe -i -s cmd.exe** do uruchamiania wiersza polecenia przy użyciu konta system.
3. Uruchom przeglądarkę w kontekście systemowym. Na przykład użyć **%PROGRAMFILES%\Internet Explorer\iexplore.exe** programu Internet Explorer.  
4. Zdefiniuj ustawienia serwera proxy.
   - Na maszynach z systemem Linux:
     - Dodaj następujący wiersz do **/etc/środowisko** pliku:
       - **że = http:\//proxy IP adres: port serwera proxy**
     - Dodaj następujące wiersze do **/etc/waagent.conf** pliku:
         - **Adres IP HttpProxy.Host=proxy**
         - **HttpProxy.Port=proxy port**
   - Na komputerach Windows, w ustawieniach przeglądarki należy określić, że powinien być używany serwer proxy. Jeśli obecnie używasz serwera proxy na koncie użytkownika, umożliwia ten skrypt należy zastosować na poziomie konta system.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Zezwalaj na połączenia przychodzące na serwerze proxy

Zezwalaj na połączenia przychodzące w ustawień serwera proxy.

1. Otwórz zaporę Windows **Zapora Windows z zabezpieczeniami zaawansowanymi**.
2. Kliknij prawym przyciskiem myszy **reguły dla ruchu przychodzącego** > **nową regułę**.
3. W **typ reguły**, wybierz opcję **niestandardowe** > **dalej**.
4. W **Program**, wybierz opcję **wszystkie programy** > **dalej**.
5. W **protokoły i porty**:
   - Ustaw typ **TCP**.
   - Ustaw **portów lokalnych** do **określonych portów**.
   - Ustaw **port zdalny** do **wszystkie porty**.
  
6. Zakończ pracę kreatora, a następnie określ nazwę dla tej reguły.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Dodaj regułę wyjątku do sieciowej grupy zabezpieczeń dla serwera proxy

W sieciowej grupie zabezpieczeń **blokady NF**, zezwalają na ruch z dowolnego portu na 10.0.0.5 do dowolnego adresu internetowego na porcie 80 (HTTP) lub 443 (HTTPS).

Poniższy skrypt programu PowerShell zawiera przykładowy zezwolenia na ruch.
Zamiast zezwalać na ruch wychodzący do wszystkich publicznych adresów internetowych, można określić zakres adresów IP (`-DestinationPortRange`), lub użyj storage.region tag usługi.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Zezwalaj na dostęp przez zaporę za pomocą tagu w pełni kwalifikowaną nazwę domeny

Można skonfigurować zapory usługi Azure, aby zezwolić na dostęp ruchu wychodzącego ruchu sieciowego do usługi Azure Backup.

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) wdrożenie zapory usługi Azure.
- [Przeczytaj o](https://docs.microsoft.com/azure/firewall/fqdn-tags) tagów w pełni kwalifikowaną nazwę domeny.



## <a name="next-steps"></a>Kolejne kroki

- Rozwiązywanie problemów z [agentów maszyny Wirtualnej platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) lub [kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-troubleshoot.md).
- [Przywróć](backup-azure-arm-restore-vms.md) maszyn wirtualnych platformy Azure.

