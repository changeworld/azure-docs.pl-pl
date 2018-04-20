---
title: Planowanie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Planowanie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 586a5590c88ef4124543c47389f62eaa864d2d18
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planowanie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager
Gdy usługi Azure Resource Manager oferuje wiele funkcji niesamowite, bardzo ważne jest zaplanować podróży migracji z prośbą sure bezproblemowe. Poświęcany czas na planowanie sprawdzi, czy nie występują problemy podczas wykonywania działań migracji. 

> [!NOTE] 
> Poniższe wskazówki został silnie przyczyniły się do powstania przez zespół doradczy klientów platformy Azure i Praca z klientami na migrowanie enviornments dużych architektów rozwiązania w chmurze. Jako takie tego dokumentu będą w dalszym ciągu aktualizowany jako wyłonić nowe wzorce sukcesu, dlatego Sprawdź od czasu okresu, aby sprawdzić, czy wszystkie nowe zalecenia dotyczące.

Istnieją cztery fazy ogólny przebieg migracji:

![Etapy migracji](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planowanie

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i wady i zalety

W zależności od Twojego rozmiar wymagania techniczne, lokalizacji geograficznych i rozwiązań operacyjnych warto wziąć pod uwagę:

1. Dlaczego wymagane jest Menedżera zasobów Azure dla organizacji?  Co to są powodów biznesowych do migracji?
2. Jakie są przyczyny techniczne dla usługi Azure Resource Manager?  Jakie (jeżeli istniał) czy chcesz skorzystać z dodatkowymi usługami platformy Azure?
3. Które aplikacji (lub zestawy maszyn wirtualnych) znajduje się w procesie migracji?
4. Jakie scenariusze są obsługiwane przy migracji interfejsu API?  Przegląd [nieobsługiwane funkcje i konfiguracje](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Zespołów operacyjnych obsługuje obecnie aplikacji/maszyn wirtualnych w klasycznym i usługi Azure Resource Manager?
6. Jak (Jeśli w ogóle) usługi Azure Resource Manager zmienia wdrożenia maszyn wirtualnych, zarządzania, monitorowania i raportowania procesów?  Skrypty wdrażania należy zaktualizować?
7. Co to jest komunikatów planowanie powiadamiania uczestników (użytkownicy końcowi, właścicieli aplikacji i właścicieli infrastruktury)?
8. W zależności od stopnia złożoności środowiska powinno być okres konserwacji gdy aplikacja jest niedostępne dla użytkowników końcowych i właścicielom aplikacji?  Jeśli tak, jak długo?
9. Co to jest plan szkolenia do upewnij się, że uczestników wiedzę i wykorzystać w usłudze Azure Resource Manager?
10. Co to jest program management lub plan zarządzania projektu do migracji?
11. Co to są termin migracji usługi Azure Resource Manager i inne powiązane mapy drogowej technologii?  Są one optymalnie wyrównana?

### <a name="patterns-of-success"></a>Wzorce Powodzenie

Klienci pomyślnie szczegółowych planów, gdzie te pytania są omawiane, udokumentowane i postanowieniom.  Upewnij się, że migracja planów szeroko są przekazywane do sponsorzy i uczestników.  Wyposażyć sobie z wiedzą na temat opcji migracji; Zdecydowanie zalecane jest odczytu za pomocą tego dokumentu migracji poniżej.

* [Omówienie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager obsługiwane platformy](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager przy użyciu programu PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Narzędzia społeczności z migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migrowania zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Problemów, aby uniknąć

- Nie można zaplanować.  Kroki technologii tej migracji zostały sprawdzone i wynikiem jest przewidywalne.
- Założenie, że platforma obsługiwane API migracji konta będzie we wszystkich scenariuszach. Odczyt [nieobsługiwane funkcje i konfiguracje](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) zrozumieć, jakie scenariusze są obsługiwane.
- Nie ma potencjalnych awarii aplikacji dla użytkowników końcowych.  Zaplanuj wystarczająco dużego buforu odpowiednio ostrzec użytkowników końcowych czasu aplikacji potencjalnie niedostępny.


## <a name="lab-test"></a>Laboratorium testowego 

**Replikowanie enviornment Twojego i przeprowadzić migrację testu**
  > [!NOTE]
  > Dokładne replikacji istniejącego środowiska jest wykonywana przy użyciu narzędzia przyczyniły się społeczności, który nie jest oficjalnie obsługiwana przez Microsoft Support. W związku z tym jest **opcjonalne** krok, ale to najlepszy sposób, aby dowiedzieć się problemów bez używania środowiska produkcyjnego. Jeśli przy użyciu narzędzia przyczyniły się społeczności nie jest opcją, przeczytaj o poniższe zalecenia przebiegu weryfikacji/Prepare/przerwania.
  >
  
  Przeprowadzenie testu laboratorium dokładne scenariusz (obliczeniowych, sieci i magazynu) jest najlepszym sposobem zapewnienia płynną migrację. Zapewni:

  - Całkowicie oddzielne laboratorium lub istniejącego środowiska produkcyjnego bez do testowania. Zaleca się całkowicie oddzielne laboratorium, które można poddać migracji wielokrotnie i może zostać zmodyfikowany utraty danych.  Skrypty w celu zebrania/wodzianem metadanych subskrypcje, do rzeczywistych są wymienione poniżej.
  - Zaleca się tworzenie laboratorium w oddzielnej subskrypcji. Przyczyną jest laboratorium będzie się działo wielokrotnie, czy posiadanie oddzielnego, izolowanego subskrypcji zmniejsza to ryzyko, że coś rzeczywistych zostanie przypadkowo usunięty.

  Można to zrobić za pomocą narzędzia AsmMetadataParser. [Dowiedz się więcej o tym narzędziu, w tym miejscu](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Wzorce Powodzenie

Poniżej zostały problemy wykryte w wielu większych migracji. To nie jest kompletną listą i przejrzyj [nieobsługiwane funkcje i konfiguracje](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) uzyskać więcej szczegółowych informacji. Może lub nie mogą wystąpić następujące problemy techniczne, ale po wykonaniu rozwiązywania tych przed podjęciem próby wykonania migracji zapewnia płynne.

- **Czy przebiegu weryfikacji/Prepare/przerwania** — jest to prawdopodobnie najważniejszych krok w celu zapewnienia Classic Powodzenie migracji Menedżera zasobów Azure. Migracja interfejsu API zawiera trzy główne kroki: Sprawdzanie poprawności, przygotowanie i zatwierdzić. Sprawdzanie poprawności zostanie odczytać stanu z klasycznego środowiska i zwracanie wyniku wszystkie problemy. Jednak ponieważ niektóre problemy, może istnieć w stosie usługi Azure Resource Manager, sprawdź poprawność nie będzie przechwytywać wszystko. Następnym krokiem w procesie migracji, przygotowanie pomoże udostępnianie tych problemów. Przygotowanie będzie przenieść metadanych ze środowiska klasycznego do usługi Azure Resource Manager, ale zostanie nie zatwierdzić przeniesienie i zostanie nie Usuń lub Zmień elementy na stronie klasycznego. Przebiegu obejmuje przygotowywania migracji, a następnie przerywanie (**nie zatwierdzania**) przygotować migracji. Celem przebiegu zweryfikować/przygotowanie/przerwania jest do wyświetlenia wszystkich metadanych w stosie usługi Azure Resource Manager, sprawdź jego (*programowo lub w portalu*), sprawdź, czy wszystko migruje poprawnie i działa za pośrednictwem technical problemy.  On również zapewni w pewnym sensie czasem trwania migracji, można odpowiednio zaplanować przestoje.  Sprawdź poprawność/przygotowanie/przerwania nie powoduje żadnych przestojów użytkownika; w związku z tym jest nie-znaczący wpływ na użycie aplikacji.
  - Poniższe elementy będzie musiał zostać rozwiązane przed przebiegu, ale te kroki przygotowania przebiegu testu spowoduje również bezpiecznie opróżnienie, jeśli zostaną one pominięte. Podczas migracji enterprise znaleźliśmy przebiegu za bezpieczne i nieoceniony sposobem zapewnienia przygotowanie do migracji.
  - Gdy przygotowanie działa formantu płaszczyzny (operacje zarządzania platformy Azure) zostanie zablokowane dla całej sieci wirtualnej, a więc nie może być zmieniana metadanych maszyny Wirtualnej podczas sprawdzania poprawności/przygotowanie/przerwania.  Ale w przeciwnym razie dowolnej funkcji aplikacji (usług pulpitu zdalnego, maszyna wirtualna użycia, itp.) będzie to miało wpływu.  Użytkownicy maszyn wirtualnych nie będzie wiadomo, że przebiegu jest wykonywana.

- **Express Route obwody i sieci VPN**. Obecnie Express bram trasy z łączami autoryzacji nie można migrować bez przestoju. Aby uzyskać obejście tego problemu, zobacz [ExpressRoute migracji obwody i skojarzone sieci wirtualne z klasycznego modelu wdrażania usługi Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozszerzenia maszyny Wirtualnej** -rozszerzenia maszyny wirtualnej są potencjalnie jedną z największych roadblocks do Migrowanie uruchomionych maszyn wirtualnych. Korygowanie rozszerzeń maszyny Wirtualnej może potrwać zgłaszane 1-2 dni, więc odpowiednio zaplanować.  Działającego agenta platformy Azure jest wymagany do raportować stan rozszerzenia maszyn wirtualnych działających maszyn wirtualnych. Jeśli stan wróci jako uszkodzony dla uruchomionej maszyny Wirtualnej, spowoduje to zatrzymanie migracji. Sam agent musi być w stanie, aby umożliwić migrację, ale jeśli istnieje rozszerzenia na maszynie Wirtualnej, następnie będą potrzebne zarówno pracy agent AND wychodzące połączenie z Internetem (z systemem DNS) do migracji do przeniesienia do przodu.
  - Jeśli połączenie z serwerem DNS zostaną utracone podczas migracji, wszystkie rozszerzenia maszyny Wirtualnej, z wyjątkiem BGInfo v1. \* muszą najpierw zostać usunięte z każdej maszyny Wirtualnej, aby przygotować migracji, a następnie ponowne dodanie do maszyny Wirtualnej po migracji usługi Azure Resource Manager.  **Jest to tylko dla maszyn wirtualnych, które są uruchomione.**  Jeśli maszyny wirtualne zostały zatrzymane deallocated, rozszerzeń maszyny Wirtualnej nie jest konieczne usunięte. **Uwaga:** wiele rozszerzeń, takich jak diagnostyki Azure i zabezpieczeń Centrum monitorowania zostanie ponownie się po migracji, więc usunięcie ich nie jest problemem.
  - Ponadto upewnij się, grup zabezpieczeń sieci są ograniczając ruch wychodzący do Internetu. Może to nastąpić w niektórych konfiguracjach grup zabezpieczeń sieci. Ruch wychodzący do Internetu (i DNS) jest wymagany dla rozszerzeń maszyny Wirtualnej do migracji do usługi Azure Resource Manager. 
  - Istnieją dwie wersje rozszerzenie BGInfo: v1 i v2.  Jeśli maszyna wirtualna została utworzona przy użyciu portalu Azure lub programu PowerShell, maszyna wirtualna prawdopodobnie mają rozszerzenie v1 na nim. To rozszerzenie nie muszą zostać usunięte i zostanie pominięty (nie migracji) migracja interfejsu API. Jednak jeśli klasycznego maszyna wirtualna została utworzona przy użyciu nowego portalu Azure, prawdopodobnie jej opartych na formacie JSON wersji v2 BGInfo, który można poddać migracji do usługi Azure Resource Manager, pod warunkiem, agent działa i wychodzący dostęp do Internetu (i DNS). 
  - **Opcja korygowania 1**. Jeśli wiadomo, że maszyny wirtualne nie będą miały wychodzącego dostępem do Internetu, działającą usługę DNS i Praca agentów programu Azure na maszynach wirtualnych, następnie odinstaluj wszystkie rozszerzenia maszyny Wirtualnej w ramach migracji na przygotowanie, zainstaluj ponownie rozszerzeń maszyny Wirtualnej, po migracji. 
  - **Opcja korygowania 2**. Jeśli rozszerzeń maszyny Wirtualnej są zbyt duże z próg wykluczający, innym rozwiązaniem jest można cofnąć alokacji/zamykania wszystkich maszyn wirtualnych przed migracją. Migrowanie deallocated maszyn wirtualnych, a następnie uruchomić je ponownie po stronie usługi Azure Resource Manager. Tutaj korzyścią jest to, że rozszerzeń maszyny Wirtualnej zostanie poddana migracji. Wadą podwyższonego jest publicznymi wszystkich wirtualnych adresów IP zostaną utracone (może to być z systemem innym niż starter,) i oczywiście maszyn wirtualnych zamknie powodują najwięcej większy wpływ na pracę aplikacji.

    > [!NOTE] 
    > Skonfigurowanie zasad Centrum zabezpieczeń Azure względem uruchomionych maszyn wirtualnych migrowanych musi zostać zatrzymana przed usunięciem rozszerzenia zasad zabezpieczeń, w przeciwnym razie zabezpieczeń rozszerzenie dotyczące monitorowania zostaną zainstalowane ponownie automatycznie na maszynie Wirtualnej po jej usunięciu.

- **Zestawy dostępności** — w przypadku sieć wirtualną (vNet) powinny być migrowane do usługi Azure Resource Manager wdrożenie klasyczny (tj. Usługa w chmurze) zawartych w niej maszyn wirtualnych musi być w jednym zestawie dostępności lub maszyn wirtualnych wszystkie nie może być w jakimkolwiek zestawie dostępności. Mając więcej niż jeden zestaw w usłudze w chmurze dostępności nie jest zgodny z usługi Azure Resource Manager i zostanie zatrzymany migracji.  Ponadto nie może być niektórych maszyn wirtualnych w zestawie dostępności i niektórych maszyn wirtualnych w zestawie dostępności. Aby rozwiązać ten problem, należy skorygować lub zamieniać usługi w chmurze.  Planowanie odpowiednio to może zająć dużo czasu. 

- **Wdrożenia roli sieć Web/proces roboczy** -usługi w chmurze zawierającego role sieci web i proces roboczy nie może migrować do usługi Azure Resource Manager. Role sieć web/proces roboczy najpierw należy usunąć z sieci wirtualnej, przed rozpoczęciem migracji.  Typowe rozwiązania jest przechodzenia wystąpień roli sieć web/proces roboczy do oddzielnych klasycznej sieci wirtualnej powiązaną z obwodem usługi ExpressRoute lub przeprowadzić migrację kodu do nowszej usługi aplikacji PaaS (rozważania wykracza poza zakres tego dokumentu). W pierwszej ponownie wdrożyć case, Utwórz nową sieć wirtualną w klasycznym, Przenieś/ponownego wdrażania ról sieć web/proces roboczy do nowej sieci wirtualnej, a następnie usunięcie jego wdrożeń z sieci wirtualnej, jest przenoszony. Nie zmian w kodzie wymaganych. Nowy [równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md) możliwość może służyć do elementu równorzędnego razem klasycznej sieci wirtualnej zawierającego role sieć web/proces roboczy i innych sieci wirtualnych w tym samym regionie Azure, takich jak sieci wirtualnej jest migrowany (**po ukończeniu migracji sieci wirtualnej, jak połączyć za pomocą sieci wirtualne nie mogą być migrowane**), dlatego zapewnia te same możliwości bez utraty wydajności i nie kary opóźnienia/przepustowość. Podane dodanie [równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md), wdrożenia roli sieć web/proces roboczy teraz łatwo można zminimalizować i blokuje migracji do usługi Azure Resource Manager.

- **Limity przydziału Menedżera zasobów Azure** -regiony platformy Azure mają oddzielne przydziały/limity dla usługi Azure Resource Manager i klasycznym. Mimo że w scenariuszu migracji nowy sprzęt nie jest używane *(możemy Cię zamianę istniejących maszyn wirtualnych ze środowiska klasycznego do usługi Azure Resource Manager)*, limity przydziału Menedżera zasobów Azure musi być w miejscu z wystarczającą wydajność przed można rozpocząć migrację. Poniżej wymieniono główne limity, który NAS spowodować problemy.  Otwórz bilet pomocy technicznej przydziału, aby podnieść granicach. 

    > [!NOTE]
    > Te limity konieczne wygenerowany, w tym samym regionie co Twojej bieżącej enviornment do migracji.
    >

    - Interfejsy sieciowe
    - Moduły równoważenia obciążenia
    - Publiczne adresy IP
    - Statyczne publiczne adresy IP
    - Rdzenie
    - Grupy zabezpieczeń sieci
    - Tabele tras

    Możesz sprawdzić do najnowszej wersji 2.0 interfejsu wiersza polecenia platformy Azure przy użyciu następujących poleceń z bieżącej limity przydziału Menedżera zasobów Azure.

    **Obliczenia bazy danych** *(rdzenie, Avaiability zestawów)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **Sieci** *(sieci wirtualne, statyczne publiczne adresy IP, publiczne adresy IP, grupy zabezpieczeń sieciowych, sieci interfejsów, równoważenia obciążenia, tabele tras)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Magazyn** *(konta magazynu)*
    
    ```bash
    az storage account show-usage
    ```

- **Usługa Azure Resource Manager interfejsu API ograniczenie** — Jeśli masz środowisku wystarczająco duży (np. > 400 maszyn wirtualnych w sieci Wirtualnej), może być trafień API domyślne ograniczenie dla operacji zapisu (obecnie **1200 zapisy na godzinę**) w usłudze Azure Resource Manager. Przed rozpoczęciem migracji, należy wywołać biletu pomocy technicznej, aby zwiększyć ten limit dla Twojej subskrypcji.

- **Upłynął limit stanu maszyny Wirtualnej udostępniania** — Jeśli żadnej maszyny Wirtualnej jest w stanie **inicjowania obsługi administracyjnej został przekroczony**, musi to być rozwiązane przed migracją. Jedynym sposobem, w tym celu jest przestojów anulowania obsługi/reprovisioning maszyny Wirtualnej (usuwanie, Zachowaj dysk i Utwórz ponownie maszynę Wirtualną). 

- **Stan maszyny Wirtualnej RoleStateUnknown** — Jeżeli migracja zostanie zatrzymany z powodu **nieznany stan roli** komunikat o błędzie, sprawdź maszyny Wirtualnej za pomocą portalu i upewnij się, jest uruchomiona. Ten błąd zazwyczaj zniknie jego właścicielem (Brak korygowania wymagane), po upływie kilku minut i jest często przejściowego typu często występujące podczas maszyny wirtualnej **start**, **zatrzymać**, **Uruchom ponownie** operacji. **Zalecana praktyka:** ponów próbę migracji ponownie za kilka minut. 

- **Klaster sieci szkieletowej nie istnieje** — w niektórych przypadkach niektórych nie można migrować maszyny wirtualne z różnych powodów nieparzysta. Jeden z tych przypadkach znane jest Jeśli maszyna wirtualna została ostatnio utworzony (w ciągu ostatniego tygodnia lub tak) i stało się trafić klastrze platformy Azure, która nie jest jeszcze wyposażony w przypadku obciążeń usługi Azure Resource Manager.  Otrzymasz komunikat o błędzie informujący **klastra sieci szkieletowej nie istnieje** i nie można dokonać migracji maszyny Wirtualnej. Oczekiwanie na kilka dni rozwiąże zwykle tego konkretnego problemu jako klaster wkrótce zostanie wyświetlony usługi Azure Resource Manager włączone. Jednak jeden natychmiastowego rozwiązania jest `stop-deallocate` maszynę Wirtualną, do przodu kontynuować migracji i uruchom maszynę Wirtualną w kopii zapasowej w usłudze Azure Resource Manager po zakończeniu migracji.

### <a name="pitfalls-to-avoid"></a>Problemów, aby uniknąć

- Nie używaj skrótów i Pomiń migracje przebiegu weryfikacji/przygotowanie/przerwania.
- Większość, jeśli nie, Twoje potencjalnych problemów będzie powierzchni etapach weryfikacji/przygotowanie/przerwania.

## <a name="migration"></a>Migracja

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i wady i zalety

Teraz można przystąpić ponieważ pracowano za pomocą znanych problemów ze środowiskiem.

Rzeczywiste migracji można wziąć pod uwagę:

1. Planowanie i Zaplanuj sieci wirtualnej (najmniejszego migracji) z zwiększanie priorytetu.  Najpierw wykonaj proste sieci wirtualnych, a postęp w sieciach wirtualnych bardziej skomplikowane.
2. Większość klientów będą mieć środowiskach nieprodukcyjnych i produkcji.  Ostatnio planowania produkcji.
3. **(OPCJONALNIE)**  Zaplanować przestój konserwacji, z dużą ilością buforu w przypadku spowodować nieoczekiwane problemy.
4. Komunikować się z i Wyrównaj ją z sieci zespoły pomocy technicznej, w przypadku problemach.

### <a name="patterns-of-success"></a>Wzorce Powodzenie

Wskazówki techniczne z powyższej sekcji laboratorium testowego należy został uznany za i skorygowane przed rzeczywistą migracją.  Z odpowiednie testy, migracja jest rzeczywiście niepowiązane ze zdarzeniami.  W środowiskach produkcyjnych może być pomocne dodatkowej pomocy, takich jak zaufanego partnera firmy Microsoft lub usług Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Problemów, aby uniknąć

Nie są w pełni testowania mogą powodować problemy i opóźnienia w procesie migracji.  

## <a name="beyond-migration"></a>Po migracji

### <a name="technical-considerations-and-tradeoffs"></a>Zagadnienia techniczne i wady i zalety

Skoro masz usługi Azure Resource Manager zmaksymalizować platformy.  Odczyt [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) Aby dowiedzieć się o dodatkowe korzyści.

Kwestie do rozważenia:

- Tworzenie pakietów migracji z innymi działaniami.  Większość klientów zdecydować się na okno obsługi aplikacji.  Jeśli tak, można używać tego przestoju w celu włączenia innych możliwości usługi Azure Resource Manager, takie jak szyfrowanie i migracji do zarządzanych dysków.
- Kontroluj powodów techniczne i biznesowe dla usługi Azure Resource Manager; Włącz dodatkowe usługi dostępne tylko w usłudze Azure Resource Manager mające zastosowanie do środowiska.
- Modernizacji środowiska z usługami PaaS.

### <a name="patterns-of-success"></a>Wzorce Powodzenie

Być nigdy wykonywane celowo na usług, które chcesz włączyć usługi Azure Resource Manager.  Znajdź wielu klientów poniżej istotne dla nich środowiska Azure:

- [Kontrola dostępu oparta na rolach](../../azure-resource-manager/resource-group-overview.md#access-control).
- [Szablony usługi Azure Resource Manager dla wdrożenia łatwiejsze i bardziej kontrolowane](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Tagi](../../azure-resource-manager/resource-group-using-tags.md).
- [Formant działania](../../azure-resource-manager/resource-group-audit.md)
- [Zasady usługi Azure](../../azure-policy/azure-policy-introduction.md)

### <a name="pitfalls-to-avoid"></a>Problemów, aby uniknąć

Należy pamiętać o tym, dlaczego uruchomiona ta klasycznego do usługi Azure Resource Manager przebieg migracji.  Jakie były oryginalnego powody biznesowe? Czy uzyskania Przyczyna biznesowe?


## <a name="next-steps"></a>Kolejne kroki

* [Omówienie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager obsługiwane platformy](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager przy użyciu programu PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społeczności z migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migrowania zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
