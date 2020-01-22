---
title: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services
description: Zawiera opis sposobu tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services przy użyciu Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 95c185c09558f3d1a525c9bcf15f3957118c4311
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294035"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services przy użyciu usługi [Azure Backup](backup-overview.md) .

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Przygotowywanie maszyn wirtualnych platformy Azure.
> * Utwórz magazyn.
> * Odnajdywanie maszyn wirtualnych i Konfigurowanie zasad tworzenia kopii zapasowych.
> * Włącz tworzenie kopii zapasowych dla maszyn wirtualnych platformy Azure.
> * Uruchom proces tworzenia początkowej kopii zapasowej.

> [!NOTE]
> W tym artykule opisano sposób konfigurowania magazynu i wybierania maszyn wirtualnych do utworzenia kopii zapasowej. Jest to przydatne, jeśli chcesz utworzyć kopię zapasową wielu maszyn wirtualnych. Alternatywnie można [utworzyć kopię zapasową pojedynczej maszyny wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md) bezpośrednio z ustawień maszyny wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Przejrzyj](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturę kopii zapasowych maszyny wirtualnej platformy Azure.
* [Dowiedz się więcej o](backup-azure-vms-introduction.md) Kopia zapasowa maszyny wirtualnej platformy Azure i rozszerzenie kopii zapasowej.
* Przed skonfigurowaniem kopii zapasowej [zapoznaj się z matrycą pomocy technicznej](backup-support-matrix-iaas.md) .

Ponadto istnieje kilka rzeczy, które mogą być konieczne w pewnych okolicznościach:

* **Zainstaluj agenta maszyny wirtualnej na maszynie wirtualnej**: Azure Backup tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przez zainstalowanie rozszerzenia agenta maszyny wirtualnej platformy Azure uruchomionego na tym komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, Agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę wirtualną lub migrujesz maszynę lokalną, może być konieczne [ręczne zainstalowanie agenta](#install-the-vm-agent).
* **Jawnie Zezwalaj na dostęp wychodzący**: Ogólnie rzecz biorąc, nie musisz jawnie zezwalać na wychodzący dostęp sieciowy dla maszyny wirtualnej platformy Azure w celu komunikowania się z Azure Backup. Niektóre maszyny wirtualne mogą jednak powodować problemy z połączeniem, wyświetlając błąd **ExtensionSnapshotFailedNoNetwork** podczas próby nawiązania połączenia. W takim przypadku należy [jawnie zezwolić na dostęp wychodzący](#explicitly-allow-outbound-access), aby rozszerzenie Azure Backup mogły komunikować się z publicznymi adresami IP platformy Azure na potrzeby tworzenia kopii zapasowych.

## <a name="create-a-vault"></a>Tworzenie magazynu

 Magazyn przechowuje kopie zapasowe i punkty odzyskiwania utworzone wraz z upływem czasu, a następnie przechowuje zasady tworzenia kopii w programie skojarzonych z maszynami z kopii zapasowej. Utwórz magazyn w następujący sposób:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. W polu wyszukiwania wpisz **Recovery Services**. W obszarze **usługi**kliknij pozycję **magazyny Recovery Services**.

     ![Wyszukaj Recovery Services magazyny](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. W menu **magazyny Recovery Services** kliknij pozycję **+ Dodaj**.

     ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. W obszarze **magazyn Recovery Services**wpisz przyjazną nazwę, aby zidentyfikować magazyn.
    * Nazwa musi być unikalna w tej subskrypcji platformy Azure.
    * Może zawierać od 2 do 50 znaków.
    * Musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.
5. Wybierz subskrypcję platformy Azure, grupę zasobów i region geograficzny, w którym ma zostać utworzony magazyn. Następnie kliknij pozycję **Utwórz**.
    * Utworzenie magazynu może chwilę potrwać.
    * Monitoruj powiadomienia o stanie w prawym górnym rogu portalu.

Po utworzeniu magazynu pojawi się on na liście magazyny Recovery Services. Jeśli Twój magazyn nie jest widoczny, wybierz pozycję **Odśwież**.

![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure Backup teraz umożliwia dostosowanie nazwy grupy zasobów utworzonej przez usługę Azure Backup. Aby uzyskać więcej informacji, zobacz [Azure Backup grupę zasobów dla Virtual Machines](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie magazyny korzystają z [magazynu geograficznie nadmiarowego (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zalecamy użycie GRS.
* Dla tańszej opcji można użyć [magazynu lokalnie nadmiarowego (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Zmodyfikuj typ replikacji magazynu w następujący sposób:

1. W nowym magazynie kliknij pozycję **Właściwości** w sekcji **Ustawienia** .
2. W obszarze **Właściwości**, w obszarze **Konfiguracja kopii zapasowej**, kliknij przycisk **Aktualizuj**.
3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

      ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu magazynu i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, należy ponownie utworzyć magazyn.

## <a name="apply-a-backup-policy"></a>Stosowanie zasad kopii zapasowych

Skonfiguruj zasady tworzenia kopii zapasowych dla magazynu.

1. W magazynie kliknij pozycję **+ kopia zapasowa** w sekcji **Przegląd** .

   ![Przycisk Kopia zapasowa](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. W > **cel kopii zapasowej** , **gdzie jest uruchomione Twoje obciążenie?** wybierz **platformę Azure**. W **czym chcesz utworzyć kopię zapasową?** wybierz **maszynę wirtualną** >  **OK**. Spowoduje to zarejestrowanie rozszerzenia maszyny wirtualnej w magazynie.

   ![Okienka celu tworzenia kopii zapasowych i tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. W obszarze **zasady tworzenia kopii zapasowych**wybierz zasady, które chcesz skojarzyć z magazynem.
    * Zasady domyślne tworzą kopię zapasową maszyny wirtualnej raz dziennie. Codzienne kopie zapasowe są przechowywane przez 30 dni. Migawki odzyskiwania natychmiastowego są przechowywane przez dwa dni.
    * Jeśli nie chcesz używać zasad domyślnych, wybierz pozycję **Utwórz nową**, a następnie utwórz zasady niestandardowe zgodnie z opisem w następnej procedurze.

      ![Domyślne zasady kopii zapasowych](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. W obszarze **Wybieranie maszyn wirtualnych**Wybierz Maszyny wirtualne, których kopię zapasową chcesz utworzyć przy użyciu zasad. Następnie kliknij przycisk **OK**.

   * Wybrane maszyny wirtualne zostały zweryfikowane.
   * Maszyny wirtualne można wybrać tylko w tym samym regionie, w którym znajduje się magazyn.
   * Kopie zapasowe maszyn wirtualnych można wykonywać tylko w pojedynczym magazynie.

     ![Okienko "Wybieranie maszyn wirtualnych"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. W obszarze **kopia zapasowa**kliknij pozycję **Włącz kopię zapasową**. Spowoduje to wdrożenie zasad w magazynie i na maszynach wirtualnych, a następnie zainstalowanie rozszerzenia kopii zapasowej na agencie maszyny wirtualnej działającym na maszynie wirtualnej platformy Azure.

     ![Przycisk "Włącz kopię zapasową"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po włączeniu kopii zapasowej:

* Usługa Backup instaluje rozszerzenie kopii zapasowej, niezależnie od tego, czy maszyna wirtualna jest uruchomiona.
* Początkowa kopia zapasowa będzie uruchamiana zgodnie z harmonogramem tworzenia kopii zapasowych.
* Gdy wykonywane są kopie zapasowe, należy pamiętać, że:
  * Uruchomiona maszyna wirtualna ma największą szansę na przechwycenie punktu odzyskiwania spójnego na poziomie aplikacji.
  * Jednak nawet jeśli maszyna wirtualna jest wyłączona, zostanie utworzona jej kopia zapasowa. Taka maszyna wirtualna jest nazywana maszyną wirtualną w trybie offline. W takim przypadku punkt odzyskiwania będzie spójny z awarią.

### <a name="create-a-custom-policy"></a>Tworzenie zasad niestandardowych

Jeśli wybrano opcję utworzenia nowych zasad tworzenia kopii zapasowych, Wypełnij ustawienia zasad.

1. W polu **Nazwa zasad**Określ zrozumiałą nazwę.
2. W polu **harmonogram tworzenia kopii zapasowych**Określ, kiedy mają być pobierane kopie zapasowe. Codzienne lub cotygodniowe wykonywanie kopii zapasowych maszyn wirtualnych platformy Azure.
3. W obszarze **natychmiastowe przywracanie**Określ, jak długo mają być przechowywane migawki lokalnie przywracane.
    * Podczas przywracania kopia zapasowa dysków maszyny wirtualnej jest kopiowana z magazynu w sieci do lokalizacji magazynu odzyskiwania. Za pomocą natychmiastowego przywracania można korzystać z migawek przechowywanych lokalnie, które są wykonywane podczas zadania tworzenia kopii zapasowej, bez czekania na przesłanie danych kopii zapasowej do magazynu.
    * Migawki do natychmiastowego przywrócenia można zachować przez od 1 do pięciu dni. Ustawienie domyślne to dwa dni.
4. W obszarze **Zakres przechowywania**Określ, jak długo mają być przechowywane codziennie lub cotygodniowe punkty kopii zapasowych.
5. W obszarze **przechowywanie miesięcznego punktu kopii zapasowej**Określ, czy chcesz przechowywać kopię zapasową codziennych, czy cotygodniowych kopii zapasowych.
6. Kliknij przycisk **OK** zapisać zasady.

    ![Nowe zasady tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup nie obsługuje automatycznego dopasowywania zegara w przypadku zmian w ramach zmiany czasu dla kopii zapasowych maszyny wirtualnej platformy Azure. Po wystąpieniu zmian czasu należy ręcznie zmodyfikować zasady tworzenia kopii zapasowych zgodnie z potrzebami.

## <a name="trigger-the-initial-backup"></a>Wyzwalanie początkowej kopii zapasowej

Początkowa kopia zapasowa będzie uruchamiana zgodnie z harmonogramem, ale można ją uruchomić od razu w następujący sposób:

1. W menu magazyn kliknij pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej**kliknij pozycję **maszyna wirtualna platformy Azure**.
3. Na liście **elementy kopii zapasowej** kliknij przycisk wielokropka (...).
4. Kliknij pozycję **Utwórz kopię zapasową teraz**.
5. W obszarze **kopia zapasowa**Użyj formantu kalendarza, aby wybrać ostatni dzień przechowywania punktu odzyskiwania. Następnie kliknij przycisk **OK**.
6. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

## <a name="verify-backup-job-status"></a>Sprawdź stan zadania tworzenia kopii zapasowej

Szczegóły zadania kopii zapasowej dla każdej kopii zapasowej maszyny wirtualnej składają się z dwóch faz, fazy **migawki** i fazy **transferu danych do magazynu** .<br/>
Faza migawki gwarantuje dostępność punktu odzyskiwania przechowywanego wraz z dyskami do **natychmiastowego przywrócenia** i jest dostępna przez maksymalnie pięć dni w zależności od przechowywania migawek skonfigurowanego przez użytkownika. Transfer danych do magazynu tworzy punkt odzyskiwania w magazynie do długoterminowego przechowywania. Transfer danych do magazynu rozpoczyna się dopiero po zakończeniu fazy migawki.

  ![Stan zadania tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Istnieją dwa **podzadania** uruchomione w zapleczu — jeden dla zadania tworzenia kopii zapasowej frontonu, który można sprawdzić w bloku szczegóły **zadania tworzenia kopii zapasowej** w następujący sposób:

  ![Stan zadania tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Zakończenie fazy **transferu danych do magazynu** może zająć wiele dni w zależności od rozmiaru dysków, liczby zmian na dysk i kilku innych czynników.

Stan zadania może się różnić w zależności od następujących scenariuszy:

**Migawka** | **Transferowanie danych do magazynu** | **Stan zadania**
--- | --- | ---
Zakończone | W toku | W toku
Zakończone | Pominięto | Zakończone
Zakończone | Zakończone | Zakończone
Zakończone | Niepowodzenie | Ukończono z ostrzeżeniem
Niepowodzenie | Niepowodzenie | Niepowodzenie

Teraz dzięki tej możliwości dla tej samej maszyny wirtualnej dwa kopie zapasowe mogą działać równolegle, ale w każdej fazie (migawka, transfer danych do magazynu) można uruchomić tylko jedno zadanie podrzędne. W związku z tym w przypadku zadania tworzenia kopii zapasowej w toku zakończyło się niepowodzeniem wykonywania kopii zapasowej w następnym dniu. Kopie zapasowe kolejnych dni mogą mieć ukończoną migawkę **,** podczas gdy zadanie tworzenia kopii zapasowej w danym dniu jest w toku.
Przyrostowy punkt odzyskiwania utworzony w magazynie będzie przechwytywał wszystkie zmiany z ostatniego punktu odzyskiwania utworzonego w magazynie. Użytkownik nie ma wpływu na koszty.

## <a name="optional-steps-install-agentallow-outbound"></a>Kroki opcjonalne (Instalacja agenta/Zezwalaj na ruch wychodzący)

### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny wirtualnej

Azure Backup tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przez zainstalowanie rozszerzenia agenta maszyny wirtualnej platformy Azure uruchomionego na komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, Agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę wirtualną lub migrujesz maszynę lokalną, może być konieczne ręczne zainstalowanie agenta, zgodnie z podsumowaniem w tabeli.

**VM** | **Szczegóły**
--- | ---
**Windows** | 1. [Pobierz i zainstaluj](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) plik msi agenta.<br/><br/> 2. Zainstaluj program z uprawnieniami administratora na komputerze.<br/><br/> 3. Sprawdź instalację. W *C:\WindowsAzure\Packages* na maszynie wirtualnej kliknij prawym przyciskiem myszy pozycję **WaAppAgent. exe** > **Właściwości**. Na karcie **szczegóły** **Wersja produktu** powinna mieć wartość 2.6.1198.718 lub wyższą.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej, a [następnie ponownie zainstaluj agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Zainstaluj program przy użyciu KCO lub pakietu DEB z repozytorium pakietu dystrybucji. Jest to preferowana metoda instalowania i uaktualniania agenta systemu Linux platformy Azure. Wszyscy [pozatwierdzeni dostawcy dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrują pakiet agenta platformy Azure z systemem Linux z obrazami i repozytoriami. Agent jest dostępny w serwisie [GitHub](https://github.com/Azure/WALinuxAgent), ale nie zalecamy instalacji.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej, i zaktualizuj pliki binarne.

### <a name="explicitly-allow-outbound-access"></a>Jawnie Zezwalaj na dostęp wychodzący

Rozszerzenie kopii zapasowej uruchomione na maszynie wirtualnej wymaga dostępu wychodzącego do publicznych adresów IP platformy Azure.

* Zazwyczaj nie trzeba jawnie zezwalać na wychodzący dostęp sieciowy dla maszyny wirtualnej platformy Azure w celu komunikowania się z Azure Backup.
* Jeśli wystąpią problemy z nawiązywaniem połączenia z maszynami wirtualnymi lub jeśli podczas próby nawiązania połączenia zobaczysz błąd **ExtensionSnapshotFailedNoNetwork** , należy jawnie zezwolić na dostęp, aby rozszerzenie kopii zapasowej mogły komunikować się z publicznymi adresami IP platformy Azure na potrzeby tworzenia kopii zapasowych. Metody dostępu są podsumowane w poniższej tabeli.

**Opcja** | **Akcja** | **Szczegóły**
--- | --- | ---
**Konfigurowanie reguł sieciowej grupy zabezpieczeń** | Zezwalaj na [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Zamiast zezwalać na każdy zakres adresów i zarządzać nim, można dodać regułę, która umożliwia dostęp do usługi Azure Backup przy użyciu [tagu usługi](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [Dowiedz się więcej](../virtual-network/security-overview.md#service-tags) o tagach usługi.<br/><br/> Tagi usług upraszczają zarządzanie dostępem i nie wiążą się z dodatkowymi kosztami.
**Wdrażanie serwera proxy** | Wdróż serwer proxy HTTP na potrzeby routingu ruchu. | Zapewnia dostęp do całego systemu Azure, a nie tylko magazynu.<br/><br/> Szczegółowa kontrola nad adresami URL magazynu jest dozwolona.<br/><br/> Pojedynczy punkt dostępu do Internetu dla maszyn wirtualnych.<br/><br/> Dodatkowe koszty dla serwera proxy.
**Konfigurowanie zapory platformy Azure** | Zezwalanie na ruch przez zaporę platformy Azure na maszynie wirtualnej przy użyciu znacznika nazwy FQDN dla usługi Azure Backup | Prosta do użycia, jeśli masz skonfigurowaną zaporę platformy Azure w podsieci sieci wirtualnej.<br/><br/> Nie można utworzyć własnych tagów FQDN ani zmodyfikować nazw FQDN w tagu.<br/><br/> Jeśli maszyny wirtualne platformy Azure mają dyski zarządzane, może być konieczne otwarcie dodatkowego portu (8443) na zaporach.

#### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Nawiązywanie łączności z sieciowej grupy zabezpieczeń, przez serwer proxy lub przez zaporę

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Konfigurowanie reguły sieciowej grupy zabezpieczeń w celu zezwalania na dostęp wychodzący do platformy Azure

Jeśli sieciowej grupy zabezpieczeń zarządza dostępem do maszyny wirtualnej, Zezwól na dostęp wychodzący dla magazynu kopii zapasowych do wymaganych zakresów i portów.

1. We właściwościach maszyny wirtualnej > **sieci**wybierz pozycję **Dodaj regułę portu wychodzącego**.
2. W obszarze **Dodawanie reguły zabezpieczeń ruchu wychodzącego**wybierz pozycję **Zaawansowane**.
3. W obszarze **Źródło**wybierz pozycję **VirtualNetwork**.
4. W obszarze **zakresy portów źródłowych**Wprowadź gwiazdkę (*), aby zezwolić na dostęp wychodzący z dowolnego portu.
5. W obszarze **Lokalizacja docelowa**wybierz pozycję **tag usługi**. Z listy wybierz pozycję **Storage. region**. Region polega na tym, że magazyn i maszyny wirtualne, których kopię zapasową chcesz utworzyć, znajdują się w lokalizacji.
6. W obszarze **zakresy portów docelowych**wybierz port.
    * Maszyna wirtualna korzystająca z dysków niezarządzanych z niezaszyfrowanym kontem magazynu: 80
    * Maszyna wirtualna korzystająca z dysków niezarządzanych z zaszyfrowanego konta magazynu: 443 (ustawienie domyślne)
    * Maszyna wirtualna z użyciem dysków zarządzanych: 8443.
7. W obszarze **Protokół**wybierz pozycję **TCP**.
8. W obszarze **priorytet**Określ wartość priorytetu mniejszą niż wszelkie wyższe reguły odmowy.

   Jeśli masz regułę odmawiającą dostępu, Nowa reguła zezwalania musi być wyższa. Na przykład jeśli masz zestaw reguł **Deny_All** w priorytecie 1000, Nowa reguła musi być ustawiona na wartość mniejszą niż 1000.
9. Podaj nazwę i opis reguły, a następnie wybierz **przycisk OK**.

Regułę sieciowej grupy zabezpieczeń można zastosować do wielu maszyn wirtualnych, aby zezwolić na dostęp wychodzący. Ten film wideo przeprowadzi Cię przez proces.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

##### <a name="route-backup-traffic-through-a-proxy"></a>Kierowanie ruchu kopii zapasowej za pomocą serwera proxy

Możesz kierować ruchem kopii zapasowej za pomocą serwera proxy, a następnie nadawać serwerowi proxy dostęp do wymaganych zakresów platformy Azure. Skonfiguruj maszynę wirtualną serwera proxy, aby zezwolić na następujące:

* Maszyna wirtualna platformy Azure powinna kierować cały ruch HTTP związany z publicznym Internetem za pośrednictwem serwera proxy.
* Serwer proxy powinien zezwalać na ruch przychodzący z maszyn wirtualnych w odpowiedniej sieci wirtualnej.
* **Sieciowej grupy zabezpieczeń musi mieć regułę** zezwalającą na ruch wychodzący z maszyny wirtualnej serwera proxy.

###### <a name="set-up-the-proxy"></a>Konfigurowanie serwera proxy

Jeśli nie masz serwera proxy konta systemowego, skonfiguruj go w następujący sposób:

1. Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Uruchom **PsExec. exe-i-s cmd. exe** , aby uruchomić wiersz polecenia w ramach konta systemowego.
3. Uruchom przeglądarkę w kontekście systemu. Na przykład użyj **użycie ścieżki%ProgramFiles%\Internet Explorer\iexplore.exe** dla programu Internet Explorer.  
4. Zdefiniuj ustawienia serwera proxy.
   * Na komputerach z systemem Linux:
     * Dodaj ten wiersz do pliku **/etc/environment** :
       * **http_proxy = http:\//proxy adres IP: Port serwera proxy**
     * Dodaj te wiersze do pliku **/etc/waagent.conf** :
         * **HttpProxy. host = adres IP serwera proxy**
         * **HttpProxy. Port = port serwera proxy**
   * Na komputerach z systemem Windows w ustawieniach przeglądarki Określ, czy powinien być używany serwer proxy. Jeśli obecnie używasz serwera proxy na koncie użytkownika, możesz użyć tego skryptu, aby zastosować ustawienie na poziomie konta systemowego.

       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Zezwalaj na połączenia przychodzące na serwerze proxy

Zezwalaj na połączenia przychodzące w ustawieniach serwera proxy.

1. W zaporze systemu Windows Otwórz **zaporę systemu Windows z zabezpieczeniami zaawansowanymi**.
2. Kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego** > **nowej reguły**.
3. W obszarze **Typ reguły**wybierz pozycję **niestandardowy** > **dalej**.
4. W **programie**wybierz kolejno pozycje **Wszystkie programy** > **dalej**.
5. W obszarze **Protokoły i porty**:
   * Ustaw typ na **TCP**.
   * Ustawianie **portów lokalnych** dla **określonych portów**.
   * Ustaw **Port zdalny** na **wszystkie porty**.

6. Zakończ pracę kreatora i określ nazwę reguły.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Dodawanie reguły wyjątku do sieciowej grupy zabezpieczeń dla serwera proxy

W sieciowej grupy zabezpieczeń **-Blokada**niedostępności Zezwalaj na ruch z dowolnego portu w 10.0.0.5 do dowolnego adresu internetowego na porcie 80 (http) lub 443 (https).

Poniższy skrypt programu PowerShell zawiera przykład do zezwalania na ruch.
Zamiast zezwalać na ruch wychodzący do wszystkich publicznych adresów internetowych, możesz określić zakres adresów IP (`-DestinationPortRange`) lub użyć znacznika usługi Storage. region.

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Zezwalaj na dostęp przez zaporę za pomocą znacznika nazwy FQDN

Zaporę platformy Azure można skonfigurować tak, aby zezwalała na dostęp wychodzący dla ruchu sieciowego do Azure Backup.

* [Dowiedz się więcej o](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) wdrażaniu zapory platformy Azure.
* [Przeczytaj informacje](https://docs.microsoft.com/azure/firewall/fqdn-tags) Tagi FQDN.

>[!NOTE]
> Azure Backup teraz obsługuje selektywne tworzenie kopii zapasowych i przywracanie dysków przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.
>
>Obecnie Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (systemu operacyjnego i danych) w maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowej maszyny wirtualnej. Funkcja wykluczania dysku umożliwia utworzenie kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb tworzenia kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dotyczące dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzestawu dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i magazynu.
>
> To rozwiązanie jest szczególnie przydatne w następujących scenariuszach:
>  
>1. Istnieje krytyczne dane, których kopie zapasowe mają być tworzone tylko w jednym dysku, i nie chcesz tworzyć kopii zapasowych reszty dysków podłączonych do maszyny wirtualnej. Pozwala to zminimalizować koszty magazynu kopii zapasowych.  
>2. Istnieją inne rozwiązania do tworzenia kopii zapasowych danych maszyny wirtualnej. Na przykład wykonujesz kopię zapasową baz danych lub danych przy użyciu innego rozwiązania do tworzenia kopii zapasowych, a chcesz użyć kopii zapasowej na poziomie maszyny wirtualnej platformy Azure na potrzeby pozostałej części dysków i danych, aby utworzyć wydajny i niezawodny system z wykorzystaniem najlepszych dostępnych funkcji.
>
>Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas na AskAzureBackupTeam@microsoft.com

## <a name="next-steps"></a>Następne kroki

* Rozwiązywanie problemów z [agentami maszyn wirtualnych platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) lub [kopiami zapasowymi maszyny wirtualnej platformy Azure](backup-azure-vms-troubleshoot.md).
* [Przywróć](backup-azure-arm-restore-vms.md) Maszyny wirtualne platformy Azure.
