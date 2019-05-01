---
title: Analiza ruchu platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i analizowanie dzienników przepływów grupy zabezpieczeń sieci platformy Azure za pomocą analizy ruchu.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: yagup;kumud
ms.openlocfilehash: a4ae997398c85dc99af8711f1c6ce4e743592d73
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939893"
---
# <a name="traffic-analytics"></a>Analiza ruchu

Analiza ruchu jest oparta na chmurze rozwiązania, który zapewnia wgląd w aktywność użytkownika i aplikacji w sieciach w chmurze. Analiza ruchu analizuje usługi Network Watcher dzienniki sieciowych grup zabezpieczeń (NSG) przepływu udostępnienie szczegółowych informacji na przepływ ruchu w Twojej chmurze platformy Azure. Za pomocą analizy ruchu możesz wykonywać następujące czynności:

- Wizualizowanie działań sieciowych wszystkich subskrypcji platformy Azure i identyfikacji punktów aktywnych.
- Identyfikuj zagrożenia bezpieczeństwa dla i zabezpieczyć swoją sieć, za pomocą informacje, takie jak otwierać porty, aplikacji, próba dostępu do Internetu i maszyn wirtualnych (VM) nawiązywania połączenia z sieciami nieautoryzowane.
- Zrozumieć wzorce przepływu ruchu między regionami platformy Azure a Internetem, aby zoptymalizować wdrożenie sieci, wydajność i pojemność.
- Wykrywanie błędów konfiguracji sieci, co prowadzi do połączenia zakończone niepowodzeniem w sieci.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Dlaczego traffic analytics?

Ważne jest monitorowanie, zarządzanie i znasz sieci Niezrównana zabezpieczeń, zgodności i wydajności. Znajomość własnego środowiska jest sprawą Zabezpieczanie i optymalizowanie go. Często muszą znać bieżący stan sieci, który nawiązuje połączenie, gdzie one jest nawiązywane połączenie, które porty są otwarte dla Internetu, zachowanie oczekiwane sieci zachowanie nieregularne sieci i nagłego wzrostu ruchu.

Sieciach w chmurze są inne niż enterprise sieciami lokalnymi, w którym masz netflow lub równoważne protokołu zdolne do routery i przełączniki, które zapewniają możliwość zbierania ruchu sieciowego adresu IP, ponieważ wprowadza lub kończy pracę z interfejsem sieciowym. Analizując dane o przepływ ruchu, możesz tworzyć analizy ruchu sieciowego i woluminów.

Sieci wirtualne platformy Azure mają dzienników przepływu sieciowej grupy zabezpieczeń zawierające informacje o ruch przychodzący i wychodzący ruch IP za pomocą sieciowej grupy zabezpieczeń skojarzone z poszczególnymi interfejsami sieciowymi maszyn wirtualnych i podsieci. Analizowanie danych nieprzetworzonych dzienników przepływu sieciowych grup zabezpieczeń, a następnie wstawianie analizy zabezpieczeń, topologia i lokalizacji geograficznej, ruch analytics może dostarczyć szczegółowe informacje dotyczące przepływu ruchu w danym środowisku. Analiza ruchu oferuje informacji takich jak hosty najczęściej komunikujące się najczęściej komunikujące się protokołów aplikacji, najbardziej konwersacji pary hosta, dozwolone/zablokowane ruchu, ruchu przychodzącego/wychodzącego, otwieranie portów w Internecie, reguły blokowania najbardziej, ruchu Dystrybucja na centrum danych platformy Azure, sieci wirtualnej, podsieci, lub nieautoryzowane sieci.

## <a name="key-components"></a>Główne składniki

- **Sieciowa grupa zabezpieczeń (NSG)**: Zawiera listę reguł zabezpieczeń, które blokują lub zezwalają na ruch sieciowy do zasobów połączonych z siecią wirtualną platformy Azure. Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi (model klasyczny) lub poszczególnymi interfejsami sieciowymi (NIC) dołączonymi do maszyn wirtualnych (model usługi Resource Manager). Aby uzyskać więcej informacji, zobacz [omówienie grupy zabezpieczeń sieci](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Dzienników przepływu Sieciowej grupy zabezpieczeń sieci**: Zezwalaj na wyświetlanie informacji o przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń. Przepływu sieciowej grupy zabezpieczeń, dzienniki są zapisywane w formacie json i Pokaż przepływy wychodzące i przychodzące na podstawie reguły w poszczególnych kart Sieciowych przepływ ma zastosowanie do 5-elementowe spójne kolekcje informacji o przepływie (źródłowego i docelowego adresu IP, źródłowy i docelowy port i protokół) i czy był dozwolony ruch lub zabronione. Aby uzyskać więcej informacji na temat dzienników przepływu sieciowych grup zabezpieczeń, zobacz [dzienników przepływu sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Usługa Azure, która służy do zbierania danych monitorowania i przechowuje dane w centralnym repozytorium. Te dane mogą obejmować zdarzenia, dane dotyczące wydajności i niestandardowe dane dostarczane za pośrednictwem interfejsu API usługi Azure. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu. Monitorowanie aplikacji, takich jak analiza ruchu i monitora wydajności sieci są tworzone przy użyciu dzienników usługi Azure Monitor jako podstawa. Aby uzyskać więcej informacji, zobacz [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Zaloguj się obszar roboczy usługi Analytics**: Wystąpienie usługi Azure Monitor dzienników, w której są przechowywane dane odnoszących się do konta platformy Azure. Aby uzyskać więcej informacji na temat obszarów roboczych usługi Log Analytics, zobacz [Utwórz obszar roboczy usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: Regionalna usługa, która pozwala na monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci na platformie Azure. Można włączyć dzienniki sieciowych grup zabezpieczeń usługi flow włączać i wyłączać przy użyciu usługi Network Watcher. Aby uzyskać więcej informacji, zobacz [usługi Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Jak działa analizy ruchu

Analiza ruchu sprawdza nieprzetworzonych dzienników przepływu sieciowych grup zabezpieczeń i przechwytuje dzienniki obniżone przez agregowanie typowych przepływów między tego samego źródłowego adresu IP, docelowy adres IP, port docelowy i protokołu. Na przykład Host 1 (adres IP: . 10.10.10.10) komunikowania się z hostem 2 (adres IP: 10.10.20.10), 100 razy w okresie 1 godziny przy użyciu portu (np. 80) i protokół (na przykład http). Zmniejszenie dziennika ma jeden wpis, który Host 1 i 2 hosta przekazana do 100 razy w okresie 1 godziny przy użyciu portu *80* i protokół *HTTP*, zamiast pozycji 100. Zmniejszenie dzienniki są rozszerzone o lokalizacji geograficznej, bezpieczeństwa i informacje o topologii, a następnie zapisywane w obszarze roboczym usługi Log Analytics. Na poniższej ilustracji przedstawiono przepływ danych:

![Przepływ danych dla przetwarzania dzienników przepływu sieciowych grup zabezpieczeń](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Obsługiwane regiony

Korzystanie z analizy ruchu dla sieciowych grup zabezpieczeń w jednym z następujących obsługiwanych regionów:

* Kanada Środkowa
* Środkowo-zachodnie stany USA
* Wschodnie stany USA
* Wschodnie stany USA 2
* Środkowo-północne stany USA
* Środkowo-południowe stany USA
* Środkowe stany USA
* Zachodnie stany USA
* Zachodnie stany USA 2
* Francja Środkowa
* Europa Zachodnia
* Europa Północna
* Brazylia Południowa
* Zachodnie Zjednoczone Królestwo
* Południowe Zjednoczone Królestwo
* Australia Wschodnia
* Australia Południowo-Wschodnia
* Azja Wschodnia
* Azja Południowo-Wschodnia
* Korea Środkowa
* Indie Środkowe
* Indie Południowe
* Japonia Wschodnia 
* Japonia Zachodnia
* Administracja USA — Wirginia

Obszar roboczy usługi Log Analytics muszą znajdować się w następujących regionach:
* Kanada Środkowa
* Środkowo-zachodnie stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA
* Francja Środkowa
* Europa Zachodnia
* Południowe Zjednoczone Królestwo
* Australia Południowo-Wschodnia
* Azja Południowo-Wschodnia
* Korea Środkowa
* Indie Środkowe
* Japonia Wschodnia
* Administracja USA — Wirginia

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="user-access-requirements"></a>Wymagania dotyczące dostępu użytkowników

Twoje konto musi należeć do jednej z następujących Azure [wbudowane role](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Model wdrażania   | Rola                   |
|---------          |---------               |
|Resource Manager   | Właściciel                  |
|                   | Współautor            |
|                   | Czytelnik                 |
|                   | Współautor sieci    |

Jeśli Twoje konto nie jest przypisana do jednego z wbudowanych ról, musi ona zostać przypisana do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) przypisany następujące działania, na poziomie subskrypcji:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

Aby uzyskać informacji na temat sposobu sprawdzania uprawnień dostępu użytkowników, zobacz [Traffic analytics — często zadawane pytania](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

W celu analizy ruchu, musisz mieć istniejącej usługi network watcher, lub [Włącz usługę network watcher](network-watcher-create.md) w każdym regionie, czy masz sieciowych grup zabezpieczeń, które mają być analizowane ruchu dla. Analiza ruchu może zostać włączona dla sieciowych grup zabezpieczeń hostowanych w dowolnym [obsługiwane regiony](#supported-regions).

### <a name="select-a-network-security-group"></a>Wybierz grupę zabezpieczeń sieci

Przed włączeniem przepływu sieciowej grupy zabezpieczeń, rejestrowanie, konieczne jest posiadanie sieciową grupę zabezpieczeń do logowania przepływów. Jeśli nie masz sieciowej grupy zabezpieczeń, zobacz [Utwórz sieciową grupę zabezpieczeń](../virtual-network/manage-network-security-group.md#create-a-network-security-group) ją utworzyć.

Po lewej stronie witryny Azure portal, wybierz **Monitor**, następnie **usługi Network watcher**, a następnie wybierz pozycję **dzienników przepływu sieciowych grup zabezpieczeń**. Wybierz sieciową grupę zabezpieczeń, który chcesz włączyć dzienników przepływu sieciowej grupy zabezpieczeń, jak pokazano na poniższej ilustracji:

![Wybór sieciowych grup zabezpieczeń, które wymagają włączenia dzienników przepływu sieciowej grupy zabezpieczeń](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Jeśli zostanie podjęta próba włączenia usługi analiza ruchu dla sieciowych grup zabezpieczeń, który znajduje się w dowolnym regionie innych niż [obsługiwane regiony](#supported-regions), komunikat o błędzie "Nie znaleziono".

## <a name="enable-flow-log-settings"></a>Włącz ustawienia dziennika przepływu

Przed włączeniem ustawienia dziennika przepływu, należy wykonać następujące zadania:

Jeśli jeszcze nie jest zarejestrowany dla Twojej subskrypcji, należy zarejestrować dostawcę usługi Azure Insights:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Jeśli nie masz jeszcze loguje się konta usługi Azure Storage do przechowywania przepływu sieciowej grupy zabezpieczeń, musisz utworzyć konto magazynu. Za pomocą poniższego polecenia, można utworzyć konta magazynu. Przed uruchomieniem poleceń, Zastąp `<replace-with-your-unique-storage-account-name>` nazwą, która jest unikatowa dla wszystkich lokalizacji platformy Azure od 3 do 24 znaków długości, przy użyciu tylko cyfry i małe litery. Jeśli to konieczne, można również zmienić nazwę grupy zasobów.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Wybierz następujące opcje, jak pokazano na ilustracji:

1. Wybierz *na* dla **stanu**
2. Wybierz *w wersji 2* dla **wersji dzienników przepływu**. W wersji 2 zawiera statystyki sesji przepływu (bajtów i pakietów)
3. Wybierz istniejące konto magazynu do przechowywania dzienników przepływów na platformie. Jeśli chcesz przechowywać dane w nieskończoność, ustaw wartość *0*. Powoduje naliczenie opłat za magazyn Azure dla konta magazynu.
4. Ustaw **przechowywania** do liczby dni, które mają być przechowywane dane.
5. Wybierz *na* dla **stan analizy ruchu**.
6. Wybierz istniejący obszar roboczy usługi Log Analytics (OMS) lub **Utwórz nowy obszar roboczy** Aby utworzyć nową. Analiza ruchu służy obszar roboczy usługi Log Analytics do przechowywania danych zagregowanych i indeksowane, który jest następnie używany do generowania analizami. Jeśli wybierzesz istniejącego obszaru roboczego, musi istnieć w jednym z [obsługiwane regiony](#supported-regions) i zostały uaktualnione do nowego języka zapytań. Jeśli nie chcesz, aby uaktualnić istniejący obszar roboczy lub w obsługiwanym regionie nie ma obszaru roboczego, Utwórz nową. Aby uzyskać więcej informacji na temat język zapytań, zobacz [usługi Azure Log Analytics, uaktualnienie do nową funkcją przeszukiwania dzienników](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    Obszaru roboczego analizy dzienników hostingu rozwiązanie do analizy ruchu i sieciowe grupy zabezpieczeń nie muszą znajdować się w tym samym regionie. Na przykład można mieć sieciowych grup zabezpieczeń w regionie wschodnie stany USA i zachodnie stany USA, w obszarze roboczym w regionie Europa Zachodnia, może być analizy ruchu. Wiele sieciowych grup zabezpieczeń można skonfigurować w tym samym obszarze roboczym.
7. Wybierz pozycję **Zapisz**.

    ![Wybór konta magazynu, obszar roboczy usługi Log Analytics i włączanie analizy ruchu](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement-nsg-flowlogs-v2.png)

Powtórz poprzednie kroki dla innych NSG, dla których chcesz włączyć analizy ruchu. Dane z dzienników przepływu są wysyłane do obszaru roboczego, dlatego upewnij się, że lokalnymi przepisami i regulacjami w kraju/regionu na przechowywanie danych w regionie, w którym istnieje obszar roboczy.

Można również skonfigurować za pomocą analizy ruchu [AzNetworkWatcherConfigFlowLog zestaw](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) polecenia cmdlet programu PowerShell w programie Azure PowerShell. Uruchom `Get-Module -ListAvailable Az` można odnaleźć zainstalowanej wersji. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Wyświetl analizę ruchu

Na stronie po lewej stronie portalu, wybierz **wszystkich usług**, wprowadź *Monitor* w **filtru** pole. Gdy **Monitor** pojawi się w wynikach wyszukiwania, wybierz ją. Aby rozpocząć eksplorację, analizę ruchu i jego możliwości, wybierz opcję **usługi Network watcher**, następnie **analizy ruchu**.

![Uzyskiwanie dostępu do pulpitu nawigacyjnego analizy ruchu](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Pulpit nawigacyjny może potrwać do 30 minut pojawi się po raz pierwszy, ponieważ analiza ruchu musi najpierw agregacji wystarczającej ilości danych dla niego do uzyskania istotnych informacji, zanim może generować raportów.

## <a name="usage-scenarios"></a>Scenariusze użycia

Niektóre szczegółowe informacje, które możesz chcieć uzyskać po w pełni jest skonfigurowana analiza ruchu, są następujące:

### <a name="find-traffic-hotspots"></a>Znajdowanie ruchu hotspotami

**Szukać**

- Które hostów, podsieci i sieci wirtualne są wysyłania lub odbierania ruchu związanego z większości, przechodzenie przez maksymalne złośliwy ruch i blokuje znaczące przepływów?
    - Sprawdź porównawczych wykresu dla hostów, podsieci i sieci wirtualnej. Opis hosta, którego, podsieciami i sieciami wirtualnymi jest wysyłany lub odbieranie ruchu w najbardziej może pomóc w zidentyfikowaniu hosty, które są przetwarzania najbardziej ruchu, i czy Dystrybucja ruchu odbywa się poprawnie.
    - Możesz ocenić, jeśli wielkość ruchu sieciowego jest odpowiedni dla hosta. Jest ilość ruchu normalnego zachowania lub on że opiszemy je dalszego dochodzenia?
- Ilość ruchu przychodzącego/wychodzącego jest?
    -   Host oczekuje się, aby otrzymać więcej ruchu przychodzącego ruchu sieciowego niż wychodzących i odwrotnie?
- Statystyka zablokowanego ruchu.
    - Dlaczego hoście blokuje znacząca ilość nieszkodliwy ruch? To zachowanie wymaga dalszych badań, prawdopodobnie optymalizacji konfiguracji
- Statystyki złośliwy ruch dozwolone/zablokowane
  - Dlaczego host odbiera złośliwy ruch i dlaczego jest dozwolone przepływy z złośliwego źródła? To zachowanie wymaga dalszego dochodzenia, prawdopodobnie optymalizacji konfiguracji.

    Wybierz **holograficznych**w obszarze **hosta**, jak pokazano na poniższej ilustracji:

    ![Pulpit nawigacyjny zaprezentować hosta z większości szczegóły ruchu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Na poniższej ilustracji przedstawiono czas na popularności najważniejsze pięcioma hostami talking oraz informacje związane z przepływem (dozwolone — przychodzącego/wychodzącego i odmowy - przychodzącego/wychodzącego przepływu) dla hosta:

    ![Pięć mówić większość hosta trendu](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Szukać**

- Które są najbardziej konwersacji pary hosta?
    - Oczekiwane zachowanie, takie jak komunikacja frontonu lub zaplecza lub nieregularne zachowanie, takie jak ruch internetowy serwer zaplecza.
- Statystyki dozwolone/zablokowane ruchu
    - Dlaczego zezwalanie lub blokowanie natężeniem ruchu znaczące hosta
- Najczęściej używany protokół aplikacji wśród większości konwersacji pary hosta:
    - Czy aplikacje te są dozwolone w tej sieci?
    - Aplikacje są skonfigurowane prawidłowo? Są one przy użyciu odpowiedniego protokołu komunikacji? Wybierz **holograficznych** w obszarze **częste odzyskiwanie pamięci konwersacji**, co zostało przedstawione na poniższej ilustracji:

        ![Pulpit nawigacyjny, w którym przedstawiane są najczęściej konwersacji](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Na poniższej ilustracji przedstawiono czas na popularności dla najważniejsze konwersacje pięć i szczegóły związane z przepływem, takich jak dozwolonych i niedozwolonych przepływy ruchu przychodzącego i wychodzącego dla pary konwersacji:

    ![Pierwszych 5 trendów i szczegóły dotyczące intensywnych konwersacji](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Szukać**

- Protokół aplikacji, które jest najczęściej używane w danym środowisku i które konwersacji pary hosta korzysta z protokołu aplikacji najbardziej?
    - Czy aplikacje te są dozwolone w tej sieci?
    - Aplikacje są skonfigurowane prawidłowo? Są one przy użyciu odpowiedniego protokołu komunikacji? Oczekiwane zachowanie jest typowe porty, takie jak 80 i 443. Komunikacji standardowa Jeśli nie są wyświetlane wszelkie nietypowe porty, mogą wymagać zmian w konfiguracji. Wybierz **holograficznych** w obszarze **port aplikacji**, na poniższej ilustracji:

        ![Pulpit nawigacyjny, w którym przedstawiane są najczęściej używane protokoły aplikacji](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Następujące obrazy Pokaż czas popularne protokoły L7 pięć i szczegóły dotyczące przepływu (na przykład dozwolonych i niedozwolonych przepływy do niego dostępu) na protokół L7:

    ![Pięć najważniejszych warstwy 7 szczegóły protokołów i trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Szczegóły protokołu aplikacji podczas wyszukiwania dziennika przepływu](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Szukać**

- Trendy wykorzystania pojemności bramy sieci VPN w danym środowisku.
    - Każda jednostka SKU sieci VPN umożliwia pewna ilość przepustowości. Czy bram sieci VPN skutkowało niewystarczającym wykorzystaniem?
    - Bram osiągają wydajność? Należy uaktualnić do następnej wyższej wersji jednostki SKU?
- Hosty najczęściej konwersacji, za pomocą których bramy sieci VPN, które są przez port, który?
    - Ten wzorzec jest normalne? Wybierz **holograficznych** w obszarze **bramy sieci VPN**, jak pokazano na poniższej ilustracji:

        ![Pulpit nawigacyjny, którym przedstawiane są najważniejsze aktywne połączenia sieci VPN](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Na poniższej ilustracji przedstawiono czas analizy trendów wykorzystania pojemności bramy Azure VPN Gateway i szczegóły dotyczące przepływu (na przykład z dozwolonymi przepływami i porty):

    ![Sieci VPN bramy trendów i przepływ szczegóły wykorzystania](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Wizualizuj Dystrybucja ruchu według lokalizacji geograficznej

**Szukać**

- Dystrybucja ruchu w centrum danych, takie jak najważniejsze źródła ruchu do centrum danych, sieci nieautoryzowany najważniejsze konwersację z centrum danych a górnej konwersację protokołów aplikacji.
  - Jeśli zauważysz większe obciążenie w centrum danych, możesz zaplanować do dystrybucji ruchu wydajne.
  - Jeśli nieautoryzowany sieci są konwersację w centrum danych, następnie Rozwiąż reguły sieciowej grupy zabezpieczeń, aby je zablokować.

    Wybierz **wyświetlanie mapy** w obszarze **środowiska**, jak pokazano na poniższej ilustracji:

    ![Prezentacja Dystrybucja ruchu pulpitu nawigacyjnego](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Mapy geograficznej przedstawiono najważniejsze wstążki do wyboru parametrów, takich jak centra danych (obsługujące analizę wdrożono/No wdrożenia/aktywny/nieaktywny/ruchu/nie obsługujące analizę ruchu) i kraje/regiony, w których pochodzi ruch Benign/złośliwym kodem do aktywnej Wdrożenie:

    ![Widok mapy geograficznej przedstawiająca aktywne wdrożenie](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Mapy geograficznej pokazuje dystrybucji ruchu do centrum danych z krajów/regionów i kontynentach komunikowania się go w niebieska (nieszkodliwy ruch) i czerwona (ruch złośliwego) kolorowe wierszy:

    ![Zaprezentować rozkładania ruchu kraje/regiony i kontynentach widoku mapy geograficznej](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Szczegóły dla dystrybucji ruchu na wyszukiwanie w dziennikach przepływu](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Wizualizuj Dystrybucja ruchu przez sieci wirtualnych

**Szukać**

- Dystrybucja ruchu na sieć wirtualną, topologia, najważniejsze źródła ruchu w sieci wirtualnej, sieci nieautoryzowany najważniejsze konwersację do sieci wirtualnej i górnym konwersację protokołów aplikacji.
  - Wiedząc, sieć wirtualną, która jest konwersację na sieć wirtualną, która. Konwersacji nie oczekuje się, można rozwiązać.
  - Jeśli nieautoryzowany sieci są konwersację z wirtualnych sieci, można rozwiązać reguły sieciowej grupy zabezpieczeń, aby zablokować sieci nieautoryzowany.
 
    Wybierz **sieci wirtualne widoku** w obszarze **środowiska**, jak pokazano na poniższej ilustracji:

    ![Pulpit nawigacyjny, w którym przedstawiane są dystrybucja sieci wirtualnej](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Topologię sieci wirtualnej przedstawiono najważniejsze wstążce wybór parametrów, takich jak sieci wirtualnej (Vnet sieci wirtualnej połączeń/Active/Inactive), połączenia zewnętrzne, aktywne przepływy i złośliwe przepływy sieci wirtualnej.
- Można filtrować wirtualnego topologią i konfiguracją sieci na podstawie subskrypcji, obszarów roboczych, grupy zasobów i interwał czasu. Dodatkowe filtry, które pomagają zrozumieć, czy przepływ: Przepływ typu (między sieciami wirtualnymi, IntraVNET itd.), kierunek przepływu (przychodzące, wychodzące), stan przepływu (dozwolonych, zablokowanych), sieci wirtualne (docelowe i połączone), typ połączenia (komunikacji równorzędnej lub brama - P2S i S2S) i sieciowej grupy zabezpieczeń. Te filtry umożliwiają skupić się na sieci wirtualnych, które chcesz zbadać szczegółowo.
- Topologię sieci wirtualnej przedstawia Dystrybucja ruchu w sieci wirtualnej w odniesieniu do przepływów (dozwolone/zablokowane/ruchu przychodzącego/ruchu wychodzącego/Benign/złośliwym kodem), protokół aplikacji i sieciowych grup zabezpieczeń, na przykład:

    ![Topologii sieci wirtualnej, na którym przedstawiane są szczegóły dystrybucji i przepływ ruchu](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologii sieci wirtualnej, na którym przedstawiane są najwyższego poziomu i więcej filtrów](./media/traffic-analytics/virtual-network-filters.png)

    ![Szczegóły do dystrybucji ruchu w sieci wirtualnej podczas wyszukiwania dziennika przepływu](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Szukać**

- Ruch dystrybucji na podsieci, topologia, najważniejsze źródła ruch do podsieci, sieci nieautoryzowany najważniejsze konwersację do podsieci, a następnie górnej konwersację protokołów aplikacji.
    - Wiedząc, podsieć, która jest konwersację do której podsieci. Jeśli widzisz nieoczekiwany rozmowy, możesz poprawić konfigurację.
    - Jeśli nieautoryzowany sieci są konwersację z podsiecią, jesteś w stanie go poprawić, konfigurując reguły sieciowej grupy zabezpieczeń, aby zablokować sieci nieautoryzowany.
- Topologia podsieci przedstawiono najważniejsze wstążki do wyboru parametry, takie jak podsieci Active/Inactive, połączeń zewnętrznych, aktywne przepływy i złośliwe przepływy podsieci.
- Topologia podsieci przedstawia Dystrybucja ruchu w sieci wirtualnej w odniesieniu do przepływów (dozwolone/zablokowane/ruchu przychodzącego/ruchu wychodzącego/Benign/złośliwym kodem), protokół aplikacji i sieciowe grupy zabezpieczeń, na przykład:

    ![Topologia podsieci, w którym przedstawiane są Dystrybucja ruchu w podsieci sieci wirtualnej w odniesieniu do przepływów](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Szukać**

Dystrybucja ruchu dla bramy Application gateway i modułu równoważenia obciążenia, topologia, najważniejsze źródła ruchu z górnego nieautoryzowane sieci konwersację bramy Application gateway i modułu równoważenia obciążenia i pierwszych konwersację protokołów aplikacji. 
    
 - Wiedząc, podsieć, która jest konwersację, do którego usługa Application gateway lub moduł równoważenia obciążenia. Jeśli zauważysz nieoczekiwany konwersacji, możesz poprawić konfigurację.
 - Jeśli nieautoryzowany sieci są konwersację z bramy aplikacji lub usługi równoważenia obciążenia, jesteś w stanie go poprawić, konfigurując reguły sieciowej grupy zabezpieczeń, aby zablokować sieci nieautoryzowany. 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Wyświetlanie portów i maszyny wirtualne odbierające ruch z Internetu

**Szukać**

- Otwórz porty są konwersację za pośrednictwem Internetu?
  - Jeśli porty nieoczekiwany zostaną znalezione open, można poprawić konfigurację:

    ![Pulpit nawigacyjny którym przedstawiane są porty, odbierania i wysyłania ruchu do Internetu](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Szczegóły dotyczące portów docelowych platformy Azure i hostów](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Szukać**

Czy masz złośliwy ruch w środowisku? Gdzie jest ona pochodzące z komputera? Gdzie jest kierowany do?

![Złośliwy ruch przepływa szczegółowo przeszukiwania dzienników](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Wizualizowanie trendów w trafień reguł sieciowych grup zabezpieczeń lub grupę zabezpieczeń sieci

**Szukać**

- Reguły sieciowej grupy zabezpieczeń lub grupę zabezpieczeń sieci ma większość trafień na wykresie porównawczych dystrybucja przepływów?
- Jakie są najważniejsze pary konwersacji źródłowy i docelowy na reguły sieciowej grupy zabezpieczeń lub grupę zabezpieczeń sieci?

    ![Pulpit nawigacyjny sieciowej grupy zabezpieczeń przedstawiające trafienia statystyki](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Następujące obrazy Pokaż czas Trend dla trafień reguł sieciowych grup zabezpieczeń i szczegóły przepływu źródłowego i docelowego dla sieciowej grupy zabezpieczeń:

  - Szybkie wykrycie sieciowej grupy zabezpieczeń i sieciowe grupy zabezpieczeń, które zasady są przechodzenie przez złośliwe przepływy i które są najważniejsze złośliwy adres IP pozwalająca też sprostać dostęp do środowiska chmury
  - Określenie, które reguły sieciowej grupy zabezpieczeń lub grupę zabezpieczeń umożliwiające/blokują ruch sieciowy znaczne
  - Wybierz opcję top filtry dla szczegółowej kontroli sieciowej grupy zabezpieczeń lub sieciowej grupy zabezpieczeń reguły

    ![Prezentacja czas na popularności trafienia reguł sieciowych grup zabezpieczeń i najważniejsze reguły sieciowej grupy zabezpieczeń](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Najważniejsze sieciowej grupy zabezpieczeń reguły szczegóły statystyk podczas wyszukiwania dziennika](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Aby uzyskać odpowiedzi na często zadawane pytania, zobacz [Traffic analytics — często zadawane pytania](traffic-analytics-faq.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak włączyć dzienniki przepływu, zobacz [rejestrowanie przepływu sieciowej grupy zabezpieczeń z włączeniem](network-watcher-nsg-flow-logging-portal.md).
- Aby zrozumieć schemat i przetwarzania informacji analizy ruchu, zobacz [Traffic analytics schematu](traffic-analytics-schema.md).
