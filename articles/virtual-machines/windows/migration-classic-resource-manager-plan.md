---
title: Planowanie migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Planowanie migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 5e2790515e172ec14e2180f9dfcac6c97b2e135a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723176"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planowanie migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager
Gdy usługi Azure Resource Manager oferuje wiele wspaniałych nowych funkcji, ważne jest zaplanowanie podróż migracyjną Postaramy się bezproblemowym przejściu. Poświęcania czasu na temat planowania będą upewnij się, czy nie występują problemy podczas wykonywania działania migracji.

> [!NOTE]
> Poniższe wskazówki został intensywnie przyczyniły się do zespołu doradczego klientów platformy Azure i architektami rozwiązania w chmurze pracuje z klientami Migrowanie dużych środowiskach. Jako takie tego dokumentu, będą w dalszym ciągu zostaje zaktualizowana, jako że nowych wzorców sukces, więc Sprawdź od czasu czas, aby zobaczyć, czy istnieją żadnych nowych zaleceń.

Istnieją cztery fazy ogólne podróży migracji:<br>

![Etapy migracji](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planowanie

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i wady

W zależności od wymagań technicznych, rozmiar, lokalizacje geograficzne i praktyki operacyjne warto wziąć pod uwagę:

1. Dlaczego usługi Azure Resource Manager jest pożądane w swojej organizacji?  Co to są powodów biznesowych do migracji?
2. Co to są przyczyn technicznych dla usługi Azure Resource Manager?  Jakie (jeśli istnieje) czy chcesz korzystać z dodatkowych usług systemu Azure?
3. Której aplikacji (lub zestawów maszyn wirtualnych) znajduje się w migracji?
4. Scenariuszy, do których są obsługiwane za pomocą interfejsu API migracji?  Przegląd [nieobsługiwane funkcje oraz konfiguracje](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Twoje zespoły operacyjne obsługuje obecnie aplikacji lub maszyny wirtualne zarówno klasyczny, jak i usługi Azure Resource Manager?
6. Jak (Jeśli w ogóle) usługi Azure Resource Manager zmienia się wdrożenie maszyny Wirtualnej, zarządzania, monitorowania i raportowania procesów?  Skrypty wdrażania muszą zostać zaktualizowane?
7. Co to jest komunikacji planuje uczestników (użytkownicy końcowi, właścicieli aplikacji i właścicieli infrastruktury)?
8. W zależności od złożoności środowiska powinien istnieć okres konserwacji gdzie aplikacja jest niedostępna, aby użytkownicy końcowi i właścicieli aplikacji?  Jeśli tak, przez jak długi czas?
9. Co to jest plan szkolenia, aby upewnić się, że zainteresowane strony wiedzą i biegły w usłudze Azure Resource Manager?
10. Co to jest zarządzanie programem lub planu zarządzania projektu migracji?
11. Co to są osie czasu dla migracji usługi Azure Resource Manager i inne powiązane map drogowych technologii?  Są one optymalnie wyrównana?

### <a name="patterns-of-success"></a>Wzorce sukcesu

Klienci pomyślnie szczegółowych planów, gdzie poprzedniego pytania są omówione udokumentowane i podlegają.  Upewnij się, że plany migracji szeroko są przekazywane do sponsorów i uczestnikom projektu.  Pożyczkowe samodzielnie dzięki wiedzy na temat opcji migracji; Zdecydowanie zaleca się odczytywanie za pośrednictwem tego dokumentu migracji poniżej.

* [Omówienie migracji obsługiwanej przez platformę zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia umożliwia migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społeczności do ułatwiających migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj często zadawane pytania dotyczące migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

- Nie można zaplanować.  Kroki technologii migracji są sprawdzone, a wynik jest przewidywalne.
- Założenie, że platforma obsługiwane interfejsu API migracji usługi będzie uwzględniać we wszystkich scenariuszach. Odczyt [nieobsługiwane funkcje oraz konfiguracje](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) Aby zrozumieć, jakie scenariusze są obsługiwane.
- Nie planuje potencjalnych awarii aplikacji dla użytkowników końcowych.  Należy zaplanować wystarczająco dużego buforu odpowiednio ostrzec użytkowników końcowych czasu aplikacji potencjalnie niedostępny.


## <a name="lab-test"></a>Wyniki testów

**Replikowanie środowiska i przeprowadzić migrację testu**
  > [!NOTE]
  > Dokładne replikacji istniejącego środowiska jest wykonywane przy użyciu narzędzia przez społeczność, co nie jest oficjalnie obsługiwane przez Microsoft Support. Dlatego jest **opcjonalne** krok, ale to najlepszy sposób, aby dowiedzieć się, problemów bez dotykania Twoje środowisko produkcyjne. Jeśli przy użyciu narzędzia przez społeczność nie jest opcją, przeczytaj temat poniższe zalecenia przebiegu weryfikacji/przygotowanie/przerwania.
  >

  Przeprowadzenie testu laboratoryjnego do danego scenariusza (obliczeniowych, sieci i magazynu) to najlepszy sposób, aby zapewnić płynną migrację. Ułatwi to zapewnienie:

- Laboratorium całkowicie oddzielnych lub istniejących poza środowiskiem produkcyjnym do testowania. Zaleca się całkowicie oddzielne laboratorium, które można poddać migracji wielokrotnie i może być modyfikowany jest generowany.  Poniżej przedstawiono skryptów w celu zebrania/wodzianem metadanych z rzeczywistych subskrypcji.
- To dobry pomysł, aby utworzyć laboratorium w oddzielnej subskrypcji. Przyczyną jest laboratorium będzie zostać rozłączona wielokrotnie i posiadanie oddzielnego, izolowane subskrypcji zmniejsza to ryzyko, że coś rzeczywiste będzie uzyskać przypadkowo usunięty.

  Można to zrobić za pomocą narzędzia AsmMetadataParser. [Dowiedz się więcej o tym narzędziu, w tym miejscu](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Wzorce sukcesu

Poniżej zostały problemów znalezionych w wielu większych migracji. To nie jest kompletną listą i powinni zapoznać się z [nieobsługiwane funkcje oraz konfiguracje](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) Aby uzyskać więcej szczegółów.  Może lub nie mogą wystąpić następujące problemy techniczne, ale po wykonaniu rozwiązywania tych przed próbą wykonania migracji zapewnia płynne.

- **Czy uruchomienia próbnego Prepare/Validate/przerwania** — jest to prawdopodobnie wykonuję Najważniejszy krok, aby upewnić się klasyczne, tak aby wskazywał Powodzenie migracji usługi Azure Resource Manager. Migracja interfejsu API ma trzy podstawowe kroki: Sprawdź poprawność, przygotowanie i zatwierdzania. Sprawdzanie poprawności będzie odczyt stanu klasycznego środowiska i zwracają wynik o wszystkich problemów. Jednak ponieważ niektóre problemy, może istnieć w stosie usługi Azure Resource Manager, sprawdzania poprawności nie będzie przechwytywać wszystko. Następnym krokiem w procesie migracji, przygotowanie ułatwia udostępnianie tych problemów. Przygotowanie będzie przenoszenie metadanych z wersji klasycznej do usługi Azure Resource Manager, ale będzie nie zatwierdzić przenoszenia i będzie nie Usuń lub Zmień elementy na stronie klasycznego. Uruchomienia próbnego obejmuje przygotowanie migracji, a następnie zostanie przerwany (**nie zatwierdzanie**) przygotowanie migracji. Celem przebiegu weryfikacji/przygotowanie/przerwania jest zobaczyć wszystkie metadane w stosie usługi Azure Resource Manager, należy go sprawdzić (*programowo, albo w portalu*), sprawdź, czy wszystko jest migrowana poprawnie i trzymać się kolejności technicznych problemy.  Jej będzie również dać Ci przedsmak czasu trwania migracji, należy odpowiednio zaplanować przestojów.  Sprawdź poprawność/przygotowanie/przerwania nie powoduje żadnych przestojów użytkownika; Dlatego jest przeznaczone do użycia aplikacji.
  - Poniższe elementy będzie musiał zostać rozwiązane przed uruchomienia próbnego, ale te kroki przygotowania przebiegu testu zostanie również bezpiecznie opróżnienia, jeśli zostaną one pominięte. Podczas migracji enterprise Odkryliśmy uruchomienia próbnego za bezpieczne i bezcenne sposobem zapewnienia gotowości do migracji.
  - Kiedy przygotowania działa formant płaszczyzny (operacje zarządzania platformy Azure) zostanie zablokowane dla całej sieci wirtualnej, a więc nie zmian do metadanych maszyny Wirtualnej podczas sprawdzania poprawności/przygotowanie/przerwania.  Ale w przeciwnym razie dowolnej funkcji aplikacji (usług pulpitu zdalnego, maszyna wirtualna użycia, itp.) będzie to miało wpływu.  Użytkownicy maszyn wirtualnych nie będzie wiadomo, jest wykonywane uruchomienia próbnego.

- **Express Route obwodów i sieci VPN**. Obecnie Express Route bram, wraz z łączami autoryzacji nie można migrować bez przestojów. Aby uniknąć tego problemu, zobacz [migracji usługi ExpressRoute circuits i skojarzonych sieci wirtualnych z klasycznego modelu wdrażania usługi Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozszerzenia maszyn wirtualnych** — rozszerzenia maszyny wirtualnej są potencjalnie jedną z największych problemów współczesnej Migrowanie działających maszyn wirtualnych. Korygowanie rozszerzeń maszyny Wirtualnej może potrwać zgłaszane 1 – 2 dni, więc odpowiednio zaplanować.  Pozwala raportować stanu rozszerzenia maszyny Wirtualnej uruchomionych maszyn wirtualnych na potrzeby działającego agenta platformy Azure. Jeśli stan wróci jako uszkodzony dla uruchomionej maszyny Wirtualnej, to zostanie zatrzymany migracji. Sam agent musi być w stanie, aby umożliwić migrację, ale jeśli istnieje rozszerzenia na maszynie Wirtualnej, następnie będą potrzebne zarówno pracy agenta i ruchu wychodzącego łączności z Internetem (z systemem DNS) do migracji przejść dalej.
  - Jeśli połączenie z serwerem DNS zostaną utracone podczas migracji, wszystkie rozszerzenia maszyny Wirtualnej, z wyjątkiem BGInfo w wersji 1. \* należy najpierw usunąć z każdego maszyny Wirtualnej przed przygotowanie migracji, a następnie ponownie dodać z powrotem do maszyny Wirtualnej po migracji usługi Azure Resource Manager.  **Te informacje dotyczą tylko maszyn wirtualnych, które są uruchomione.**  Jeśli maszyny wirtualne zostaną zatrzymane przydział zostanie cofnięty, rozszerzenia maszyn wirtualnych nie są do usunięcia.

  > [!NOTE]
  > Wiele rozszerzeń, takich jak narzędzie diagnostyczne systemu Azure i zabezpieczeń Centrum monitorowania zostaną ponownie zainstalować samodzielnie po migracji, dlatego usunięcie ich nie jest problemem.

  - Ponadto upewnij się, sieciowe grupy zabezpieczeń są ograniczając ruch wychodzący do Internetu. Może to nastąpić w niektórych konfiguracjach sieciowych grup zabezpieczeń. Ruch wychodzący do Internetu (i DNS) jest wymagany dla rozszerzeń maszyn wirtualnych do migracji do usługi Azure Resource Manager.
  - Dwie wersje rozszerzenie BGInfo istnieją i są wywoływane w wersji 1 i 2.  

      - Jeśli maszyna wirtualna używa rozszerzenie BGInfo w wersji 1, można pozostawić to rozszerzenie, ponieważ jest. Migracja interfejsu API pomija tego rozszerzenia. Po zakończeniu migracji można dodać rozszerzenie BGInfo.
      - Jeśli maszyna wirtualna używa rozszerzenia BGInfo opartych na formacie JSON w wersji 2, maszyna wirtualna została utworzona przy użyciu witryny Azure portal. Migracja interfejsu API obejmuje to rozszerzenie podczas migracji do usługi Azure Resource Manager, pod warunkiem że agent działa i ma ruch wychodzący do Internetu (i DNS).

  - **Opcja korygowania 1**. Jeśli wiesz, maszyny wirtualne nie będą mogli korzystać wychodzącego dostępem do Internetu, usługi DNS pracy i pracy agentów programu Azure na maszynach wirtualnych, następnie odinstaluj wszystkie rozszerzenia maszyny Wirtualnej w ramach migracji, zanim przygotowania, ponownie zainstalować rozszerzenia maszyny Wirtualnej, po zakończeniu migracji.
  - **Opcja korygowania 2**. W przypadku zbyt duży dla próg wykluczający rozszerzeń maszyn wirtualnych innym rozwiązaniem jest cofnięcie przydziału/zamykania wszystkich maszyn wirtualnych przed migracją. Migrowanie przydział zostanie cofnięty maszyn wirtualnych, a następnie uruchom je ponownie po stronie usługi Azure Resource Manager. W tym miejscu ma zmigruje rozszerzeń maszyn wirtualnych. Minusem jest to, że wszystkie publiczne połączonego z wirtualnych adresów IP zostaną utracone (może to być inne niż początkowy,) i oczywiście maszyn wirtualnych zostanie zamknięty, powodując znacznie większego wpływu na pracę aplikacji.

    > [!NOTE]
    > Skonfigurowanie zasad usługi Azure Security Center przed działających maszyn wirtualnych, migrowanie zasad zabezpieczeń, które musi zostać zatrzymane przed usunięciem rozszerzenia, w przeciwnym razie zabezpieczeń rozszerzenie monitorowania zostaną automatycznie zainstalowane ponownie na maszynie Wirtualnej po jej usunięciu.

- **Zestawy dostępności** — w przypadku sieci wirtualnej (vNet) powinny być migrowane do usługi Azure Resource Manager, klasycznego wdrożenia (czyli usługi w chmurze) zawartych maszyny wirtualne muszą być w jednym zestawie dostępności lub maszyn wirtualnych wszystkie nie może występować w dowolnym zestawie dostępności. Posiadanie więcej niż jeden zestaw dostępności w usłudze w chmurze nie jest zgodny z usługą Azure Resource Manager i zostanie zatrzymany migracji.  Ponadto nie może być niektórych maszyn wirtualnych w zestawie dostępności, a niektóre maszyny wirtualne nie znajduje się w zestawie dostępności. Aby rozwiązać ten problem, należy skorygować lub zamieniać usługi w chmurze.  Planowanie odpowiednio to może zająć dużo czasu.

- **Wdrażanie roli sieć Web/proces roboczy** -zawierający role sieć web i proces roboczy usług w chmurze nie można migrować do usługi Azure Resource Manager. Role sieć web/proces roboczy. najpierw należy usunąć z siecią wirtualną, przed rozpoczęciem migracji.  Typowe rozwiązanie jest po prostu przesuń wystąpienia roli sieć web/proces roboczy do oddzielnych klasycznej sieci wirtualnej połączonej z obwodem usługi ExpressRoute lub migrację kodu do nowszych usług aplikacji PaaS (tej dyskusji wykracza poza zakres tego dokumentu). W poprzedniej wersji portalu Wdróż ponownie przypadek, Utwórz nową sieć wirtualną w klasycznym, Przenieś/ponowne wdrażanie ról sieć web/proces roboczy do tej nowej sieci wirtualnej, a następnie usunięcie jego wdrożeń z sieci wirtualnej jest przenoszony. Nie wymaganych zmian w kodzie. Nowy [komunikacji równorzędnej sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md) możliwość może służyć do komunikacji równorzędnej ze sobą klasycznej sieci wirtualnej, zawierający role sieć web/proces roboczy i innymi sieciami wirtualnymi w tym samym regionie platformy Azure, takich jak sieci wirtualnej, są migrowane (**po zakończeniu migracji sieci wirtualnej, ponieważ wirtualnych sieciach równorzędnych nie mogą być migrowane**), dlatego dostarczanie tych samych możliwości bez utraty wydajności oraz kar nie opóźnienia/przepustowości. Biorąc pod uwagę dodanie [komunikacji równorzędnej sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md), teraz można łatwo zminimalizować wdrożenia ról sieć web/proces roboczy i blokuje migracji do usługi Azure Resource Manager.

- **Limity przydziału Menedżera zasobów platformy Azure** — regiony platformy Azure mają oddzielne limity przydziału/limity dla wdrożeń klasycznych, jak i usługi Azure Resource Manager. Mimo że w scenariuszu migracji nowy sprzęt nie jest używane *(Firma Microsoft jest zamienianie istniejących maszyn wirtualnych z wersji klasycznej do usługi Azure Resource Manager)* , limity przydziału Menedżera zasobów platformy Azure nadal muszą być w miejscu z wystarczającą pojemnością przed można uruchomić migracji. Poniżej wymieniono główne ograniczenia, które okazały spowodować problemy.  Otwórz bilet pomocy technicznej limitu przydziału, aby zwiększyć limity.

    > [!NOTE]
    > Te limity muszą być wywoływany w tym samym regionie, co Twoje bieżące środowisko do migracji.
    >

  - Interfejsy sieciowe
  - Moduły równoważenia obciążenia
  - Publiczne adresy IP
  - Statyczne publiczne adresy IP
  - Rdzenie
  - Grupy zabezpieczeń sieci
  - Tabele tras

    Możesz sprawdzić bieżące limity przydziału usługi Azure Resource Manager przy użyciu następujących poleceń do najnowszej wersji programu Azure PowerShell.
    
    [!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

    **Obliczenia** *(rdzenie, zestawy dostępności)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Sieć** *(sieci wirtualne, statyczne publiczne adresy IP, publiczne adresy IP, sieciowe grupy zabezpieczeń, sieciowego tabele tras interfejsów, moduły równoważenia obciążenia)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Magazyn** *(konta magazynu)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Menedżer zasobów interfejsu API usługi Azure limity ograniczania przepływności** — Jeśli masz wystarczająco duże środowisko (np.) > 400 maszyn wirtualnych w sieci Wirtualnej) może napotkać API domyślne ograniczenie do zapisu (obecnie `1200 writes/hour`) w usłudze Azure Resource Manager. Przed rozpoczęciem migracji, należy zgłosić bilet pomocy technicznej w celu zwiększenia tego limitu dla Twojej subskrypcji.


- **Upłynął limit czasu się stan maszyny Wirtualnej aprowizacji** — w przypadku każdej maszyny Wirtualnej jest w stanie `provisioning timed out`, musi to być rozwiązane przed migracją. Jedynym sposobem, w tym celu jest bez przestojów przez anulowanie aprowizacji/reprovisioning maszyny Wirtualnej (delete, przechowywanie i ponowne utworzenie maszyny Wirtualnej).

- **Stan maszyny Wirtualnej RoleStateUnknown** — w przypadku migracji zatrzymuje ze względu na `role state unknown` komunikat o błędzie, sprawdź maszynę Wirtualną przy użyciu portalu i upewnij się, jest on uruchomiony. Ten błąd zazwyczaj znikną jej właścicielem (nie wymagana korekta) po kilku minutach i jest często przejściowy typu często występujące podczas maszynę wirtualną `start`, `stop`, `restart` operacji. **Zalecana praktyka:** ponów próbę migracji ponownie za kilka minut.

- **Nie istnieje klastra Service Fabric** — w niektórych przypadkach określone, nie można migrować maszyny wirtualne z różnych powodów nieparzysta. Jednym z tych przypadków znane jest Jeśli maszyna wirtualna została niedawno utworzony (w ciągu ostatniego tygodnia lub tak) i się stało z zawarciem klastrze platformy Azure, która nie jest jeszcze wyposażony w przypadku obciążeń usługi Azure Resource Manager.  Otrzymasz komunikat o błędzie informujący `fabric cluster does not exist` i nie można dokonać migracji maszyny Wirtualnej. Oczekiwanie na kilka dni będą zwykle są rozwiązywane tego konkretnego problemu jako klaster wkrótce otrzyma włączone usługi Azure Resource Manager. Jednak jeden natychmiastowego obejście polega na `stop-deallocate` tę maszynę Wirtualną, do przodu kontynuować migrację i uruchom maszynę Wirtualną w kopii zapasowej w usłudze Azure Resource Manager po zakończeniu migracji.

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

- Nie używaj skrótów i pominąć migracje przebiegu weryfikacji/przygotowanie/przerwania.
- Większość, jeśli nie, Twoja potencjalnych problemów ujawni podczas wykonywania czynności sprawdzania poprawności/przygotowanie/przerwania.

## <a name="migration"></a>Migracja

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i wady

Teraz wszystko jest gotowe, ponieważ pracowali przy użyciu znanych problemów ze środowiskiem.

Dla rzeczywistych migracji warto wziąć pod uwagę:

1. Planowanie i Planowanie sieci wirtualnej (najmniejsza jednostka migracji) z zwiększanie priorytetu.  Najpierw wykonaj proste sieci wirtualnych i postępu przy użyciu bardziej skomplikowanych sieci wirtualnych.
2. Większość klientów będzie spoza środowiska produkcyjnego i wdrożeń produkcyjnych.  Ostatnie planowania produkcji.
3. **(OPCJONALNIE)**  Zaplanować przestój w ramach konserwacji z dużą ilością buforu, w przypadku nieoczekiwanych problemów.
4. Komunikować się z i wyrównane z zespołów pomocy technicznej, w przypadku problemów.

### <a name="patterns-of-success"></a>Wzorce sukcesu

Wskazówki techniczne z _laboratorium testowego_ sekcji powinny być traktowane jako i zminimalizować przed rzeczywistą migrację.  Dzięki odpowiednie testy migracji jest faktycznie niepowiązane ze zdarzeniami.  W środowiskach produkcyjnych pomocne może być zapewnienie dodatkowego wsparcia, takich jak zaufanemu partnerowi firmy Microsoft lub usług firmy Microsoft w wersji Premier.

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

Nie są w pełni testowanie może spowodować problemy i opóźnienia w procesie migracji.  

## <a name="beyond-migration"></a>Po migracji

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i wady

Teraz, że jesteś w usłudze Azure Resource Manager, zmaksymalizuj platformy.  Odczyt [przegląd możliwości usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Aby dowiedzieć się o dodatkowe korzyści.

Co należy wziąć pod uwagę:

- Tworzenie pakietów migracji z innymi działaniami.  Większość klientów zdecydować się na okno obsługi aplikacji.  Jeśli tak, można włączyć inne możliwości usługi Azure Resource Manager, takie jak szyfrowanie i migrację do usługi Managed Disks za pomocą tej awarii.
- Ponowne przeanalizowanie przyczyny techniczne i biznesowe dla usługi Azure Resource Manager; Włącz dodatkowe usługi dostępne tylko na usłudze Resource Manager, które mają zastosowanie do danego środowiska.
- Modernizuj środowiska za pomocą usługi PaaS.

### <a name="patterns-of-success"></a>Wzorce sukcesu

Być znaczącej usług, które chcesz włączyć usługi Azure Resource Manager.  Wielu klientów uważa poniżej atrakcyjne dla swoich środowisk platformy Azure:

- [Kontrola dostępu oparta na rolach](../../role-based-access-control/overview.md).
- [Szablony usługi Azure Resource Manager dla wdrożenia łatwiejsze i bardziej kontrolowanego](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Tagi](../../azure-resource-manager/resource-group-using-tags.md).
- [Działania sterowania](../../azure-resource-manager/resource-group-audit.md)
- [Zasady usługi Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

Należy pamiętać o tym, dlaczego pracę tego modelu klasycznego do usługi podróży migracji do usługi Azure Resource Manager.  Jakie były oryginalnego powody biznesowe? Czy osiągasz uzasadnienie biznesowe?


## <a name="next-steps"></a>Kolejne kroki

* [Omówienie migracji obsługiwanej przez platformę zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfejs wiersza polecenia umożliwia migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Brama sieci VPN migracja z klasycznego do usługi Resource Manager](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrowanie obwodów ExpressRoute i skojarzonych sieci wirtualnych z klasycznego modelu wdrażania usługi Resource Manager](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Narzędzia społeczności do ułatwiających migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj często zadawane pytania dotyczące migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
