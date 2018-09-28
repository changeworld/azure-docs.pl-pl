---
title: 'Usługa Azure Backup: Przygotowanie do tworzenia kopii zapasowych maszyn wirtualnych'
description: Upewnij się, że środowisko jest przygotowane do kopii zapasowych maszyn wirtualnych na platformie Azure.
services: backup
author: markgalioto
manager: carmonm
keywords: kopie zapasowe Tworzenie kopii zapasowej;
ms.service: backup
ms.topic: conceptual
ms.date: 9/10/2018
ms.author: markgal
ms.openlocfilehash: 00432e1b5a181c57e4901b684d0a86c1cc843037
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408984"
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Przygotowywanie środowiska do tworzenia kopii zapasowych maszyn wirtualnych wdrożonych przez program Resource Manager

Ten artykuł zawiera instrukcje dotyczące przygotowania środowiska do tworzenia kopii zapasowej maszyny wirtualnej (VM) wdrożone usługi Azure Resource Manager. Kroki opisane w procedurach użyj witryny Azure portal. Podczas wykonywania kopii zapasowej maszyny wirtualnej, punktów odzyskiwania lub dane kopii zapasowej są przechowywane w magazynie usługi Recovery Services. Magazyny usługi Recovery Services przechowywać dane kopii zapasowej dla maszyn wirtualnych klasycznych i wdrożeniu usługi Resource Manager.

> [!NOTE]
> Platforma Azure oferuje dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [usługi Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).

Przed rozpoczęciem ochrony (lub utworzyć kopię zapasową) wdrożonych przez Menedżera zasobów maszyny wirtualnej, upewnij się, że spełnione następujące wymagania wstępne:

* Tworzenie lub identyfikowanie magazynu usługi Recovery Services *w tym samym regionie co maszyna wirtualna*.
* Wybierz scenariusz, definiowania zasad tworzenia kopii zapasowej i zdefiniuj elementy podlegające ochronie.
* Sprawdź instalację agenta maszyny Wirtualnej (rozszerzenie) na maszynie wirtualnej.
* Sprawdź łączność sieciową.
* W przypadku maszyn wirtualnych systemu Linux do dostosowywania środowiska tworzenia kopii zapasowych spójnych z aplikacją kopii zapasowych, należy wykonać [kroki, aby skonfigurować skrypty przed i po utworzeniu migawki](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Jeśli te warunki są już istnieją w Twoim środowisku, przejdź do [tworzenie kopii zapasowych maszyn wirtualnych z systemem](backup-azure-arm-vms.md) artykułu. Jeśli potrzebujesz skonfigurować, lub zaznacz jedną z tych wymagań wstępnych, w tym artykule poprowadzi Cię przez kroki.

## <a name="supported-operating-systems-for-backup"></a>Obsługiwane systemy operacyjne dla kopii zapasowej

 * **Linux**: Usługa Azure Backup obsługuje [listę dystrybucji, które platforma Azure wspiera](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), z wyjątkiem CoreOS Linux. Aby uzyskać listę systemów operacyjnych Linux, które obsługują Przywracanie plików, zobacz [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej](backup-azure-restore-files-from-vm.md#for-linux-os).

    > [!NOTE] 
    > Inne dystrybucje bring-your-own Linux może działać, dopóki agent maszyny Wirtualnej jest dostępne na maszynie wirtualnej i obsługiwany język Python. Jednak te dystrybucji nie są obsługiwane.
    >
 * **Windows Server**, **klienta Windows**: wersje starsze niż Windows Server 2008 R2 lub Windows 7, nie są obsługiwane.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Ograniczenia w przypadku tworzenia kopii zapasowych i przywracania maszyny Wirtualnej
Przed przygotowaniem środowiska, należy zrozumieć następujące ograniczenia:

* Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu SSD w warstwie standardowa nie jest obecnie obsługiwane.
* Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu więcej niż 32 dyski danych nie jest obsługiwane.
* Tworzenie kopii zapasowych maszyn wirtualnych za pomocą zastrzeżonego adresu IP i nie ma zdefiniowanego punktu końcowego nie jest obsługiwane.
* Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux, szyfrowane za pomocą funkcji szyfrowania Linux Unified klucz instalacji (LUKS) nie jest obsługiwane.
* Nie zaleca się tworzenie kopii zapasowych maszyn wirtualnych, które zawierają udostępnionych woluminów klastra (CSV) lub serwera plików skalowalnego w poziomie konfiguracji. Jeśli będą wykonywane, awarii składników zapisywania CSV jest oczekiwany. Wymagają one, obejmujące wszystkie maszyny wirtualne, które uwzględnione w konfiguracji klastra podczas wykonywania zadania migawki. Usługa Azure Backup nie obsługuje spójność wielu maszyn wirtualnych. 
* Dane kopii zapasowej nie zawiera sieciowych zainstalowane dyski dołączone do maszyny Wirtualnej.
* Zamiana istniejącej maszyny wirtualnej podczas przywracania nie jest obsługiwana. Jeśli użytkownik podejmie próbę przywrócenia maszyny Wirtualnej, gdy maszyna wirtualna istnieje, operacja przywracania kończy się niepowodzeniem.
* Między regionami, tworzenie kopii zapasowej i przywracania nie są obsługiwane.
* Podczas konfigurowania kopii zapasowej, upewnij się, że **zapory i sieci wirtualne** ustawienia konta magazynu, Zezwalaj na dostęp ze wszystkich sieci.
* Dla wybranych sieci po skonfigurowaniu ustawień zapory i sieci wirtualnej dla konta magazynu, wybierz **dozwolonych zaufanych usług firmy Microsoft dostęp do tego konta magazynu** jako wyjątek, aby włączyć usługi Azure Backup dostęp do konta magazynu z ograniczeniami sieci. Odzyskiwanie na poziomie elementu nie jest obsługiwana dla kont magazynu w sieci z ograniczeniami.
* Można utworzyć kopię zapasową maszyn wirtualnych we wszystkich publicznych regionach platformy Azure. (Zobacz [Lista kontrolna](https://azure.microsoft.com/regions/#services) obsługiwanych regionów.) Jeśli obecnie jest obsługiwany region, którego szukasz, nie pojawi się na liście rozwijanej podczas tworzenia magazynu.
* Przywracanie kontrolera domeny (DC) maszyny Wirtualnej, która jest częścią konfiguracji kontrolera domeny na wielu jest obsługiwany tylko przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz [Przywracanie kontrolera domeny multi DC](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Migawek na dysku z włączonym akceleratorem zapisu nie jest obsługiwane. To ograniczenie blokuje możliwość usługi Azure Backup wykonywania migawki spójnej z aplikacją wszystkich dysków maszyny wirtualnej.
* Przywracanie maszyn wirtualnych, które mają następujące specjalne konfiguracje sieci jest obsługiwane tylko za pomocą programu PowerShell. Maszyny wirtualne tworzone za pomocą przepływu pracy przywracania w interfejsie użytkownika nie będą miały te konfiguracje sieci, po zakończeniu operacji przywracania. Aby dowiedzieć się więcej, zobacz [przywracania maszyn wirtualnych ze specjalnymi konfiguracjami sieci](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Maszyn wirtualnych w ramach konfiguracji usługi równoważenia obciążenia (wewnętrznych i zewnętrznych)
  * Maszyny wirtualne za pomocą wielu zastrzeżonych adresów IP
  * Maszyny wirtualne z wieloma kartami sieciowymi

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Utwórz magazyn usługi Recovery Services dla maszyny Wirtualnej
Magazyn usługi Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania, które zostały utworzone wraz z upływem czasu. Magazyn usługi Recovery Services zawiera także zasady tworzenia kopii zapasowych, które są skojarzone z chronionych maszyn wirtualnych.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Na **Centrum** menu, wybierz opcję **Przeglądaj**, a następnie wpisz **usługi Recovery Services**. Po rozpoczęciu wpisywania, dane wejściowe filtrowanie listy zasobów. Wybierz **Magazyny usługi Recovery Services**.

    ![Wpisując w polu i wybierając polecenie "Magazynów usługi Recovery Services", w wynikach](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Zostanie wyświetlona lista magazynów Usług odzyskiwania.
1. Na **Magazyny usługi Recovery Services** menu, wybierz opcję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **Magazyny usługi Recovery Services** zostanie otwarte okienko. Monit o podanie informacji dla **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji**.

    ![Okienko "Magazyny usługi recovery Services"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
1. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikalna w tej subskrypcji platformy Azure. Wpisz nazwę, która zawiera 2 do 50 znaków. Musi zaczynać się literą i może zawierać tylko litery, cyfry i łączniki.
1. Wybierz **subskrypcji** Aby wyświetlić listę dostępnych subskrypcji. Jeśli nie masz pewności, której subskrypcji użyć, użyj wartości domyślnej (lub sugerowane) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy pracy lub nauki jest skojarzony z wieloma subskrypcjami platformy Azure.
1. Wybierz **grupy zasobów** Aby wyświetlić listę dostępnych grup zasobów lub wybierz **New** do tworzenia nowej grupy zasobów. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
1. Wybierz **lokalizacji** do wybierz region geograficzny magazynu. Magazyn *musi* znajdować się w tym samym regionie, w którym znajdują się maszyny wirtualne, które mają być chronione.

   > [!IMPORTANT]
   > Jeśli masz pewności co do lokalizacji, w której istnieje maszyna wirtualna, zamknij okno dialogowe tworzenia magazynu, a następnie przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w wielu regionach, należy utworzyć magazyn usługi Recovery Services w każdym regionie. Przed przejściem do następnej lokalizacji utwórz magazyn w pierwszej lokalizacji. Nie ma potrzeby określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn usługi Recovery Services i usługi Azure Backup określają, automatycznie.
   >
   >

1. Wybierz pozycję **Utwórz**. Utworzenie magazynu Usług odzyskiwania może zająć trochę czasu. Monitoruj powiadomienia o stanie wyświetlane w obszarze prawym górnym rogu portalu. Po utworzeniu magazynu pojawi się na liście magazynów usługi Recovery Services. Jeśli nie widzisz swojego magazynu wybierz **Odśwież**.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Magazyn został już utworzony, więc teraz zajmiemy się skonfigurowaniem jego replikacji.

## <a name="set-storage-replication"></a>Replikacja magazynu zestawu
Dla opcji replikacji magazynu pozwala wybrać magazyn geograficznie nadmiarowy i Magazyn lokalnie nadmiarowy. Domyślnie magazyn jest nadmiarowy geograficznie. Pozostaw ustawienie opcji jako magazyn geograficznie nadmiarowy dla Twoja podstawowa kopia zapasowa. Tańsze opcji, które nie są tak niezawodne, wybrać magazyn lokalnie nadmiarowy.

Aby edytować ustawienia replikacji magazynu:

1. Na **Magazyny usługi Recovery Services** okienku wybierz swój magazyn.
    Po wybraniu magazynu, **ustawienia** okienko, (który ma nazwę magazynu u góry) i otwórz okienko szczegółów magazynu.

   ![Wybierz magazyn z listy magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

1. Na **ustawienia** okienko, przewiń w dół za pomocą suwaka pionowy **zarządzanie** sekcji, a następnie wybierz pozycję **infrastruktura zapasowa**. W **ogólne** zaznacz **konfiguracji kopii zapasowej**. Na **konfiguracji kopii zapasowej** okienku wybierz opcję replikacji swojego magazynu. Domyślnie magazyn jest nadmiarowy geograficznie.

   ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, nadal korzystać z magazynu geograficznie nadmiarowego. Jeśli używasz platformy Azure jako punktu końcowego magazynu kopii zapasowych innego niż podstawowy, wybierz magazyn lokalnie nadmiarowy. Przeczytaj więcej na temat opcji magazynu na platformie [Omówienie replikacji usługi Azure Storage](../storage/common/storage-redundancy.md).

1. Jeśli zmienisz typ replikacji magazynu, wybierz **Zapisz**.
    
Po wybraniu opcji magazynu dla magazynu możesz skojarzyć maszyny Wirtualnej w magazynie. Aby rozpocząć kojarzenie, należy odnaleźć i zarejestrować maszyny wirtualne Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Wybierz cel kopii zapasowej, Ustaw zasady i zdefiniuj elementy podlegające ochronie
Przed zarejestrowaniem maszyny wirtualnej przy użyciu magazynu usługi Recovery Services, należy uruchomić proces wykrywania, aby zidentyfikować wszystkie nowe maszyny wirtualne, które są dodawane do subskrypcji. Odnajdywanie proces wyszukuje na platformie Azure listę maszyn wirtualnych w subskrypcji. Jeśli zostaną znalezione nowe maszyny wirtualne, portalu jest wyświetlany, nazwa usługi w chmurze i skojarzonego regionu. W witrynie Azure portal *scenariusza* wprowadzić w magazynie usługi Recovery Services. *Zasady* są harmonogramem określającym częstotliwość i czas punktów odzyskiwania. Zasady zawierają także zakres przechowywania dla punktów odzyskiwania.

1. Jeśli Twój magazyn Usług odzyskiwania jest już otwarty, przejdź do kroku 2. Jeśli nie masz magazyn usługi Recovery Services, Otwórz, otwórz [witryny Azure portal](https://portal.azure.com/). Na **Centrum** menu, wybierz opcję **więcej usług**.

   a. Na liście zasobów wpisz **Usługi odzyskiwania**. Po rozpoczęciu wpisywania, dane wejściowe filtrowanie listy. Po wyświetleniu **Magazyny usługi Recovery Services**, wybierz ją.

      ![Wpisując w polu i wybierając polecenie "Magazynów usługi Recovery Services", w wynikach](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Zostanie wyświetlona lista magazynów Usług odzyskiwania. W przypadku żadnych magazynów w ramach subskrypcji, ta lista jest pusta.

      ![Widok listy magazynów usługi Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Wybierz magazyn z listy magazynów Usług odzyskiwania.

      **Ustawienia** magazynu Otwórz okienko i pulpit nawigacyjny wybranego magazynu.

      ![Pulpit nawigacyjny w okienku i magazynu ustawień](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
1. W menu pulpitu nawigacyjnego magazynu, wybierz **kopii zapasowej**.

   ![Przycisk tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Kopii zapasowej** i **cel kopii zapasowej** otwieranie okienka.

1. Na **cel kopii zapasowej** Ustaw okienku **gdzie jest uruchomione Twoje obciążenie?** jako **Azure** i **co ma zawierać kopia zapasowa?** jako  **Maszyna wirtualna**. Następnie wybierz przycisk **OK**.

   ![Kopia zapasowa i cel kopii zapasowej okienka](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   W tym kroku rejestruje rozszerzenie maszyny Wirtualnej w magazynie. **Cel kopii zapasowej** okienko zostanie zamknięte, a **zasady tworzenia kopii zapasowej** zostanie otwarte okienko.

   !["Kopia zapasowa" i "Zasady kopii zapasowych" okienek](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
1. Na **zasady tworzenia kopii zapasowej** okienku Wybieranie zasad kopii zapasowych, który chcesz zastosować do magazynu.

   ![Wybór zasad tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Szczegóły domyślnych zasad znajdują się w menu rozwijanym. Aby utworzyć nowe zasady, wybierz opcję **Utwórz nowy** z menu rozwijanego. Instrukcje dotyczące definiowania zasad tworzenia kopii zapasowych można znaleźć w sekcji [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Wybierz **OK** Aby skojarzyć zasady tworzenia kopii zapasowych w magazynie.

   **Zasady tworzenia kopii zapasowej** okienko zostanie zamknięte, a **wybierz maszyny wirtualne** zostanie otwarte okienko.
1. Na **wybierz maszyny wirtualne** okienku wybierz maszyny wirtualne do skojarzenia z określonymi zasadami i wybierz **OK**.

   ![Okienko "Wybierz maszyny wirtualne"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Wybrana maszyna wirtualna jest weryfikowana. Jeśli nie widzisz oczekiwanych maszyn wirtualnych, sprawdź, czy maszyny wirtualne znajdują się w tym samym regionie platformy Azure co magazyn usługi Recovery Services. Jeśli nadal nie widzisz maszyn wirtualnych, sprawdź, czy nie są już chronione w innym magazynie. Pulpitu nawigacyjnego magazynu zawiera region, w której istnieje magazyn usługi Recovery Services.

1. Skoro zdefiniowano wszystkie ustawienia magazynu, na **kopii zapasowej** okienku wybierz **Włącz kopię zapasową**. Ten krok powoduje wdrożenie zasad dla magazynu i maszyn wirtualnych. Ten krok nie powoduje utworzenia początkowego punktu odzyskiwania dla maszyny wirtualnej.

   ![Przycisk "Włącz kopię zapasową"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po pomyślnym włączeniu kopii zapasowej zasady tworzenia kopii zapasowej zostanie uruchomiony zgodnie z harmonogramem. Jeśli chcesz wygenerować zadania tworzenia kopii zapasowej na żądanie, na wykonywanie kopii zapasowych maszyn wirtualnych teraz wyświetlić [wyzwalanie zadania tworzenia kopii zapasowej](./backup-azure-vms-first-look-arm.md#initial-backup).

Jeśli masz problemy z zarejestrowaniem maszyny wirtualnej, zobacz następujące informacje na temat instalowania agenta maszyny Wirtualnej oraz łączność sieciową. Prawdopodobnie nie potrzebujesz następujących informacji w przypadku ochrony maszyn wirtualnych utworzonych na platformie Azure. Pamiętaj jednak, jeśli wykonano migrację maszyn wirtualnych na platformie Azure, poprawnie zainstalowany agent maszyny Wirtualnej i maszyny wirtualnej mogą komunikować się z siecią wirtualną.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Zainstaluj agenta maszyny Wirtualnej na maszynie wirtualnej
Dla rozszerzenia kopii zapasowej do pracy, Azure [agenta maszyny Wirtualnej](../virtual-machines/extensions/agent-windows.md) musi być zainstalowany na maszynie wirtualnej platformy Azure. Jeśli maszyna wirtualna została utworzona w portalu Azure Marketplace, agent maszyny Wirtualnej jest już obecny w maszynie wirtualnej. 

Poniżej znajduje się w sytuacjach, w którym są *nie* używania maszyny Wirtualnej utworzone w portalu Azure Marketplace. **Na przykład po migracji maszyny Wirtualnej z lokalnego centrum danych. W takim przypadku agenta maszyny Wirtualnej musi być zainstalowany w celu włączenia ochrony maszyny wirtualnej.**

**Uwaga**: po zainstalowaniu agenta maszyny Wirtualnej, należy użyć programu Azure PowerShell można zaktualizować właściwości ProvisionGuestAgent, więc Azure wie, maszyny Wirtualnej został zainstalowany agent. 

Jeśli masz problemy z tworzeniem kopii zapasowej maszyny Wirtualnej platformy Azure, skorzystaj z poniższej tabeli, aby sprawdzić, czy agent maszyny Wirtualnej platformy Azure został poprawnie zainstalowany na maszynie wirtualnej. W tabeli przedstawiono dodatkowe informacje na temat agenta maszyny Wirtualnej dla Windows i maszyn wirtualnych systemu Linux.

| **Operacja** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalowanie agenta maszyny wirtualnej |Pobierz i zainstaluj [plik MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Do ukończenia procesu instalacji niezbędne są uprawnienia administratora. |<li> Zainstaluj najnowszą wersję [agenta systemu Linux](../virtual-machines/extensions/agent-linux.md). Do ukończenia procesu instalacji niezbędne są uprawnienia administratora. Zaleca się zainstalowanie agenta ze swojego repozytorium dystrybucji. Firma Microsoft **nie zaleca się** Instalowanie agenta maszyny Wirtualnej systemu Linux bezpośrednio z serwisu github.  |
| Aktualizowanie agenta maszyny wirtualnej |Aktualizowanie agenta maszyny wirtualnej jest równie proste, jak ponowne zainstalowanie [plików binarnych agenta maszyny wirtualnej](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Upewnij się, że żadna operacja tworzenia kopii zapasowej nie jest uruchomiona podczas aktualizowania agenta maszyny wirtualnej. |Postępuj zgodnie z instrukcjami dotyczącymi [aktualizowania agenta maszyny wirtualnej systemu Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Zalecamy aktualizowanie agenta ze swojego repozytorium dystrybucji. Firma Microsoft **nie zaleca się** aktualizowania agenta maszyny Wirtualnej systemu Linux bezpośrednio z serwisu github.<br>Upewnij się, że podczas aktualizowania agenta maszyny wirtualnej żadna operacja tworzenia kopii zapasowej nie jest uruchomiona. |
| Sprawdzanie poprawności instalacji agenta maszyny wirtualnej |<li>Przejdź do folderu *C:\WindowsAzure\Packages* w maszynie wirtualnej Azure. <li>Powinien znajdować się w nim plik WaAppAgent.exe.<li> Kliknij plik prawym przyciskiem myszy, przejdź do opcji **Właściwości**, a następnie wybierz kartę **Szczegóły**. W polu Wersja produktu powinna znajdować się wartość 2.6.1198.718 lub wyższa. |ND |

### <a name="backup-extension"></a>Rozszerzenie kopii zapasowej
Po zainstalowaniu agenta maszyny Wirtualnej na maszynie wirtualnej usługa Azure Backup instaluje rozszerzenie kopii zapasowej agenta maszyny Wirtualnej. Usługa Backup bezproblemowo uaktualnienia i poprawki rozszerzenie kopii zapasowej.

Usługa Backup instaluje rozszerzenie kopii zapasowej, czy maszyna wirtualna jest uruchomiona. Uruchomiona maszyna wirtualna zapewnia największe prawdopodobieństwo uzyskania punktu odzyskiwania spójnego z aplikacją. Usługa Backup będzie jednak nadal do tworzenia kopii zapasowej maszyny Wirtualnej, nawet jeśli jest ona wyłączona i nie można zainstalować rozszerzenia. Jest to nazywane *maszyny Wirtualnej w trybie offline*. W takim przypadku punkt odzyskiwania będzie *awaryjnie spójny*.

## <a name="establish-network-connectivity"></a>Ustanowienie połączenia z siecią
Aby zarządzać migawki maszyny Wirtualnej, rozszerzenie kopii zapasowej, musi mieć łączność platformy Azure publicznych adresów IP. Bez odpowiednie połączenie z Internetem limit czasu żądań HTTP maszyny wirtualnej, a operacja tworzenia kopii zapasowej nie powiedzie się. Jeśli wdrożenie ma ograniczenia dostępu w miejscu — przy użyciu sieciowej grupy zabezpieczeń (NSG), na przykład wybierz jedną z tych opcji, aby zapewni przejrzystą ścieżkę ruchu kopii zapasowej:

* [Lista dozwolonych adresów IP centrum danych platformy Azure zakresy](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Wdrażanie serwera proxy HTTP dla routingu ruchu.

Podczas wybierania opcji wad i zalet należą do zakresu od możliwości zarządzania, kontrolę i kosztów.

| Opcja | Zalety | Wady |
| --- | --- | --- |
| Lista dozwolonych adresów IP, zakresy |Bez dodatkowych kosztów.<br><br>W przypadku otwierania dostępu w sieciowej grupie zabezpieczeń, użyj **AzureNetworkSecurityRule zestaw** polecenia cmdlet. |Trudne w zarządzaniu jako objęte zmień zakresy adresów IP wraz z upływem czasu.<br><br>Zapewnia dostęp do całej platformy Azure i nie tylko magazyn. |
| Użyj serwera proxy HTTP |Zapewnia szczegółową kontrolę na serwerze proxy magazynu dozwolone adresy URL.<br><br>Pojedynczy punkt internet dostępu do maszyn wirtualnych.<br><br>Nie może ulec zmianie adresów IP platformy Azure. |Dodatkowe koszty uruchamiania maszyn wirtualnych z oprogramowaniem serwera proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Zakresy adresów IP dozwolonych centrum danych platformy Azure
Do listy dozwolonych zakresów adresów IP centrum danych platformy Azure, zobacz [witryny sieci Web Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) szczegółowe informacje na temat zakresów adresów IP i instrukcje.

Zezwalaj na połączenia do magazynu w określonym regionie przy użyciu [tagów usług](../virtual-network/security-overview.md#service-tags). Upewnij się, że reguła, która umożliwia uzyskanie dostępu do konta magazynu ma wyższy priorytet niż regułę, która blokuje dostęp do Internetu. 

![Sieciowa grupa zabezpieczeń z tagami magazynu dla regionu](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Poniższy klip wideo przedstawiono procedury krok po kroku, aby skonfigurować tagi usługi: 

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!NOTE]
> Aby uzyskać listę tagów usługi storage i regiony, zobacz [tagów usług magazynu](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Użyj serwera proxy HTTP dla kopii zapasowych maszyn wirtualnych
Jeśli wykonujesz kopię zapasową maszyny Wirtualnej, rozszerzenie kopii zapasowej na maszynie Wirtualnej wysyła polecenia zarządzania migawki do usługi Azure Storage przy użyciu interfejsu API protokołu HTTPS. Kierowanie ruchu rozszerzenie kopii zapasowej za pośrednictwem serwera proxy HTTP, ponieważ jest jedynym składnikiem skonfigurowane do uzyskiwania dostępu do publicznej sieci internet.

> [!NOTE]
> Nie zaleca się oprogramowania określonego serwera proxy, która powinna być używana. Upewnij się, wybierz serwer proxy, która jest zgodna z krokami konfiguracji poniżej.
>
>

Na poniższej ilustracji przykład przedstawiono kroki trzech konfiguracji niezbędne do korzystania z serwera proxy HTTP:

* Trasy maszyn wirtualnych aplikacji cały ruch HTTP powiązany do publicznej sieci internet za pośrednictwem serwera proxy maszyny Wirtualnej.
* Maszyna wirtualna serwera proxy zezwala na ruch przychodzący z maszyn wirtualnych w sieci wirtualnej.
* Sieciową grupę zabezpieczeń o nazwie NF blokady musi regułę zabezpieczeń, która zezwala na ruch wychodzący z Internetu z maszyną Wirtualną serwera proxy.

Aby użyć serwera proxy HTTP do komunikowania się z publicznej sieci internet, wykonaj następujące czynności.

> [!NOTE]
> Te kroki odnoszą się określonej nazwy i wartości w tym przykładzie. Teraz podczas wprowadzania (lub wklejanie) szczegółów do Twojego kodu, użyj nazwy i wartości dla danego wdrożenia.

#### <a name="step-1-configure-outgoing-network-connections"></a>Krok 1: Konfigurowanie wychodzących połączeń sieciowych
###### <a name="for-windows-machines"></a>W przypadku komputerów Windows
Procedura ta konfiguruje konfigurację serwera proxy dla konta system lokalny.

1. Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
1. Otwórz program Internet Explorer, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

1. W programie Internet Explorer przejdź do **narzędzia** > **Opcje internetowe** > **połączeń** > **ustawienia sieci LAN**.
1. Sprawdź ustawienia serwera proxy dla konta system. Ustaw IP serwera proxy i port.
1. Zamknij program Internet Explorer.

Poniższy skrypt konfiguruje konfiguracji serwera proxy dla komputera i używa go na potrzeby cały wychodzący ruch protokołu HTTP lub HTTPS. Jeśli po skonfigurowaniu serwera proxy dla bieżącego konta użytkownika (nie konta systemu lokalnego), należy użyć tego skryptu, aby zastosować je do SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Jeśli zauważysz "(407) wymagane uwierzytelnianie serwera Proxy" w dzienniku serwera proxy, sprawdź, czy uwierzytelnianie jest poprawnie skonfigurowane.
>
>

###### <a name="for-linux-machines"></a>Dla maszyn z systemem Linux
Dodaj następujący wiersz do ```/etc/environment``` pliku:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Dodaj następujące wiersze do ```/etc/waagent.conf``` pliku:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Krok 2: Zezwalaj na połączenia przychodzące na serwerze proxy
1. Na serwerze proxy Otwórz zaporę Windows. Najprostszym sposobem na dostęp do zapory jest wyszukiwanie **Zapora Windows z zabezpieczeniami zaawansowanymi**.
1. W **Zapora Windows z zabezpieczeniami zaawansowanymi** okno dialogowe, kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego** i wybierz **nową regułę**.
1. W Kreatora nowej reguły przychodzącej na **typ reguły** wybierz opcję **niestandardowe** opcji, a następnie wybierz pozycję **dalej**.
1. Na **Program** wybierz opcję **wszystkie programy** i wybierz **dalej**.
1. Na **protokół i porty** strony, wprowadź następujące informacje i wybierz **dalej**:
   * Aby uzyskać **protokołu typu**, wybierz opcję **TCP**.
   * Aby uzyskać **port lokalny**, wybierz opcję **określonych portów**. W poniższym polu Określ numer portu serwera proxy, który został skonfigurowany.
   * Aby uzyskać **port zdalny**, wybierz opcję **wszystkie porty**.

Pozostałe kreatora Zaakceptuj ustawienia domyślne, aż dojdziesz do końca. Następnie Nazwij tę regułę. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Krok 3: Dodaj regułę wyjątku do sieciowej grupy zabezpieczeń
Następujące polecenie dodaje wyjątek do sieciowej grupy zabezpieczeń. Ten wyjątek zezwala na ruch TCP z dowolnego portu na 10.0.0.5 do dowolnego adresu internetowego na porcie 80 (HTTP) lub 443 (HTTPS). Jeśli potrzebujesz określonego portu w publicznej sieci internet, pamiętaj dodać ten port do ```-DestinationPortRange```.

W wierszu polecenia programu Azure PowerShell wpisz następujące polecenie:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub w przypadku dowolnej funkcji, które mają być wyświetlane uwzględnione, [Prześlij nam swoją opinię](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy przygotowania środowiska do tworzenia kopii zapasowej maszyny Wirtualnej, jest kolejnym krokiem logicznego, aby utworzyć kopię zapasową. Planowania artykuł zawiera bardziej szczegółowe informacje o tworzeniu kopii zapasowych maszyn wirtualnych.

* [Tworzenie kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md)
* [Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej](backup-azure-vms-introduction.md)
* [Zarządzanie kopiami zapasowymi maszyny wirtualnej](backup-azure-manage-vms.md)
