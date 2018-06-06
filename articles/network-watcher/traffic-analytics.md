---
title: Analiza ruchu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i analizować dzienniki przepływu grupy zabezpieczeń sieci platformy Azure z analizy ruchu.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: f7603d56a56e45771fa170c05ec62a0725ba367a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715826"
---
# <a name="traffic-analytics"></a>Analiza ruchu

Analiza ruchu jest oparta na chmurze rozwiązaniem, które zapewnia wgląd w działania użytkowników i aplikacji w sieciach w chmurze. Analiza ruchu analizuje dzienniki zabezpieczeń sieci obserwatora sieciowego przepływu grupy (NSG) zapewniające wgląd w ruchu w chmurze Azure. Analiza ruchu sieciowego można:

- Wizualizacja aktywności sieci subskrypcji platformy Azure i Znajdź punkty aktywne.
- Identyfikowanie zagrożenia bezpieczeństwa i zabezpieczenia sieci o informacje, takie jak otwieranie portów, próba dostępu do Internetu i maszyn wirtualnych (VM) nawiązywania połączenia z sieciami nieautoryzowane aplikacji.
- Zrozumieć wzorce przepływu ruchu w różnych regionach platformy Azure i internet w celu zoptymalizowania wdrożenia sieci wydajność i pojemność.
- Wskazanie błędów konfiguracji sieci, co może prowadzić do połączenia nie powiodło się w sieci.

## <a name="why-traffic-analytics"></a>Dlaczego ruchu analytics?

Jest to niezbędne do monitorowania, zarządzania i wiedzieć własnej sieci, który nie został naruszony zabezpieczeń, zgodności i wydajności. Znajomość własnego środowiska jest najważniejsze do ochrony i zoptymalizować go. Często muszą znać bieżący stan sieci, który nawiązuje połączenie, where, które porty są otwarte z Internetem, oczekiwane zachowanie sieci, zachowanie nieregularne sieci i nagłego wzrostu ruchu.

Sieciach w chmurze są inne niż lokalnej sieci przedsiębiorstwa, gdzie masz Netflow lub routery obsługujące protokół równoważne i przełączników, które zapewniają możliwość zbierania ruchu sieciowego IP wprowadza lub kończy działanie interfejsu sieciowego. Analizując ruch przepływu danych, można tworzyć analizy ruchu sieciowego i wolumin.

Dzienniki przepływu NSG, zapewniających informacji na temat ruch przychodzący ma sieci wirtualnych platformy Azure i ruch wychodzący ruch pakietów IP za pośrednictwem grupy zabezpieczeń sieci skojarzonych z interfejsów sieciowych poszczególnych maszyn wirtualnych i podsieci. Analizowanie danych pierwotnych dzienniki przepływu NSG, a następnie wstawianie analizy ruchu zabezpieczeń, topologii i lokalizacji geograficznej, analytics może udostępnić wgląd w ruchu w danym środowisku. Analiza ruchu udostępnia takie informacje jak najbardziej komunikacji hostów, najbardziej komunikacji protokołów aplikacji, najbardziej konwersację pary hosta, dozwolone/blokowane ruchu, ruchu przychodzącego/wychodzącego, otwartych portów internetowych, reguły blokowania najbardziej, ruch dystrybucji na centrum danych Azure, sieci wirtualnej, podsieci, lub nieautoryzowane sieci.

## <a name="key-components"></a>Główne składniki 

- **Grupy zabezpieczeń sieci (NSG)**: zawiera listę reguł zabezpieczeń, które akceptować lub odrzucać ruch sieciowy do zasoby podłączone do sieci wirtualnej platformy Azure. Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi (model klasyczny) lub poszczególnymi interfejsami sieciowymi (NIC) dołączonymi do maszyn wirtualnych (model usługi Resource Manager). Aby uzyskać więcej informacji, zobacz [omówienie grupy zabezpieczeń sieci](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Dzienniki przepływu grupa zabezpieczeń sieci**: umożliwia wyświetlanie informacji o przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci. Grupy NSG przepływu dzienniki są zapisywane w formacie json i Pokaż wychodzących i przepływów przychodzących na podstawie reguł na, przepływ karty interfejsu Sieciowego dotyczy, 5-elementowej informacji na temat przepływu (źródłowego i docelowego adresu IP, portu źródłowego i docelowego i protocol), a jeśli ruch jest dozwolone, lub Odmowa dostępu. Aby uzyskać więcej informacji o dziennikach przepływu NSG, zobacz [dzienniki przepływu NSG](network-watcher-nsg-flow-logging-overview.md).
- **Zaloguj się Analytics**: Usługa Azure, która służy do zbierania danych monitorowania i przechowuje dane w centralnym repozytorium. Dane te mogą obejmować zdarzeń, danych wydajności lub niestandardowe dane przekazane za pośrednictwem interfejsu API platformy Azure. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu. Monitorowanie aplikacji, takich jak analizy ruchu i monitor wydajności sieci są tworzone przy użyciu analizy dzienników jako podstawę. Aby uzyskać więcej informacji, zobacz [dziennika analizy](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Obszar roboczy analizy dziennika**: wystąpienie analizy dzienników, w którym są przechowywane dane dotyczące konta platformy Azure. Aby uzyskać więcej informacji na temat obszarów roboczych usługi Analiza dzienników, zobacz [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Monitor sieci**: regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki na poziomie scenariusz sieci na platformie Azure. Można włączyć NSG przepływu dzienniki włączać i wyłączać z obserwatora sieciowego. Aby uzyskać więcej informacji, zobacz [obserwatora sieciowego](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Jak działa analizy ruchu 

Analiza ruchu sprawdza nowych dzienników przepływu NSG i przechwytywanie zmniejszenie dzienniki przez agregowanie przepływów typowe między tego samego źródłowego adresu IP, docelowy adres IP, docelowy port i protokół. Na przykład 1 hosta (adres IP: 10.10.10.10) komunikowania się z hostem 2 (adres IP: 10.10.20.10), 100 razy w okresie 1 godziny przy użyciu portu (na przykład 80) i protokół (na przykład http). Zmniejszenie dziennik ma jeden wpis, który Host 1 i 2 hosta przekazywane 100 razy w ciągu 1 godziny przy użyciu portu *80* i protokół *HTTP*, zamiast pozycji 100. Zmniejszenie dzienniki są rozszerzona o lokalizacji geograficznej, zabezpieczeń i informacji o topologii, a następnie przechowywane w obszarze roboczym analizy dzienników. Na poniższej ilustracji przedstawiono przepływ danych:

![Przepływ danych dzienników przepływu NSG przetwarzania](media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Obsługiwane regiony

Analiza ruchu jest dostępna w wersji zapoznawczej. Funkcje w wersji zapoznawczej ma taki sam poziom dostępności i niezawodności jako funkcje ogólnie wersji.  W wersji zapoznawczej, można użyć analizy ruchu dla grup NSG w jednym z następujących regionach: zachodnie centralnej nam wschodnie stany USA, wschodnie stany USA 2, północno-środkowe stany, południowo-środkowe stany, środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Europa Zachodnia, Europa Północna, Wielka Brytania Zachodnia, Wielka Brytania Południowa, Australia Wschodnia , a Australia południowo-wschodnia. Obszar roboczy analizy dzienników musi istnieć w zachodnie centralnej nam wschodnie stany USA, Europa Zachodnia, Południowo-Wschodnia Australia albo region Wielka Brytania Południowa.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher 

Analiza ruchu, musisz mieć istniejących obserwatora sieciowego lub [włączyć obserwatora sieciowego](network-watcher-create.md) w poszczególnych regionach, czy masz grup NSG, które mają być analizowane ruch związany. Analiza ruchu mogą być włączone dla grupy NSG hostowanej w żadnym z [obsługiwane regiony](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Ponowne zarejestrowanie dostawcy zasobów sieciowych 

Przed użyciem analizy ruchu w wersji zapoznawczej, należy ponownie zarejestrować dostawcy zasobów sieciowych. Kliknij przycisk **spróbuj on** w polu kodu poniżej, aby otworzyć powłokę chmury Azure. Powłoka chmury automatycznie rejestruje należy do Twojej subskrypcji platformy Azure. Po otwarciu powłoki chmury, wprowadź następujące polecenie, aby ponownie zarejestrować dostawcy zasobów sieciowych:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Wybierz grupy zabezpieczeń sieci 

Przed włączeniem rejestrowania przepływu NSG, musi mieć grupę zabezpieczeń sieci do logowania przepływów. Jeśli nie masz sieciowej grupy zabezpieczeń, zobacz [Utwórz grupę zabezpieczeń sieci](../virtual-network/manage-network-security-group.md#create-a-network-security-group) go utworzyć.

Po lewej stronie portalu Azure, wybierz **Monitor**, następnie **obserwatora sieciowego**, a następnie wybierz **dzienniki przepływu NSG**. Wybierz grupy zabezpieczeń sieci, który chcesz włączyć dziennik przepływu NSG, jak pokazano na poniższej ilustracji:

![Wybór grup NSG, wymagających aktywacji dziennika przepływu NSG](media/traffic-analytics/selection-of-nsgs-that-require- enablement-of-nsg-flow-logging.png)

Jeśli spróbujesz Włącz analizy ruchu dla grupy NSG, który znajduje się w dowolnym regionie innym niż [obsługiwane regiony](#supported-regions), komunikat o błędzie "Nie została odnaleziona". 

## <a name="enable-flow-log-settings"></a>Włącz ustawienia dziennika przepływu

Przed włączeniem ustawienia dziennika przepływu, należy wykonać następujące zadania:

Zarejestruj dostawcę usługi Azure Insights, jeśli jeszcze nie jest zarejestrowany dla Twojej subskrypcji:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Jeśli nie masz jeszcze konta usługi Azure Storage do przechowywania przepływu NSG loguje, należy utworzyć konta magazynu. W poleceniu występującym można utworzyć konta magazynu. Przed uruchomieniem polecenia należy zastąpić `<replace-with-your-unique-storage-account-name>` nazwę, która jest unikatowa dla wszystkich lokalizacji platformy Azure, w zakresie od 3 do 24 znaków długości, używając tylko cyfry i małe litery. Można również zmienić nazwę grupy zasobów, w razie potrzeby.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Wybierz poniższe opcje, jak pokazano na rysunku:

1. Wybierz *na* dla **stanu**
2. Wybierz istniejące konto magazynu do przechowywania dzienników przepływu w. Jeśli chcesz przechowywać dane w nieskończoność, ustaw wartość na *0*. Naliczone opłaty za magazyn Azure dla konta magazynu.
3. Ustaw **przechowywania** liczby dni, które mają być przechowywane dane.
4. Wybierz *na* dla **ruchu Analytics stanie**.
5. Wybierz istniejący obszar roboczy analizy dzienników (OMS) lub **Utwórz nowy obszar roboczy** Aby utworzyć nowy. Obszar roboczy analizy dzienników jest używany przez analityka ruchu do przechowywania danych zagregowane i indeksowanych, który jest następnie używany do generowania do analizy. Jeśli wybierzesz istniejący obszar roboczy, musi istnieć w jednym z [obsługiwane regiony](#traffic-analytics-supported-regions) i nie zostaną uaktualnione do nowego języka zapytań. Jeśli nie chcesz, aby uaktualnić istniejący obszar roboczy lub nie masz obszaru roboczego w obsługiwanym regionie, Utwórz nową. Aby uzyskać więcej informacji o językach zapytania, zobacz [Analiza dzienników Azure uaktualnienia do nowego wyszukiwania dziennika](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    Nie masz obszaru roboczego analizy dzienników hosting rozwiązania analizy ruchu i grup NSG w tym samym regionie. Na przykład może być analizy ruchu w obszarze roboczym w regionie Europa Zachodnia, gdy masz grup NSG wschodnie stany USA i zachodnie stany USA. W tym samym obszarze roboczym można skonfigurować wiele grup NSG.
6. Wybierz pozycję **Zapisz**.

    ![Wybór konta magazynu, obszar roboczy analizy dzienników i włączanie analizy ruchu](media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Powtórz poprzednie kroki dla innych grup NSG dla których chcesz włączyć analiza ruchu. Dane z dzienników przepływu są wysyłane do obszaru roboczego, dlatego upewnij się, że lokalne przepisom eksportowym obowiązującym w Twoim kraju na przechowywanie danych w regionie, w którym znajduje się obszar roboczy.

## <a name="view-traffic-analytics"></a>Analiza ruchu w widoku

W lewej stronie portalu, wybierz **wszystkie usługi**, wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawia się w wynikach wyszukiwania, wybierz go. Aby rozpocząć eksplorowanie analizy ruchu i jego możliwości, zaznacz **obserwatora sieciowego**, następnie **analizy ruchu (wersja zapoznawcza)**.

![Uzyskiwanie dostępu do pulpitu nawigacyjnego Analytics ruchu](media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Pulpit nawigacyjny może potrwać do 30 minut pojawi się po raz pierwszy, ponieważ analizy ruchu musi najpierw agregacji wystarczającej ilości danych dla niej pochodzić wgląd w istotne dane, zanim może generować raportów.

## <a name="usage-scenarios"></a>Scenariusze użycia

Niektóre szczegółowe informacje, które można uzyskać po ruchu Analytics jest w pełni skonfigurowane, są następujące:

### <a name="find-traffic-hotspots"></a>Znajdowanie ruchu punkty aktywne

**Szukać**

- Hosty, które są wysyłanie i odbieranie najbardziej danych?
    - Opis, których hostów są wysyłaniu lub odbieraniu większość ruchu mogą ułatwić identyfikację hostów, które są przetwarzania większość ruchu.
    - Użytkownik może ocenić, czy wielkość ruchu sieciowego jest odpowiednia dla hosta. Ilość ruchu normalne zachowanie jest lub on wymagają dalszych badań?
- Ilość ruchu przychodzącego/wychodzącego jest?
    -   Host oczekuje na pobranie więcej ruchu przychodzącego ruchu niż wychodzące lub odwrotnie?
- Statystyka dozwolone/blokowane ruchu.
    - Dlaczego jest hostem stosowanie lub blokuje znacząca ilość ruchu?

    Wybierz **szczegółowe**w obszarze **hostów z większość ruchu**, jak pokazano na poniższej ilustracji:

    ![Pulpit nawigacyjny pokazujące hosta z większości szczegóły ruchu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Na poniższej ilustracji przedstawiono czas analizy trendów top pięcioma hostami talking i szczegóły związane z przepływem (dozwolone — przychodzącego/wychodzącego i odmowy - przychodzącego/wychodzącego przepływu) dla maszyny Wirtualnej:

    ![Trend hosta mówić większość 5](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Szukać**

- Które są najbardziej conversing pary hosta?
    - Oczekiwane zachowanie, takich jak front end zaplecza komunikacji lub nieregularne zachowanie, takie jak ruch internetowy wewnętrznej bazy danych.
- Statystyki ruchu dozwolone/blokowane
    - Dlaczego zezwalających lub blokuje znaczny ruch woluminu hosta
- Najczęściej używane protokołu aplikacji między większości conversing pary hosta:
    - Te aplikacje są dozwolone w tej sieci?
    - Aplikacje są skonfigurowane prawidłowo? Są one przy użyciu odpowiedniego protokołu komunikacji? Wybierz **szczegółowe** w obszarze **najbardziej częste konwersacji**, co zostało przedstawione na poniższej ilustracji:

        ![Pulpit nawigacyjny pokazujące najczęstsze konwersacji](media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Na poniższej ilustracji przedstawiono czas analizy trendów dla górnej konwersacji pięć i szczegóły związane z przepływem, takich jak dozwolonych i zabronionych przepływów przychodzących i wychodzących dla pary konwersacji:

    ![Szczegóły konwersacji chatty 5 i trend](media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Szukać**

- Protokołu aplikacji najczęściej używanego w danym środowisku, a które conversing pary hosta najczęściej używają protokołu aplikacji?
    - Te aplikacje są dozwolone w tej sieci?
    - Aplikacje są skonfigurowane prawidłowo? Są one przy użyciu odpowiedniego protokołu komunikacji? Oczekiwane zachowanie jest typowe porty, takie jak 80 i 443. Standardowe komunikacji jeśli są wyświetlane wszelkie nietypowe porty, mogą wymagać zmian w konfiguracji. Wybierz **szczegółowe** w obszarze **Top protokołów aplikacji**, na poniższej ilustracji:

        ![Pulpit nawigacyjny pokazujące protokoły top aplikacji](media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Następujące obrazy Pokaż podczas analizy trendów protokołów P7 5 i szczegóły związane z przepływem (na przykład dozwolonych i zabronionych przepływów w) dla protokołu P7:

    ![Pięć górnej warstwy 7 szczegóły protokołów i trend](media/traffic-analytics/top five-layer-seven-protocols-details-and-trend.png)

    ![Przepływ informacji dla aplikacji protokołu w dzienniku wyszukiwania](media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Szukać**

- Trendy wykorzystania pojemności bramy sieci VPN w danym środowisku.
    - Każda jednostka SKU sieci VPN umożliwia określoną ilość przepustowości. Bramy sieci VPN są wykorzystany?
    - Twoje bram zbliżają pojemność? Należy uaktualnić do dalej nowszej wersji?
- Najbardziej conversing hostach, za pośrednictwem których bramy sieci VPN, które są przez port, który?
    - Ten wzorzec jest normalne? Wybierz **szczegółowe** w obszarze **aktywnych połączeń sieci VPN z góry**, jak pokazano na poniższej ilustracji:

        ![Pulpit nawigacyjny pokazujące top aktywnych połączeń sieci VPN](media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Na poniższej ilustracji przedstawiono czas analizy trendów wykorzystania pojemności bramy sieci VPN platformy Azure i szczegóły związane z przepływem (takie jak dozwolone przepływów i portów):

    ![Sieć VPN bramy trendu i przepływ szczegóły wykorzystania](media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Wizualizuj Dystrybucja ruchu według ich położenia

**Szukać**

- Dystrybucja ruchu na każde centrum danych, takie jak górny źródeł ruchu do centrum danych, sieci nieautoryzowanych top konwersację z centrum danych i górnej konwersację protokołów aplikacji.
    - Jeśli zauważysz większe obciążenie w centrum danych, można zaplanować dla dystrybucji wydajne ruchu.
    - Jeśli nieautoryzowany sieci są konwersację w centrum danych, następnie popraw reguły NSG można je zablokować.

    Wybierz **kliknij tutaj, aby wyświetlić na żywo geomap** w obszarze **ruchu dystrybucji między centrami danych Azure**, jak pokazano na poniższej ilustracji:

  ![Dystrybucja ruchu pokazujące pulpitu nawigacyjnego](media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geograficznie — Mapa pokazuje top wstążki do wyboru parametrów takich jak centra danych (włączone Analytics wdrożono/nr — wdrożenia/Active/Inactive/ruch/nie włączono analizy ruchu) i krajów, przyczyniając się Benign/złośliwym kodem ruchu aktywne Wdrożenie:

    ![Widoku mapy replikacji geograficznej — Warstwa prezentacji aktywnych wdrożeń](media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Mapa geograficznie pokazuje rozkład ruchu centrum danych z innych krajów i kontynentów i komunikacji z jej w niebieski (ruch niegroźne) i kolor czerwony (szkodliwy ruch) pokolorowane wierszy:

    ![Pokazujące Dystrybucja ruchu do innych krajów i kontynentów i widoku mapy Geo](media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Przepływ szczegóły ruchu dystrybucji podczas wyszukiwania dziennika](media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Wizualizuj Dystrybucja ruchu w sieci wirtualnych

**Szukać**

- Dystrybucja ruchu dla sieci wirtualnej, topologia, najwyższego źródeł ruch do sieci wirtualnej, sieci nieautoryzowanych top konwersację sieci wirtualnej i górnej konwersację protokołów aplikacji.
    - Znajomość sieci wirtualnej, który jest konwersację do sieci wirtualnej, który. Konwersacji nie jest oczekiwany, można rozwiązać.
    - Jeśli nieautoryzowany sieci są konwersację z sieci wirtualnej, można usunąć reguły NSG do blokowania sieci nieautoryzowany.
 
    Wybierz **kliknij tutaj, aby zobaczyć topologii ruchu w sieci wirtualnej** w obszarze **dystrybucji sieci wirtualnej**, jak pokazano na poniższej ilustracji: 

    ![Pulpit nawigacyjny pokazujące dystrybucji sieci wirtualnej](media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Topologię sieci wirtualnej pokazuje top wstążki wybór parametry, takie jak sieci wirtualnej (sieci wirtualnej między połączeń/Active/Inactive), połączeń zewnętrznych, aktywnych przepływów i złośliwych przepływów sieci wirtualnej.
- Topologię sieci wirtualnej przedstawia rozkład ruchu do sieci wirtualnej w odniesieniu do przepływów (dozwolonych/zablokowanych/ruchu przychodzącego/wychodzącego/Benign/złośliwym kodem), protokołu aplikacji i grup zabezpieczeń sieci, na przykład:

    ![Topologia sieci wirtualnej pokazujące szczegóły dystrybucji i przepływu ruchu](media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![Przepływ szczegóły dla sieci wirtualnej Dystrybucja ruchu w dzienniku wyszukiwania](media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Szukać**

- Dystrybucja ruchu na podsieci, topologia, top źródeł ruchu do podsieci, sieci top rouge konwersację podsieci i górnej konwersację protokołów aplikacji.
    - Wiedząc, które podsieci jest konwersację, do których podsieci. Jeśli widzisz nieoczekiwany konwersacji, można poprawić konfigurację.
    - Jeśli rouge sieci są konwersację z podsiecią, jest możliwość go poprawić, konfigurując reguły NSG na przykład blokowanie sieci nieautoryzowany.
- Topologia podsieci pokazuje top wstążki do wyboru parametry, takie jak podsieci, połączeń zewnętrznych, aktywnych przepływów i złośliwych przepływów podsieci Active/Inactive.
- Topologii podsieci przedstawia rozkład ruchu do sieci wirtualnej w odniesieniu do przepływów (dozwolonych/zablokowanych/ruchu przychodzącego/wychodzącego/Benign/złośliwym kodem), protokołu aplikacji i grup NSG, na przykład:

    ![Pokazujące Dystrybucja ruchu w podsieci sieci wirtualnej, w odniesieniu do przepływów topologii podsieci](media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Wyświetl portów i odbiera ruch z Internetu maszyny wirtualne

**Szukać**

- Otwórz porty są konwersację przez internet?
    - Jeśli nieoczekiwane portów znajdują się otwarty, można poprawić konfigurację:

        ![Pulpit nawigacyjny pokazujące porty odbieranie i wysyłanie ruch do Internetu](media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

        ![Szczegóły porty docelowe Azure i hostów](media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Szukać**

Czy masz szkodliwy ruch w danym środowisku? Gdzie jest pochodzące z? Gdzie jest przeznaczony do?

![Szczegóły przepływów szkodliwy ruch do wyszukiwania dziennika](media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>Wizualizuj trendy w trafień reguły NSG

**Szukać**

- Grupa NSG/reguły, które mają najwięcej trafień?
- Co to są top pary konwersacji źródłowego i docelowego na grupę NSG?

    ![Pulpit nawigacyjny pokazujące NSG trafienia statystyki](media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Następujące obrazy Pokaż podczas analizy trendów dla trafień reguły NSG i szczegóły przepływu źródłowego i docelowego dla grupy zabezpieczeń sieci:

    ![Czas pokazujące umożliwia analizę trendów trafień reguły NSG i górny reguły NSG](media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Górny NSG zasady statystyki szczegóły w dzienniku wyszukiwania](media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Aby uzyskać odpowiedzi na często zadawane pytania, zobacz [ruchu analytics — często zadawane pytania](traffic-analytics-faq.md).
