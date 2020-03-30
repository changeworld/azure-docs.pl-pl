---
title: Praca z poprzednią wersją usługi Azure Migrate
description: W tym artykule opisano sposób pracy z poprzednią wersją usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c9cea52e04a991e6e3ac64426f0443939f8aaa3a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77914385"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Praca z poprzednią wersją usługi Azure Migrate

Ten artykuł zawiera informacje dotyczące pracy z poprzednią wersją usługi Azure Migrate.


Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja:** Użyj tej wersji do tworzenia projektów migracji platformy Azure, odnajdywania maszyn lokalnych oraz organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) o nowościach w tej wersji.
- **Poprzednia wersja**: Jeśli używasz poprzedniej wersji usługi Azure Migrate (obsługiwano tylko ocenę lokalnych maszyn wirtualnych VMware), należy teraz użyć bieżącej wersji. Jeśli nadal musisz używać projektów migracji platformy Azure utworzonych w poprzedniej wersji, jest to, co można i nie można zrobić:
    - Nie można już tworzyć projektów migracji.
    - Zaleca się, aby nie wykonywać nowych odkryć.
    - Nadal można uzyskać dostęp do istniejących projektów.
    - Nadal można przeprowadzać oceny.
    

## <a name="upgrade-between-versions"></a>Uaktualnianie między wersjami

Nie można uaktualnić projektów lub składników w poprzedniej wersji do nowej wersji. Należy [utworzyć nowy projekt migracji platformy Azure](how-to-add-tool-first-time.md)i dodać do niego narzędzia oceny i migracji.

## <a name="find-projects-from-previous-version"></a>Znajdowanie projektów z poprzedniej wersji

Znajdź projekty z poprzedniej wersji w następujący sposób:

1. W witrynie Azure portal > **wszystkie usługi**wyszukaj i wybierz pozycję **Migracja platformy Azure**. 
2. Na pulpicie nawigacyjnym migracji platformy Azure znajduje się powiadomienie i łącze umożliwiające dostęp do starych projektów migracji platformy Azure.
3. Kliknij łącze, aby otworzyć projekty w wersji 1.


## <a name="create-an-assessment"></a>Tworzenie oceny

Po odnalezieniu maszyn wirtualnych w portalu należy je pogrupować i utworzyć oceny.

- Można natychmiast utworzyć jako oceny lokalne natychmiast po wykryciu maszyn wirtualnych w portalu.
- W przypadku ocen opartych na wydajności zaleca się odczekanie co najmniej jednego dnia przed utworzeniem oceny opartej na wydajności, aby uzyskać wiarygodne zalecenia dotyczące rozmiaru.

Utwórz ocenę w następujący sposób:

1. Na stronie **Omówienie** projektu kliknij pozycję **+Utwórz ocenę**.
2. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.
3. Utwórz grupę i określ jej nazwę.
4. Wybierz maszyny wirtualne, które chcesz dodać do grupy.
5. Kliknij pozycję **Utwórz ocenę**, aby utworzyć grupę i ocenę.
6. Po utworzeniu oceny wyświetl ją w**obszarze Pulpit nawigacyjny** **przeglądu** > .
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.

Jeśli chcesz zaktualizować istniejącą ocenę przy użyciu najnowszych danych wydajności, możesz użyć polecenia **Oblicz ponownie** w ramach oceny, aby ją zaktualizować.

## <a name="review-an-assessment"></a>Przegląd oceny 

Ocena obejmuje trzy etapy:

- Ocena rozpoczyna się od analizy przydatności, aby dowiedzieć się, czy maszyny są zgodne na platformie Azure.
- Szacowanie rozmiaru.
- Szacowanie kosztów miesięcznych.

Maszyna przechodzi do późniejszego etapu tylko wtedy, gdy przechodzi poprzedni. Na przykład jeśli komputer nie powiedzie się sprawdzanie przydatności, jest oznaczony jako nieodpowiedni dla platformy Azure i zmiany rozmiaru i wyceny nie jest wykonywana.


### <a name="review-azure-readiness"></a>Przejrzyj gotowość platformy Azure

Widok gotowości platformy Azure w ocenie przedstawia stan gotowości każdej maszyny wirtualnej.

**Analizy gotowości** | **Stan** | **Szczegóły**
--- | --- | ---
Gotowa na platformę Azure | Brak problemów ze zgodnością. Komputer można migrować w stanie— —jest na platformie Azure i zostanie uruchomiony na platformie Azure z pełną obsługą platformy Azure. | W przypadku maszyn wirtualnych, które są gotowe do migracji, usługa Azure Migrate wyświetla zalecany rozmiar maszyny wirtualnej na platformie Azure.
Warunkowo gotowa na platformę Azure | Komputer może uruchomić się na platformie Azure, ale może nie mieć pełnej obsługi platformy Azure. Na przykład komputer ze starszą wersją systemu Windows Server, która nie jest obsługiwana na platformie Azure. | Usługa Azure Migrate wyjaśnia problemy z gotowością i udostępnia kroki korygowania.
Niegotowa na platformę Azure |  Maszyna wirtualna nie zostanie uruchomiony na platformie Azure. Na przykład jeśli maszyna wirtualna ma dysk o pojemności większej niż 4 TB, nie można jej hostować na platformie Azure. | Usługa Azure Migrate wyjaśnia problemy z gotowością i udostępnia kroki korygowania.
Nieznana gotowość | Usługa Azure Migrate nie może zidentyfikować gotowości platformy Azure, zwykle dlatego, że dane nie są dostępne.


#### <a name="azure-vm-properties"></a>Właściwości maszyny Wirtualnej platformy Azure
Gotowość uwzględnia szereg właściwości maszyny Wirtualnej, aby określić, czy maszyna wirtualna może działać na platformie Azure.


**Właściwość** | **Szczegóły** | **Analizy gotowości**
--- | --- | ---
**Typ rozruchu** | BIOS obsługiwane. UEFI nie jest obsługiwane. | Warunkowo gotowy, jeśli typ rozruchu jest UEFI.
**Rdzenie** | Maszyny <rdzenia = maksymalna liczba rdzeni (128) obsługiwane dla maszyny wirtualnej platformy Azure.<br/><br/> Jeśli historia wydajności jest dostępna, usługa Azure Migrate uwzględnia wykorzystane rdzenie.<br/>Jeśli w ustawieniach oceny określono współczynnik komfortu, liczba wykorzystanych rdzeni jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii wydajności, usługa Azure Migrate używa przydzielonych rdzeni bez stosowania współczynnika komfortu. | Gotowy, jeśli jest mniejszy lub równy limitom.
**Pamięci** | Rozmiar pamięci komputera <= maksymalna pamięć (3892 GB w&nbsp;serii M platformy Azure Standard_M128m<sup>2)</sup>dla maszyny Wirtualnej platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Jeśli historia wydajności jest dostępna, usługa Azure Migrate uwzględnia wykorzystaną pamięć.<br/><br/>Jeśli określono współczynnik komfortu, wykorzystana pamięć jest mnożona przez współczynnik komfortu.<br/><br/> Jeśli nie ma historii przydzielonej pamięci jest używany, bez stosowania współczynnika komfortu.<br/><br/> | Gotowy, jeśli w granicach.
**Dysk magazynu** | Przydzielony rozmiar dysku musi wynosić 4 TB (4096 GB) lub mniej.<br/><br/> Liczba dysków podłączonych do urządzenia musi wynosić 65 lub mniej, łącznie z dyskiem systemu operacyjnego. | Gotowy, jeśli w granicach.
**Obsługa sieci** | Maszyna musi mieć do niej przymocowane 32 lub mniej kart sieciowych. | Gotowy, jeśli w granicach.

#### <a name="guest-operating-system"></a>System operacyjny gościa

Wraz z właściwości maszyny wirtualnej usługi Azure Migrate również analizuje gołym okiem użytkownika lokalnej maszyny Wirtualnej, aby określić, czy maszyna wirtualna może działać na platformie Azure.

- Usługa Azure Migrate uwzględnia system operacyjny określony w systemie vCenter Server.
- Ponieważ odnajdowanie wykonane przez usługę Azure Migrate jest oparte na urządzeniu, nie ma sposobu, aby sprawdzić, czy system operacyjny działający wewnątrz maszyny Wirtualnej jest taki sam, jak określony w vCenter Server.

Używana jest następująca logika.

**System operacyjny** | **Szczegóły** | **Analizy gotowości**
--- | --- | ---
Windows Server 2016 i wszystkie usługi SPs | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
Windows Server 2012 R2 i wszystkie SPs | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
Windows Server 2012 i wszystkie usługi SPs | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
Windows Server 2008 R2 i wszystkie SPs | Platforma Azure zapewnia pełną obsługę.| Gotowa na platformę Azure
Windows Server 2008 (32-bitowy i 64-bitowy) | Platforma Azure zapewnia pełną obsługę. | Gotowa na platformę Azure
Windows Server 2003, 2003 R2 | Brak pomocy technicznej i potrzebujesz [niestandardowej umowy pomocy technicznej (CSA)](https://aka.ms/WSosstatement) do obsługi na platformie Azure. | Warunkowo gotowy do platformy Azure, należy rozważyć uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Brak wsparcia. Komputer może uruchomić się na platformie Azure, ale nie ma obsługi systemu operacyjnego jest dostarczana przez platformę Azure. | Warunkowo gotowy do platformy Azure, zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Windows Client 7, 8 i 10 | Platforma Azure zapewnia pomoc techniczną tylko w [ramach subskrypcji programu Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Warunkowo gotowa na platformę Azure
Pulpit systemu Windows 10 Pro | Platforma Azure zapewnia pomoc techniczną z [prawami do hostingu wielodostępnego.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Warunkowo gotowa na platformę Azure
Windows Vista, XP Professional | Brak wsparcia. Komputer może uruchomić się na platformie Azure, ale nie ma obsługi systemu operacyjnego jest dostarczana przez platformę Azure. | Warunkowo gotowy do platformy Azure, zaleca się uaktualnienie systemu operacyjnego przed migracją na platformę Azure.
Linux | Platforma Azure wspiera te [systemy operacyjne Linux](../virtual-machines/linux/endorsed-distros.md). Inne systemy operacyjne Linux może uruchomić na platformie Azure, ale zaleca się uaktualnienie systemu operacyjnego do zatwierdzonej wersji, przed migracją na platformę Azure. | Gotowe do platformy Azure, jeśli wersja jest zatwierdzona.<br/><br/>Warunkowo gotowy, jeśli wersja nie jest zatwierdzona.
Inne systemy operacyjne<br/><br/> Na przykład Oracle Solaris, Apple Mac OS itp., FreeBSD itp. | Platforma Azure nie wspiera tych systemów operacyjnych. Komputer może uruchomić się na platformie Azure, ale nie ma obsługi systemu operacyjnego jest dostarczana przez platformę Azure. | Warunkowo gotowy do platformy Azure, zaleca się zainstalowanie obsługiwanego systemu operacyjnego przed migracją na platformę Azure.  
System operacyjny określony jako **Inne** w systemie vCenter Server | Usługa Azure Migrate nie może zidentyfikować systemu operacyjnego w tym przypadku. | Nieznana gotowość. Upewnij się, że system operacyjny działający wewnątrz maszyny Wirtualnej jest obsługiwany na platformie Azure.
32-bitowe systemy operacyjne | Komputer może uruchomić się na platformie Azure, ale platforma Azure może nie zapewniać pełnej pomocy technicznej. | Warunkowo gotowy do platformy Azure, należy rozważyć uaktualnienie systemu operacyjnego komputera z 32-bitowego systemu operacyjnego do 64-bitowego systemu operacyjnego przed migracją na platformę Azure.


### <a name="review-sizing"></a>Przejrzyj rozmiar

 Zalecenie rozmiaru migracji platformy Azure zależy od kryterium zmiany rozmiaru określonego we właściwościach oceny.

- Jeśli rozmiar jest oparty na wydajności, zalecenie rozmiaru uwzględnia historię wydajności maszyn wirtualnych (procesora CPU i pamięci) i dysków (We/Wy i przepływność).
- Jeśli kryterium zmiany rozmiaru jest "jako lokalne", zalecenie rozmiaru na platformie Azure jest oparte na rozmiarze lokalnej maszyny Wirtualnej. Rozmiar dysku zależy od typu Magazyn określony we właściwościach oceny (domyślnie są dyski premium). Usługa Azure Migrate nie uwzględnia danych dotyczących wydajności maszyny Wirtualnej i dysków.

### <a name="review-cost-estimates"></a>Przegląd szacowanych kosztów

Szacunki kosztów pokazują całkowity koszt obliczeń i magazynu podczas uruchamiania maszyn wirtualnych na platformie Azure wraz ze szczegółami dotyczącymi każdego komputera.

- Szacunki kosztów są obliczane przy użyciu zalecenia rozmiaru maszyny wirtualnej i jego dysków oraz właściwości oceny.
- Szacowany miesięczny koszt zasobów obliczeniowych i magazynowych jest agregowany dla wszystkich maszyn wirtualnych w grupie.
- Szacowanie kosztów jest do uruchamiania lokalnej maszyny Wirtualnej jako infrastruktury platformy Azure jako usługi (IaaS) maszyny wirtualne. Usługa Azure Migrate nie uwzględnia platformy jako usługi (PaaS) lub oprogramowania jako kosztów usługi (SaaS).

### <a name="review-confidence-rating-performance-based-assessment"></a>Ocena zaufania (ocena oparta na wynikach)

Każda ocena oparta na wynikach jest skojarzona z oceną zaufania.

- Ocena zaufania waha się od jednej gwiazdki do pięciu gwiazdek (jedna gwiazdka jest najniższa, a pięć gwiazdek najwyższa).
- Ocena zaufania jest przypisywana do oceny, opartej na dostępności punktów danych potrzebnych do obliczenia oceny.
- Ocena zaufania do oceny pomaga oszacować niezawodność zaleceń dotyczących rozmiaru określanych przez usługę Azure Migrate.
- Ocena zaufania nie jest dostępna dla ocen lokalnych "tak jak jest".

Aby uzyskać rozmiar oparty na wydajności, usługa Azure Migrate wymaga następujących elementów:
- Wykorzystanie danych dla procesora CPU.
- Dane pamięci maszyny Wirtualnej.
- Dla każdego dysku podłączonego do maszyny Wirtualnej, potrzebuje danych we/wy i przepływności dysku.
- Dla każdej karty sieciowej podłączonej do maszyny Wirtualnej usługa Azure Migrate wymaga wejścia/wyjścia sieci.
- Jeśli którykolwiek z powyższych nie są dostępne, zalecenia dotyczące rozmiaru (a tym samym oceny zaufania) może nie być wiarygodne.


W zależności od procentu dostępnych punktów danych możliwe oceny zaufania są sumowane w tabeli.

**Dostępność punktów danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Kwestie oceny wpływające na oceny zaufania

Ocena może nie mieć wszystkich dostępnych punktów danych z wielu powodów:

- Nie profiluj środowiska przez cały czas trwania oceny. Na przykład jeśli tworzysz ocenę z czasem trwania wydajności ustawionym na jeden dzień, musisz poczekać co najmniej jeden dzień po rozpoczęciu odnajdywania lub wszystkie punkty danych do zebrania.
- Niektóre maszyny wirtualne zostały zamknięte w okresie, dla którego obliczono ocenę. Jeśli wszystkie maszyny wirtualne zostały wyłączone przez część czasu trwania, usługa Azure Migrate nie może zbierać danych o wydajności dla tego okresu.
- Niektóre maszyny wirtualne zostały utworzone pomiędzy w okresie obliczania oceny. Na przykład jeśli tworzysz ocenę przy użyciu historii wydajności ostatniego miesiąca, ale utworzysz wiele maszyn wirtualnych w środowisku tydzień temu, historia wydajności nowych maszyn wirtualnych nie będzie przez cały czas trwania.

> [!NOTE]
> Jeśli ocena zaufania jakiejkolwiek oceny jest niższa niż pięć gwiazdek, poczekaj co najmniej jeden dzień, aby urządzenie profilować środowisko, a następnie ponownie obliczyć ocenę. Jeśli rozmiar nie jest oparty na wydajności może nie być niezawodny. Jeśli nie chcesz ponownie obliczać, zalecamy przełączenie do rozmiaru lokalnego, zmieniając właściwości oceny.



## <a name="create-groups-using-dependency-visualization"></a>Tworzenie grup przy użyciu wizualizacji zależności

Oprócz ręcznego tworzenia grup można tworzyć grupy przy użyciu wizualizacji zależności.
- Zazwyczaj tej metody, gdy chcesz ocenić grupy z wyższym poziomem zaufania przez krzyżowe sprawdzanie zależności komputera, przed uruchomieniem oceny.
- Wizualizacja zależności może pomóc w skutecznym zaplanowaniu migracji na platformę Azure. Pomaga upewnić się, że nic nie pozostaje w tyle i że niespodziewane awarie nie występują podczas migracji na platformę Azure.
- Można odkryć wszystkie współzależne systemy, które muszą migrować razem i określić, czy uruchomiony system nadal obsługuje użytkowników, czy jest kandydatem do likwidacji zamiast migracji.
- Usługa Azure Migrate używa rozwiązania mapy usług w usłudze Azure Monitor, aby włączyć wizualizację zależności.

> [!NOTE]
> Wizualizacja zależności nie jest dostępna w usłudze Azure Government.

Aby skonfigurować wizualizację zależności, należy skojarzyć obszar roboczy usługi Log Analytics z projektem migracji platformy Azure, zainstalować agentów na komputerach, dla których chcesz wizualizować zależności, a następnie utworzyć grupy przy użyciu informacji o zależnościach. 



### <a name="associate-a-log-analytics-workspace"></a>Kojarzenie obszaru roboczego usługi Log Analytics

Aby użyć wizualizacji zależności, należy skojarzyć obszar roboczy usługi Log Analytics z projektem migracji. Obszar roboczy można utworzyć lub dołączyć tylko w tej samej subskrypcji, w której tworzony jest projekt migracji.

1. Aby dołączyć obszar roboczy usługi Log Analytics do projektu, w **obszarze Przegląd**, > **Essentials**, kliknij pozycję **Wymaga konfiguracji**.
2. Można utworzyć nowy obszar roboczy lub dołączyć istniejący obszar roboczy:
  - Aby utworzyć nowy obszar roboczy, określ nazwę. Obszar roboczy jest tworzony w regionie w tej samej [geografii platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migracji.
  - Po dołączeniu istniejącego obszaru roboczego można wybrać ze wszystkich dostępnych obszarów roboczych w tej samej subskrypcji co projekt migracji. Wyświetlane są tylko te obszary robocze, które zostały utworzone w [obsługiwanym regionie mapy usług](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Aby dołączyć obszar roboczy, upewnij się, że masz dostęp "Reader" do obszaru roboczego.

> [!NOTE]
> Nie można zmienić obszaru roboczego skojarzonego z projektem migracji.

### <a name="download-and-install-vm-agents"></a>Pobieranie i instalowanie agentów maszyn wirtualnych

Po skonfigurowaniu obszaru roboczego należy pobrać i zainstalować agentów na każdym komputerze lokalnym, który chcesz ocenić. Ponadto jeśli masz komputery bez połączenia z Internetem, musisz pobrać i zainstalować na nich [bramę usługi Log Analytics.](../azure-monitor/platform/gateway.md)

1. W **obszarze Przegląd**kliknij pozycję **Zarządzaj** > **maszynami**i wybierz wymaganą maszynę.
2. W kolumnie **Zależności** kliknij pozycję **Zainstaluj agentów**.
3. Na stronie **Zależności** pobierz i zainstaluj agenta monitorowania firmy Microsoft (MMA) i agenta zależności na każdej maszynie wirtualnej, którą chcesz ocenić.
4. Skopiuj identyfikator i klucz obszaru roboczego. Są one potrzebne podczas instalowania programu MMA na komputerze lokalnym.

> [!NOTE]
> Aby zautomatyzować instalację agentów, można użyć narzędzia wdrażania, takiego jak menedżer konfiguracji lub narzędzie partnerskie, takie jak [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), które zapewnia rozwiązanie wdrażania agenta dla usługi Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instalowanie agenta MMA na komputerze z systemem Windows

Aby zainstalować agenta na komputerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij pozycję **Wyrażam zgodę** na zaakceptowanie licencji.
3. W **obszarze Folder docelowy**zachowaj lub zmodyfikuj domyślny folder instalacyjny > **Dalej**.
4. W **obszarze Opcje konfiguracji agenta**wybierz pozycję Azure Log **Analytics** > **Next**.
5. Kliknij **przycisk Dodaj,** aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator obszaru roboczego i klucz skopiowany z portalu. Kliknij przycisk **alej**.

Agenta można zainstalować z wiersza polecenia lub przy użyciu zautomatyzowanej metody, takiej jak Menedżer konfiguracji. [Dowiedz się więcej](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) o instalowaniu agenta MMA przy użyciu tych metod.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instalowanie agenta MMA na komputerze z systemem Linux

Aby zainstalować agenta na komputerze z systemem Linux:

1. Przenieś odpowiedni pakiet (x86 lub x64) na komputer z systemem Linux za pomocą scp/sftp.
2. Zainstaluj pakiet przy użyciu argumentu --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) o liście systemów operacyjnych Linux obsługiwanych przez MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Instalowanie agenta MMA na komputerze monitorowanym przez menedżera operacyjnego

W przypadku maszyn monitorowanych przez program System Center Operations Manager w wersji 2012 R2 lub nowszej nie ma potrzeby instalowania agenta MMA. Mapa usługi integruje się z programem MMA programu Operations Manager w celu zebrania niezbędnych danych zależności. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Agent zależności musi być zainstalowany.

### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności

1. Aby zainstalować agenta zależności na komputerze z systemem Windows, kliknij dwukrotnie plik instalacyjny i postępuj zgodnie z instrukcjami kreatora.
2. Aby zainstalować agenta zależności na komputerze z systemem Linux, zainstaluj jako katalog główny za pomocą następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

- Dowiedz się więcej o [obsłudze agenta zależności](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) dla systemów operacyjnych Windows i Linux.
- [Dowiedz się więcej](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) o tym, jak zainstalować agenta zależności za pomocą skryptów.

>[!NOTE]
> Artykuł usługi Azure Monitor dla maszyn wirtualnych, do którego odwołuje się omówienie wymagań wstępnych systemu i metod wdrażania agenta zależności, ma również zastosowanie do rozwiązania mapy usług.

### <a name="create-a-group-with-dependency-mapping"></a>Tworzenie grupy z mapowaniem zależności

1. Po zainstalowaniu agentów przejdź do portalu i kliknij przycisk **Zarządzaj** > **maszynami**.
2. Wyszukaj maszynę, na której zainstalowano agentów.
3. **Kolumna Zależności** dla komputera powinna być teraz pokazywalna jako **zależności widoku.** Kliknij kolumnę, aby wyświetlić zależności komputera.
4. Mapa zależności dla komputera zawiera następujące szczegóły:
    - Połączenia TCP przychodzące (klienci) i wychodzące (serwery) do/z komputera
        - Maszyny zależne, które nie mają zainstalowanego agenta MMA i agenta zależności, są pogrupowane według numerów portów.
        - Maszyny zależne, które mają zainstalowany MMA i agent zależności są wyświetlane jako oddzielne pola.
    - Procesy uruchomione wewnątrz maszyny, można rozwinąć każdą skrzynkę maszyn, aby wyświetlić procesy
    - Wyświetlane są właściwości komputera, w tym FQDN, system operacyjny, adres MAC. Możesz kliknąć każde pole maszyny, aby wyświetlić szczegóły.

4. Zależności dla różnych czasów trwania można wyświetlić, klikając czas trwania w etykiecie zakresu czasu. Domyślnie zakres wynosi godzinę. Można zmodyfikować zakres czasu lub określić daty rozpoczęcia i zakończenia oraz czas trwania.

   > [!NOTE]
   >    Obsługiwany jest zakres czasu do godziny. Użyj dzienników usługi Azure Monitor, aby [zbadać dane zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) przez dłuższy czas trwania.

5. Po zidentyfikowaniu komputerów zależnych, które chcesz zgrupować, użyj klawiszy Ctrl+Click, aby wybrać wiele komputerów na mapie, a następnie kliknij pozycję **Grupuj maszyny**.
6. Określ nazwę grupy. Sprawdź, czy maszyny zależne są odnajdowane przez usługę Azure Migrate.

    > [!NOTE]
    > Jeśli komputer zależny nie zostanie wykryty przez usługę Azure Migrate, nie można dodać go do grupy. Aby dodać takie maszyny do grupy, należy ponownie uruchomić proces odnajdywania z odpowiednim zakresem w systemie vCenter Server i upewnić się, że komputer zostanie wykryty przez usługę Azure Migrate.  

7. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz to pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij **przycisk OK,** aby zapisać grupę.

Po utworzeniu grupy zaleca się instalowanie agentów na wszystkich komputerach grupy i udoskonalanie grupy przez wizualizację zależności całej grupy.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Dane zależności kwerendy z dzienników usługi Azure Monitor

Dane zależności przechwycone przez mapę usługi są dostępne do wykonywania zapytań w obszarze roboczym usługi Log Analytics skojarzonym z projektem migracji platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) o tabelach danych mapy usług do wykonywania zapytań w dziennikach usługi Azure Monitor. 

Aby uruchomić zapytania Kusto:

1. Po zainstalowaniu agentów przejdź do portalu i kliknij przycisk **Przegląd**.
2. W **obszarze Przegląd**przejdź do sekcji **Essentials** projektu i kliknij nazwę obszaru roboczego podajoną obok **obszaru roboczego usługi OMS**.
3. Na stronie Obszaru roboczego usługi Log Analytics kliknij pozycję**Dzienniki** **ogólne** > .
4. Napisz zapytanie, aby zebrać dane zależności przy użyciu dzienników usługi Azure Monitor. Znajdź przykładowe zapytania w następnej sekcji.
5. Uruchom zapytanie, klikając przycisk Uruchom. 

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) o tym, jak pisać zapytania Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Przykładowy monitor Platformy Azure rejestruje kwerendy

Poniżej przedstawiono przykładowe kwerendy, których można użyć do wyodrębnienia danych zależności. Można zmodyfikować kwerendy, aby wyodrębnić preferowane punkty danych. Tutaj [dostępna](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)jest wyczerpująca lista pól w rekordach danych zależności. Więcej przykładowych zapytań znajdziesz [tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Podsumowywanie połączeń przychodzących na zestawie maszyn

Rekordy w tabeli dla metryk połączenia, VMConnection, nie reprezentują poszczególnych fizycznych połączeń sieciowych. Wiele fizycznych połączeń sieciowych jest grupowanych w połączenie logiczne. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o tym, jak dane połączenia sieciowego są agregowane w jeden rekord logiczny w VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Podsumowanie ilości danych wysyłanych i odbieranych na połączeniach przychodzących między zestawem komputerów

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o](migrate-services-overview.md) najnowszej wersji programu Azure Migrate.
