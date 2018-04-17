---
title: 'Kopia zapasowa Azure: Przygotowanie do tworzenia kopii zapasowych maszyn wirtualnych | Dokumentacja firmy Microsoft'
description: Upewnij się, że środowisko jest przygotowane do tworzenia kopii zapasowych maszyn wirtualnych na platformie Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Tworzenie kopii zapasowych; Tworzenie kopii zapasowej;
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2018
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 70c1553c166cc334f9db03c78139181c6f5c0553
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Przygotowywanie środowiska do tworzenia kopii zapasowych maszyn wirtualnych wdrożonych przez program Resource Manager

Ten artykuł zawiera kroki przygotowania środowiska do utworzenia kopii zapasowej maszyny wirtualnej (VM) wdrożone usługi Azure Resource Manager. Kroki opisane w procedurach za pomocą portalu Azure. Przechowywanie danych kopii zapasowej maszyny wirtualnej w magazynie usług odzyskiwania. Magazyn przechowuje dane kopii zapasowej dla maszyn wirtualnych klasycznego i wdrożeniu usługi Resource Manager.

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).

Przed ochrony (lub utworzyć kopię zapasową) maszyny wirtualnej wdrożone usługi Resource Manager, upewnij się, że istnieją następujące wymagania wstępne:

* Tworzenie magazynu usług odzyskiwania (lub Zidentyfikuj istniejącego magazynu usług odzyskiwania) *w tym samym regionie co maszyna wirtualna*.
* Wybierz scenariusz, definiowanie zasad tworzenia kopii zapasowej i zdefiniuj elementy do ochrony.
* Sprawdź instalację agenta maszyny Wirtualnej na maszynie wirtualnej.
* Sprawdź łączność sieciową.
* Dla maszyn wirtualnych systemu Linux, jeśli chcesz dostosować środowisko tworzenia kopii zapasowych spójnych z aplikacją kopii zapasowych, wykonaj [kroki, aby skonfigurować skrypty migawki przed i po utworzeniu migawki](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Jeśli te warunki istnieje już w danym środowisku, przejdź do [kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md) artykułu. Jeśli musisz skonfigurować lub sprawdzanie wszystkich wymagań wstępnych, w tym artykule poprowadzi Cię przez kroki.

## <a name="supported-operating-systems-for-backup"></a>Obsługiwane systemy operacyjne dla kopii zapasowej
 * **Linux**: kopia zapasowa Azure obsługuje [listę dystrybucji, które zatwierdza Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), z wyjątkiem CoreOS Linux. 
 
    > [!NOTE] 
    > Innych bring-your — właścicielem — dystrybucje systemu Linux może działać, dopóki agent maszyny Wirtualnej jest dostępne na maszynie wirtualnej, a także obsługę języka Python istnieje. Jednak te dystrybucji nie są obsługiwane.
 * **Windows Server**: wersje starsze niż Windows Server 2008 R2 nie są obsługiwane.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Ograniczenia w przypadku tworzenia kopii zapasowej i przywracanie maszyny Wirtualnej
Aby przygotować środowisko, należy zrozumieć następujące ograniczenia:

* Tworzenie kopii zapasowych maszyn wirtualnych z więcej niż 16 dysków danych nie jest obsługiwane.
* Tworzenie kopii zapasowych maszyn wirtualnych z zastrzeżonego adresu IP i nie zdefiniowanych punktów końcowych nie jest obsługiwane.
* Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux zaszyfrowany za pomocą szyfrowania Linux Unified klucz instalacji (LUKS) nie jest obsługiwana.
* Nie zaleca się tworzenie kopii zapasowych maszyn wirtualnych, które zawierają konfigurację udostępnionych woluminów klastra (CSV) lub serwera plików skalowalnego w poziomie. Wymagają one, obejmujące wszystkich maszyn wirtualnych uwzględnione w konfiguracji klastra podczas zadania migawki. Kopia zapasowa Azure nie obsługuje wielu maszyn wirtualnych. 
* Dane kopii zapasowej nie zawiera dyski sieciowe zainstalowane dołączony do maszyny Wirtualnej.
* Zamiana istniejącej maszyny wirtualnej podczas przywracania nie jest obsługiwana. Jeśli podjęto próbę przywrócenia maszyny Wirtualnej, gdy maszyna wirtualna istnieje, operacja przywracania kończy się niepowodzeniem.
* Region między tworzenie kopii zapasowej i przywracania nie są obsługiwane.
* Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych za pomocą niezarządzanych dysków na kontach magazynu przy użyciu reguł sieciowej zastosowane, nie jest obsługiwane dla klientów na starym stos kopii zapasowej maszyny Wirtualnej. 
* Podczas konfigurowania kopii zapasowej, upewnij się, że **zapory i sieci wirtualne** ustawienia konta magazynu jest dozwolony dostęp z wszystkich sieci.
* Można tworzyć kopie zapasowe maszyn wirtualnych we wszystkich regionach publicznej platformy Azure. (Zobacz [Lista kontrolna](https://azure.microsoft.com/regions/#services) obsługiwanych regionów.) Jeśli obecnie jest obsługiwany region, którego szukasz, nie zostanie wyświetlony na liście rozwijanej podczas tworzenia magazynu.
* Przywracanie kontrolera domeny (DC) maszyny Wirtualnej, która jest częścią konfiguracji kontrolera domeny na wielu jest obsługiwane tylko za pomocą programu PowerShell. Aby dowiedzieć się więcej, zobacz [Przywracanie kontrolera domeny, kontrolera domeny na wielu](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Przywracanie maszyn wirtualnych, które mają następujące konfiguracje sieciowe specjalne jest obsługiwane tylko za pomocą programu PowerShell. Maszyny wirtualne utworzone za pomocą przepływu pracy przywracania w interfejsie użytkownika nie będą miały te konfiguracje sieci po zakończeniu operacji przywracania. Aby dowiedzieć się więcej, zobacz [przywracanie maszyn wirtualnych z konfiguracjami sieci specjalne](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Maszyny wirtualne znajdujące się w konfiguracji usługi równoważenia obciążenia (wewnętrznych i zewnętrznych)
  * Maszyny wirtualne z wielu zastrzeżonych adresów IP
  * Maszyny wirtualne z wieloma kartami sieciowymi

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Tworzenie magazynu usług odzyskiwania dla maszyny Wirtualnej
Magazyn usług odzyskiwania jest jednostka, która przechowuje kopie zapasowe i punkty odzyskiwania, które zostały utworzone w czasie. Magazyn usług odzyskiwania zawiera również zasady tworzenia kopii zapasowej, które są skojarzone z chronionych maszyn wirtualnych.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na **Centrum** menu, wybierz opcję **Przeglądaj**, a następnie wpisz **usług odzyskiwania**. Po rozpoczęciu wpisywania, dane wejściowe filtruje listę zasobów. Wybierz **Magazyny usług odzyskiwania**.

    ![Wpisz w polu i wybierając polecenie "Magazyny usług odzyskiwania" w wynikach](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Zostanie wyświetlona lista magazynów Usług odzyskiwania.
3. Na **Magazyny usług odzyskiwania** menu, wybierz opcję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **Magazyny usług odzyskiwania** zostanie otwarte okienko. Monituje o podanie informacji o **nazwa**, **subskrypcji**, **grupy zasobów**, i **lokalizacji**.

    ![Okienko "Magazyny usług odzyskiwania"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Nazwa musi być unikalna w tej subskrypcji platformy Azure. Wpisz nazwę, która zawiera 2 do 50 znaków. Musi rozpoczynać się od litery i może zawierać tylko litery, cyfry i łączniki.
5. Wybierz **subskrypcji** aby zobaczyć listę dostępnych subskrypcji. Jeśli nie masz pewności, które subskrypcji do użycia, należy użyć wartości domyślnej (lub sugerowane) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy pracy lub konto służbowe jest skojarzony z wieloma subskrypcjami platformy Azure.
6. Wybierz **grupy zasobów** Aby wyświetlić listę dostępnych grup zasobów, lub wybierz **nowy** Aby utworzyć nową grupę zasobów. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Wybierz **lokalizacji** wybierz region geograficzny magazynu. Magazyn *musi* znajdować się w tym samym regionie, w którym znajdują się maszyny wirtualne, które mają być chronione.

   > [!IMPORTANT]
   > Jeśli masz pewności co do lokalizacji, w której istnieje maszyna wirtualna, zamknij okno dialogowe Tworzenie magazynu i przejdź do listy maszyn wirtualnych w portalu. Jeśli masz maszyny wirtualne w wielu regionach, musisz utworzyć magazyn usług odzyskiwania w każdym regionie. Przed przejściem do następnej lokalizacji utwórz magazyn w pierwszej lokalizacji. Jest niepotrzebna do określania kont magazynu do przechowywania danych kopii zapasowej. Magazyn usług odzyskiwania usługi Azure Backup obsługi i który automatycznie.
   >
   >

8. Wybierz pozycję **Utwórz**. Utworzenie magazynu Usług odzyskiwania może zająć trochę czasu. Monitoruje powiadomienia o stanie w prawym górnym obszarze portalu. Po utworzeniu magazynu zostanie wyświetlona lista magazynów usług odzyskiwania. Jeśli nie widzisz magazynu, wybierz **Odśwież**.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Magazyn został już utworzony, więc teraz zajmiemy się skonfigurowaniem jego replikacji.

## <a name="set-storage-replication"></a>Ustaw replikację magazynu
Dla opcji replikacji magazynu umożliwia wybór między magazynu geograficznie nadmiarowego i lokalnie nadmiarowego magazynu. Domyślnie magazyn jest nadmiarowy geograficznie. Pozostaw ustawienie opcji jako magazynu geograficznie nadmiarowego dla Twoja podstawowa kopia zapasowa. Jeśli chcesz tańsze opcja, która nie jest trwałej, wybierz magazyn lokalnie nadmiarowy.

Aby edytować ustawienia replikacji magazynu:

1. Na **Magazyny usług odzyskiwania** okienku, wybierz magazyn.
    Po wybraniu magazynu, **ustawienia** okienka (mający nazwę magazynu u góry) i otwórz okienko szczegółów magazynu.

   ![Wybierz z listy magazynów kopii zapasowych magazynu](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Na **ustawienia** okienka, przewiń w dół za pomocą suwaka pionowego **Zarządzaj** , a następnie wybierz opcję **infrastruktura kopii zapasowej**. W **ogólne** zaznacz **konfiguracji kopii zapasowej**. Na **konfiguracji kopii zapasowej** okienku, wybierz opcję replikacji magazynu dla magazynu. Domyślnie magazyn jest nadmiarowy geograficznie.

   ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, Kontynuuj korzystanie z magazynu geograficznie nadmiarowego. Jeśli używasz platformy Azure jako punktu końcowego magazynu kopii zapasowych innego niż podstawowy, wybierz magazyn lokalnie nadmiarowy. Przeczytaj więcej na temat opcji magazynu w [Omówienie replikacji usługi Azure Storage](../storage/common/storage-redundancy.md).

3. Jeśli zmienisz typ replikacji magazynu, wybierz **zapisać**.
    
Po wybraniu opcji magazynu dla magazynu możesz skojarzyć maszynę Wirtualną z magazynu. Aby rozpocząć kojarzenie, należy odnaleźć i zarejestrować maszyny wirtualne Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Wybierz cel kopii zapasowej, ustawienie zasad i zdefiniuj elementy do ochrony
Przed zarejestrowaniem maszyny wirtualnej z magazynu usług odzyskiwania, należy uruchomić proces wykrywania, aby zidentyfikować wszelkie nowe maszyny wirtualne, które są dodawane do subskrypcji. Proces odnajdywania kwerendy Azure o listę maszyn wirtualnych w ramach subskrypcji. W przypadku znalezienia nowych maszyn wirtualnych portalu Wyświetla nazwa usługi w chmurze i region skojarzone. W portalu Azure *scenariusza* wprowadzić w magazynie usług odzyskiwania. *Zasady* jest harmonogram kiedy i jak często są pobierane punktów odzyskiwania. Zasady zawierają także zakres przechowywania dla punktów odzyskiwania.

1. Jeśli Twój magazyn Usług odzyskiwania jest już otwarty, przejdź do kroku 2. Jeśli nie masz magazyn usług odzyskiwania, Otwórz, otwórz [portalu Azure](https://portal.azure.com/). Na **Centrum** menu, wybierz opcję **więcej usług**.

   a. Na liście zasobów wpisz **Usługi odzyskiwania**. Po rozpoczęciu wpisywania, dane wejściowe filtruje listę. Po wyświetleniu **Magazyny usług odzyskiwania**, zaznacz go.

      ![Wpisz w polu i wybierając polecenie "Magazyny usług odzyskiwania" w wynikach](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Zostanie wyświetlona lista magazynów Usług odzyskiwania. Jeśli w Twojej subskrypcji nie żadnego magazynu, ta lista jest pusta.

      ![Widok listy magazynów usługi Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Wybierz magazyn z listy magazynów Usług odzyskiwania.

      **Ustawienia** okienko i na pulpicie nawigacyjnym magazynu dla wybranego magazynu otwarte.

      ![Ustawienia okienka i magazynu pulpitu nawigacyjnego](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. W menu nawigacyjnym magazynu wybierz **kopii zapasowej**.

   ![Przycisk tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Kopii zapasowej** i **cel kopii zapasowej** Otwórz okienka.

3. Na **celu kopii zapasowej** ustawić okienku **gdzie działa Twoje obciążenie?** jako **Azure** i **co chcesz utworzyć kopię zapasową?** jako  **Maszyna wirtualna**. Następnie wybierz przycisk **OK**.

   ![Cel kopii zapasowej i kopii zapasowych okienka](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Ten krok rejestruje rozszerzenia maszyny Wirtualnej w magazynie. **Cel kopii zapasowej** okienko zostanie zamknięte, a **kopii zapasowej zasad** zostanie otwarte okienko.

   !["Kopia zapasowa" i "Kopia zapasowa zasad" okienka](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. Na **kopii zapasowej zasad** okienku, wybierz zasady tworzenia kopii zapasowej, który chcesz zastosować do magazynu.

   ![Wybór zasad tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Szczegóły domyślnych zasad znajdują się w menu rozwijanym. Aby utworzyć nowe zasady, wybierz opcję **Utwórz nowy** z menu rozwijanego. Instrukcje dotyczące definiowania zasad tworzenia kopii zapasowych można znaleźć w sekcji [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Wybierz **OK** do skojarzenia zasad tworzenia kopii zapasowej w magazynie.

   **Kopii zapasowej zasad** okienko zostanie zamknięte, a **wybierz maszyny wirtualne** zostanie otwarte okienko.
5. Na **wybierz maszyny wirtualne** okienku wybierz maszyny wirtualne do skojarzenia z określonych zasad, a następnie wybierz **OK**.

   !["Wybierz maszyny wirtualne" okienko](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Wybrana maszyna wirtualna jest weryfikowana. Jeśli nie widzisz oczekiwanego maszyny wirtualnej, sprawdź, czy maszyny wirtualne w tym samym regionie Azure, co magazyn usług odzyskiwania. Jeśli nadal nie widać maszyny wirtualnej, sprawdź, czy nie są już chronione w innym magazynie. Pulpitem nawigacyjnym magazynu zawiera region, gdzie istnieje magazyn usług odzyskiwania.

6. Teraz, gdy chcesz zdefiniować wszystkie ustawienia magazynu, w **kopii zapasowej** okienku wybierz **kopii zapasowej Włącz**. Ten krok wdraża zasady dla magazynu i maszyn wirtualnych. Ten krok nie powoduje utworzenia początkowego punktu odzyskiwania dla maszyny wirtualnej.

   ![Przycisk "Włącz kopii zapasowej"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po pomyślnym włączeniu kopii zapasowej zasad tworzenia kopii zapasowej zostanie uruchomiony zgodnie z harmonogramem. Jeśli chcesz wygenerować zadanie tworzenia kopii zapasowej na żądanie do kopii zapasowych maszyn wirtualnych, zobacz [wyzwalając zadanie tworzenia kopii zapasowej](./backup-azure-arm-vms.md#triggering-the-backup-job).

Jeśli masz problemy z zarejestrowaniem maszyny wirtualnej, zobacz poniższe informacje na temat instalowania agenta maszyny Wirtualnej oraz łączność sieciową. Prawdopodobnie nie potrzebujesz następujących informacji w przypadku ochrony maszyn wirtualnych utworzonych na platformie Azure. Jednak po migracji maszyn wirtualnych na platformie Azure, należy poprawnie zainstalowany agent maszyny Wirtualnej i maszyny wirtualnej mogą komunikować się z siecią wirtualną.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Zainstaluj agenta maszyny Wirtualnej na maszynie wirtualnej
Rozszerzenia kopii zapasowej do pracy Azure [agenta maszyny Wirtualnej](../virtual-machines/windows/agent-user-guide.md) musi być zainstalowany na maszynie wirtualnej Azure. Jeśli maszyna wirtualna została utworzona z portalu Azure Marketplace, agent maszyny Wirtualnej jest już obecny w maszynie wirtualnej. 

Poniżej przedstawiono informacje dotyczące sytuacji, w którym są *nie* przy użyciu maszyny Wirtualnej utworzone w witrynie Azure Marketplace. Na przykład w przypadku migracji maszyny Wirtualnej z lokalnego centrum danych. W takim przypadku agenta maszyny Wirtualnej musi być zainstalowany, aby chronić maszyny wirtualnej.

Jeśli masz problemy z tworzenia kopii zapasowej maszyny Wirtualnej Azure, skorzystaj z poniższej tabeli, aby sprawdzić, czy agent maszyny Wirtualnej platformy Azure jest poprawnie zainstalowane na maszynie wirtualnej. W tabeli znajdują się dodatkowe informacje na temat agenta maszyny Wirtualnej systemu Windows i maszyn wirtualnych systemu Linux.

| **Operacja** | **Windows** | **Linux** |
| --- | --- | --- |
| Zainstaluj agenta maszyny Wirtualnej |Pobierz i zainstaluj [plik MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Potrzebne są uprawnienia administratora, aby zakończyć instalację. |Zainstaluj najnowszą [agenta systemu Linux](../virtual-machines/linux/agent-user-guide.md). Potrzebne są uprawnienia administratora, aby zakończyć instalację. Zaleca się zainstalowanie agenta z repozytorium dystrybucji. Firma Microsoft *nie jest zalecane* Instalowanie agenta maszyny Wirtualnej systemu Linux bezpośrednio z usługi GitHub.  |
| Aktualizuj agenta maszyny Wirtualnej |Aktualizacja agenta maszyny Wirtualnej jest tak proste, jak zainstalować ponownie [plików binarnych agenta maszyny Wirtualnej](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Upewnij się, że żadna operacja tworzenia kopii zapasowej nie jest uruchomiona podczas aktualizowania agenta maszyny wirtualnej. |Postępuj zgodnie z instrukcjami dotyczącymi [aktualizacja agenta maszyny Wirtualnej systemu Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Firma Microsoft zaleca aktualizacji agenta z repozytorium dystrybucji. Firma Microsoft *nie jest zalecane* zaktualizować agenta maszyny Wirtualnej systemu Linux bezpośrednio z usługi GitHub.<br><br>Upewnij się, że żadna operacja tworzenia kopii zapasowej nie jest uruchomiona podczas aktualizowania agenta maszyny wirtualnej. |
| Sprawdź poprawność instalacji agenta maszyny Wirtualnej |1. Przejdź do folderu C:\WindowsAzure\Packages w maszynie Wirtualnej platformy Azure. <br><br>2. Znajdź plik WaAppAgent.exe. <br><br>3. Kliknij plik prawym przyciskiem myszy, przejdź do opcji **Właściwości**, a następnie wybierz kartę **Szczegóły**. **Wersji produktu** pole powinno być 2.6.1198.718 lub nowszej. |ND |

### <a name="backup-extension"></a>Rozszerzenie kopii zapasowej
Po zainstalowaniu agenta maszyny Wirtualnej na maszynie wirtualnej, usługa Azure Backup instaluje zapasowy numer wewnętrzny agenta maszyny Wirtualnej. Usługa Kopia zapasowa bezproblemowo uaktualnia i poprawek zapasowy numer wewnętrzny.

Usługi Kopia zapasowa instaluje zapasowy numer wewnętrzny, czy maszyna wirtualna jest uruchomiona. Uruchomiona maszyna wirtualna zapewnia największe prawdopodobieństwo uzyskania punktu odzyskiwania spójnego z aplikacją. Jednak usługa kopii zapasowej będzie utworzyć kopię zapasową maszyny Wirtualnej, nawet jeśli jest wyłączony i nie można zainstalować rozszerzenia. Jest to nazywane *maszyny Wirtualnej w trybie offline*. W takim przypadku punkt odzyskiwania będzie *awaryjnie spójny*.

## <a name="establish-network-connectivity"></a>Ustanowienie połączenia z siecią
Aby zarządzać migawek maszyny Wirtualnej, zapasowy numer wewnętrzny musi mieć łączność Azure publicznych adresów IP. Bez prawo łączność z Internetem limit czasu żądania HTTP maszyny wirtualnej i tworzenia kopii zapasowej nie powiedzie się. Jeśli wdrożenia obowiązują ograniczenia dostępu w — za pośrednictwem grupy zabezpieczeń sieci (NSG), na przykład wybrać jedną z te opcje, aby podać Wyczyść ścieżkę do tworzenia kopii zapasowej ruchu:

* [Lista dozwolonych adresów IP centrum danych Azure zakresów](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Wdrażanie serwera proxy HTTP dla routingu ruchu.

W przypadku podejmując opcji kompromisy należą do zakresu od możliwości zarządzania, kontrolę i kosztów.

| Opcja | Zalety | Wady |
| --- | --- | --- |
| Lista dozwolonych adresów IP, zakresów |Brak dodatkowych kosztów.<br><br>Do otwierania dostępu w grupy NSG, użyj **AzureNetworkSecurityRule zestaw** polecenia cmdlet. |Złożone, aby zarządzać jako dotkniętych zakresów IP ulec zmianie.<br><br>Zapewnia dostęp do całej platformy Azure i nie tylko magazynu. |
| Użyj serwera proxy HTTP |Kontrolę na serwerze proxy za pośrednictwem magazynu dozwolone adresy URL.<br><br>Pojedynczy punkt internet dostęp do maszyn wirtualnych.<br><br>Nie może ulec zmiany adresu IP platformy Azure. |Dodatkowe koszty uruchomioną maszynę Wirtualną z oprogramowaniem serwera proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Zakresy IP dozwolonych centrum danych Azure
Do listy dozwolonych zakresy IP centrum danych Azure, zobacz [witryny sieci Web Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) szczegółowe informacje na temat zakresów adresów IP i instrukcje.

Umożliwia połączenia z magazynem określonego regionu za pomocą [usługi tagi](../virtual-network/security-overview.md#service-tags). Upewnij się, że reguła, która umożliwia uzyskanie dostępu do konta magazynu ma wyższy priorytet niż regułę, która blokuje dostęp do Internetu. 

![Grupy NSG z tagami magazynu dla regionu](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Tagi usługi magazynu są dostępne tylko w określonych regionach i są w wersji zapoznawczej. Aby uzyskać listę regionów, zobacz [usługi tagi dla magazynu](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Użyj serwera proxy HTTP dla kopii zapasowych maszyn wirtualnych
Podczas tworzenia kopii zapasowej maszyny Wirtualnej, tworzenia kopii zapasowej rozszerzenia na maszynie Wirtualnej polecenia są wysyłane migawki zarządzania do magazynu Azure przy użyciu interfejsu API protokołu HTTPS. Kierować ruchem zapasowy numer wewnętrzny, za pośrednictwem serwera proxy HTTP, ponieważ jest on tylko składnik skonfigurowany do uzyskiwania dostępu do publicznej sieci internet.

> [!NOTE]
> Nie zaleca się oprogramowania określonego serwera proxy, która powinna być używana. Upewnij się, wybierz serwer proxy, który jest zgodny z czynności konfiguracyjne poniżej.
>
>

Na poniższej ilustracji przykładzie przedstawiono kroki trzech konfiguracji koniecznych do używania serwera proxy HTTP:

* Trasy wirtualna aplikacji cały ruch HTTP powiązany publicznego Internetu za pośrednictwem serwera proxy maszyny Wirtualnej.
* Maszyna wirtualna serwera proxy zezwala na ruch przychodzący z maszyn wirtualnych w sieci wirtualnej.
* Sieciową grupę zabezpieczeń o nazwie NF blokady musi regułę zabezpieczeń, która umożliwia wychodzący ruch internetowy pochodzący z serwera proxy maszyny Wirtualnej.

Aby użyć serwera proxy HTTP do komunikacji z publicznego Internetu, wykonaj następujące kroki.

> [!NOTE]
> Kroki przy użyciu określonej nazwy i wartości w tym przykładzie. W przypadku podczas wprowadzania (lub wklejanie) szczegółów do kodu, użyj nazwy i wartości dla danego wdrożenia.

#### <a name="step-1-configure-outgoing-network-connections"></a>Krok 1: Konfigurowanie wychodzących połączeń sieciowych
###### <a name="for-windows-machines"></a>W przypadku komputerów z systemem Windows
Ta procedura powoduje ustawienie konfiguracji serwera proxy dla konta system lokalny.

1. Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Otwórz program Internet Explorer, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. W programie Internet Explorer przejdź do **narzędzia** > **Opcje internetowe** > **połączeń** > **ustawienia sieci LAN**.
4. Sprawdź ustawienia serwera proxy dla konta system. Ustaw proxy IP i portu.
5. Zamknij program Internet Explorer.

Poniższy skrypt ustawia konfigurację serwera proxy dla komputera i używa go do dowolnego ruch wychodzący protokołu HTTP lub HTTPS. Jeśli skonfigurowano serwer proxy dla bieżącego konta użytkownika (a nie konto system lokalny), należy użyć tego skryptu do dotyczą SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Jeśli zauważysz "(407) uwierzytelniania serwera Proxy wymagane" w dzienniku serwera proxy, sprawdź, czy uwierzytelnianie jest prawidłowo skonfigurowane.
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
1. Na serwerze proxy otwórz Zaporę systemu Windows. Najprostszym sposobem dostęp zapory jest do wyszukania **Zapora systemu Windows z zabezpieczeniami zaawansowanymi**.
2. W **Zapora systemu Windows z zabezpieczeniami zaawansowanymi** okno dialogowe, kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego** i wybierz **nową regułę**.
3. W Kreatora nowej reguły przychodzącej na **typ reguły** wybierz pozycję **niestandardowy** opcję i zaznacz **dalej**.
4. Na **Program** wybierz pozycję **wszystkie programy** i wybierz **dalej**.
5. Na **protokoły i porty** , wprowadź następujące informacje i wybrać opcję **dalej**:
   * Aby uzyskać **protokół typu**, wybierz pozycję **TCP**.
   * Aby uzyskać **portów lokalnych**, wybierz pozycję **określonych portów**. W polu poniżej Określ numer portu serwera proxy, który został skonfigurowany.
   * Aby uzyskać **port zdalny**, wybierz pozycję **wszystkie porty**.

Pozostałe kreatorze zaakceptuj ustawienia domyślne, aż do zakończenia. Następnie nazwę tej reguły. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Krok 3: Dodaj regułę wyjątku do grupy NSG
Polecenie dodaje wyjątek do grupy NSG. Ten wyjątek zezwala na ruch TCP z dowolnego portu na 10.0.0.5 do dowolnego adresu internetowego na porcie 80 (HTTP) lub 443 (HTTPS). Jeśli potrzebujesz określonego portu w publicznej sieci internet, należy dodać tego portu do ```-DestinationPortRange```.

W wierszu polecenia programu PowerShell systemu Azure wprowadź następujące polecenie:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub w przypadku dowolnej funkcji, które mają być wyświetlane uwzględnione, [Prześlij nam opinię](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy przygotowania środowiska do tworzenia kopii zapasowej maszyny Wirtualnej, następnym krokiem logicznej jest utworzenie kopii zapasowej. Planowania artykuł zawiera bardziej szczegółowe informacje o tworzeniu kopii zapasowych maszyn wirtualnych.

* [Tworzenie kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md)
* [Zaplanuj infrastrukturę kopii zapasowej maszyny Wirtualnej](backup-azure-vms-introduction.md)
* [Zarządzanie kopii zapasowych maszyn wirtualnych](backup-azure-manage-vms.md)
