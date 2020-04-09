---
title: Analiza ruchu na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak analizować dzienniki przepływu grupy zabezpieczeń sieci platformy Azure za pomocą analizy ruchu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: damendo
ms.reviewer: vinigam
ms.openlocfilehash: adba282a96f9d250569e090e186859c04e89ebda
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981549"
---
# <a name="traffic-analytics"></a>Analiza ruchu

Usługa Traffic Analytics to rozwiązanie oparte na chmurze, które zapewnia wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Analiza ruchu analizuje dzienniki przepływu sieciowej grupy zabezpieczeń obserwatora sieci (Network Watcher), aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Dzięki analizie ruchu drogowego możesz:

- Wizualizuj aktywność sieciową w ramach subskrypcji platformy Azure i identyfikuj punkty wyszukiwania.
- Identyfikowanie zagrożeń bezpieczeństwa i zabezpieczanie sieci za pomocą informacji, takich jak otwarte porty, aplikacje próbujące uzyskać dostęp do Internetu i maszyny wirtualne (VM) łączące się z nieautoryzowanymi sieciami.
- Poznaj wzorce przepływu ruchu w regionach platformy Azure i w Internecie, aby zoptymalizować wdrożenie sieci pod kątem wydajności i pojemności.
- Należy wskazać błędy konfiguracji sieci prowadzące do nieudanych połączeń w sieci.

> [!NOTE]
> Usługa Traffic Analytics obsługuje teraz zbieranie danych dzienników przepływu nsg z większą częstotliwością 10 minut

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Dlaczego analiza ruchu?

Ważne jest, aby monitorować, zarządzać i znać własną sieć pod kątem bezkompromisowych zabezpieczeń, zgodności i wydajności. Znajomość własnego środowiska ma ogromne znaczenie dla jego ochrony i optymalizacji. Często musisz znać bieżący stan sieci, kto łączy się, z którego się łączą, które porty są otwarte dla Internetu, oczekiwane zachowanie sieci, nieregularne zachowanie sieci i nagły wzrost ruchu.

Sieci w chmurze różnią się od lokalnych sieci przedsiębiorstw, w których występują routery i przełączniki obsługujące przepływ netto lub równoważne protokoły, które umożliwiają zbieranie ruchu sieciowego IP w miarę wprowadzania lub zamykania interfejsu sieciowego. Analizując dane przepływu ruchu, można utworzyć analizę przepływu ruchu sieciowego i woluminu.

Sieci wirtualne platformy Azure mają dzienniki przepływu sieciowej grupy zabezpieczeń sieciowych, które zawierają informacje o ruchu ip transferu danych przychodzących i wychodzących za pośrednictwem sieciowej grupy zabezpieczeń skojarzonej z poszczególnymi interfejsami sieciowymi, maszynami wirtualnymi lub podsieciami. Analizując surowe dzienniki przepływu nsg i wstawiając analizy zabezpieczeń, topologii i geografii, analizy ruchu może zapewnić wgląd w przepływ ruchu w środowisku. Usługa Traffic Analytics udostępnia informacje, takie jak większość komunikujących się hostów, większość komunikujących się protokołów aplikacji, większość par hostów konwersujących, dozwolony/zablokowany ruch, ruch przychodzący/wychodzący, otwarte porty internetowe, większość reguł blokowania, dystrybucja ruchu na centrum danych platformy Azure, sieć wirtualna, podsieci lub nieautoryzowane sieci.

## <a name="key-components"></a>Główne składniki

- **Sieciowa grupa zabezpieczeń (NSG)**: Zawiera listę reguł zabezpieczeń, które zezwalają lub odmawiają ruchu sieciowego zasobom połączonym z siecią wirtualną platformy Azure. Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi (model klasyczny) lub poszczególnymi interfejsami sieciowymi (NIC) dołączonymi do maszyn wirtualnych (model usługi Resource Manager). Aby uzyskać więcej informacji, zobacz [Omówienie sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Dzienniki przepływu sieciowej grupy zabezpieczeń (NSG):** Umożliwia wyświetlanie informacji o ruchu IP przychodzących i wychodzących za pośrednictwem sieciowej grupy zabezpieczeń. Dzienniki przepływu sieciowej sieciowej są zapisywane w formacie json i pokazują przepływy wychodzące i przychodzące na podstawie reguły, karty sieciowej, do których ma zastosowanie przepływ, informacje o przepływie pięciu krotek (źródłowy/docelowy adres IP, port źródłowy/docelowy i protokół) oraz jeśli ruch był dozwolony lub odrzucony. Aby uzyskać więcej informacji na temat dzienników przepływu nsg, zobacz [dzienniki przepływu nsg](network-watcher-nsg-flow-logging-overview.md).
- **Usługa Log Analytics:** usługa platformy Azure, która zbiera dane monitorowania i przechowuje dane w centralnym repozytorium. Te dane mogą obejmować zdarzenia, dane dotyczące wydajności lub dane niestandardowe dostarczane za pośrednictwem interfejsu API platformy Azure. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu. Aplikacje do monitorowania, takie jak monitor wydajności sieci i analizy ruchu są tworzone przy użyciu dzienników usługi Azure Monitor jako podstawy. Aby uzyskać więcej informacji, zobacz [Dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Obszar roboczy usługi Log Analytics:** jest przechowywane wystąpienie dzienników usługi Azure Monitor, w którym przechowywane są dane dotyczące konta platformy Azure. Aby uzyskać więcej informacji na temat obszarów roboczych usługi Log Analytics, zobacz [Tworzenie obszaru roboczego usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Kontroler sieci:** usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci na platformie Azure. Dzienniki przepływu sieciowej sieciowej można włączać i wyłączać za pomocą obserwatora sieciowego. Aby uzyskać więcej informacji, zobacz [Kontrola sieci](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Jak działa analityka ruchu

Analiza ruchu sprawdza nieprzetworzone dzienniki przepływu sieciowej sieciowej sieciowej i przechwytuje zmniejszone dzienniki, agregując typowe przepływy między tym samym źródłowym adresem IP, docelowym adresem IP, portem docelowym i protokołem. Na przykład host 1 (adres IP: 10.10.10.10) komunikujący się z hostem 2 (adres IP: 10.10.20.10), 100 razy w okresie 1 godziny przy użyciu portu (na przykład 80) i protokołu (na przykład http). Zredukowany dziennik ma jeden wpis, który Host 1 & Host 2 komunikował 100 razy w ciągu 1 godziny przy użyciu portu *80* i protokołu *HTTP*, zamiast 100 wpisów. Zmniejszone dzienniki są wzbogacone o informacje o lokalizacji geograficznej, zabezpieczeń i topologii, a następnie przechowywane w obszarze roboczym usługi Log Analytics. Poniższy obraz przedstawia przepływ danych:

![Przepływ danych do przetwarzania dzienników przepływu nsg](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Obsługiwane regiony: NSG 

Można użyć analizy ruchu dla grup zabezpieczeń w dowolnym z następujących obsługiwanych regionów:

* Kanada Środkowa
* Zachodnio-środkowe stany USA
* Wschodnie stany USA
* Wschodnie stany USA 2
* Północno-środkowe stany USA
* Południowo-środkowe stany USA
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
* US Gov Wirginia
* Chiny Wschodnie 2

## <a name="supported-regions-log-analytics-workspaces"></a>Obsługiwane regiony: obszary robocze analizy dzienników

Obszar roboczy usługi Log Analytics musi istnieć w następujących regionach:
* Kanada Środkowa
* Zachodnio-środkowe stany USA
* Wschodnie stany USA
* Wschodnie stany USA 2
* Północno-środkowe stany USA
* Południowo-środkowe stany USA
* Środkowe stany USA
* Zachodnie stany USA
* Zachodnie stany USA 2
* Środkowe stany USA
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
* Japonia Wschodnia
* US Gov Wirginia
* Chiny Wschodnie 2

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="user-access-requirements"></a>Wymagania dotyczące dostępu użytkowników

Twoje konto musi być członkiem jednej z następujących [wbudowanych ról](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)platformy Azure:

|Model wdrażania   | Rola                   |
|---------          |---------               |
|Resource Manager   | Właściciel                  |
|                   | Współautor            |
|                   | Czytelnik                 |
|                   | Współautor sieci    |

Jeśli twoje konto nie jest przypisane do jednej z wbudowanych ról, musi być przypisane do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) która jest przypisana do następujących akcji, na poziomie subskrypcji:

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
- "Microsoft.Network/expressRouteCircuits/read"

Aby uzyskać informacje na temat sprawdzania uprawnień dostępu użytkowników, zobacz [Często zadawane pytania dotyczące analizy ruchu](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Aby analizować ruch, musisz mieć istniejącego obserwatora sieci lub [włączyć obserwatora sieci](network-watcher-create.md) w każdym regionie, dla którego masz sieciowe sieciowe sieciowe, dla których chcesz analizować ruch. Analiza ruchu można włączyć dla grup zabezpieczeń hostowanych w dowolnym z [obsługiwanych regionów.](#supported-regions-nsg)

### <a name="select-a-network-security-group"></a>Wybieranie sieciowej grupy zabezpieczeń

Przed włączeniem rejestrowania przepływu sieciowej grupy zabezpieczeń musi istnieć sieciowa grupa zabezpieczeń do rejestrowania przepływów. Jeśli nie masz sieciowej grupy zabezpieczeń, zobacz [Tworzenie sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md#create-a-network-security-group) w celu jej utworzenia.

W witrynie Azure portal przejdź do **usługi Network watcher**, a następnie wybierz pozycję **Dzienniki przepływu sieciowej sieciowej**. Wybierz grupę zabezpieczeń sieci, dla której chcesz włączyć dziennik przepływu sieciowej grupy zabezpieczeń, jak pokazano na poniższej ilustracji:

![Wybór nsg, które wymagają włączenia dziennika przepływu NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Jeśli spróbujesz włączyć analizę ruchu dla śwolżej, która jest hostowana w dowolnym regionie innym niż [obsługiwane regiony,](#supported-regions-nsg)zostanie wyświetlony błąd "Nie znaleziono".

## <a name="enable-flow-log-settings"></a>Włączanie ustawień dziennika przepływu

Przed włączeniem ustawień dziennika przepływu należy wykonać następujące zadania:

Zarejestruj dostawcę usługi Azure Insights, jeśli nie jest on jeszcze zarejestrowany dla subskrypcji:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Jeśli nie masz jeszcze konta usługi Azure Storage do przechowywania dzienników przepływu nsg, należy utworzyć konto magazynu. Konto magazynu można utworzyć za pomocą następującego polecenia. Przed uruchomieniem polecenia `<replace-with-your-unique-storage-account-name>` zastąp nazwą, która jest unikatowa we wszystkich lokalizacjach platformy Azure o długości od 3 do 24 znaków, używając tylko cyfr i mniejszych liter. W razie potrzeby można również zmienić nazwę grupy zasobów.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Wybierz następujące opcje, jak pokazano na rysunku:

1. Wybierz *włącz* dla **stanu**
2. Wybierz *wersję 2* dla **wersji dzienników przepływu**. Wersja 2 zawiera statystyki sesji przepływu (bajty i pakiety)
3. Wybierz istniejące konto magazynu do przechowywania dzienników przepływu. Upewnij się, że magazyn nie ma ustawionej wartości true "Data Lake Storage Gen2 Hierarchiical Namespace enabled".
4. Ustaw **retencję** na liczbę dni, dla których chcesz przechowywać dane. Jeśli chcesz przechowywać dane na zawsze, ustaw wartość *0*. Opłaty usługi Azure Storage za konto magazynu. 
5. Wybierz *pozycję Wł.* **Dla stanu analizy ruchu**.
6. Wybierz interwał przetwarzania. Zgodnie z wybranym wyborem dzienniki przepływu będą zbierane z konta magazynu i przetwarzane przez Traffic Analytics. Możesz wybrać interwał przetwarzania co 1 godzinę lub co 10 minut. 
7. Wybierz istniejący obszar roboczy usługi Log Analytics (OMS) lub wybierz pozycję **Utwórz nowy obszar roboczy,** aby utworzyć nowy obszar roboczy. Obszar roboczy usługi Log Analytics jest używany przez usługi Traffic Analytics do przechowywania zagregowanych i indeksowanych danych, które są następnie używane do generowania analizy. Jeśli wybierzesz istniejący obszar roboczy, musi on istnieć w jednym z [obsługiwanych regionów](#supported-regions-log-analytics-workspaces) i został uaktualniony do nowego języka zapytań. Jeśli nie chcesz uaktualnić istniejącego obszaru roboczego lub nie masz obszaru roboczego w obsługiwanym regionie, utwórz nowy. Aby uzyskać więcej informacji na temat języków zapytań, zobacz [Uaktualnienie usługi Azure Log Analytics do nowego wyszukiwania dzienników](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Obszar roboczy analizy dzienników obsługujący rozwiązanie analizy ruchu i grup zabezpieczeń nie muszą znajdować się w tym samym regionie. Na przykład może mieć analizy ruchu w obszarze roboczym w regionie Europa Zachodnia, podczas gdy może być nsgs we wschodnich stanach USA i zachodnich stanach USA. Wiele grup zabezpieczeń można skonfigurować w tym samym obszarze roboczym.

8. Wybierz **pozycję Zapisz**.

    ![Wybór konta magazynu, obszaru roboczego usługi Log Analytics i włączenia usługi Traffic Analytics](./media/traffic-analytics/ta-customprocessinginterval.png)

Powtórz poprzednie kroki dla innych grup zabezpieczeń, dla których chcesz włączyć analizę ruchu. Dane z dzienników przepływu są wysyłane do obszaru roboczego, dzięki czemu lokalne przepisy i regulacje w danym kraju zezwalają na przechowywanie danych w regionie, w którym istnieje obszar roboczy. Jeśli ustawiono różne interwały przetwarzania dla różnych grup zabezpieczeń, dane będą zbierane w różnych odstępach czasu. Na przykład: Można włączyć interwał przetwarzania wynoszący 10 minut dla krytycznych wirtualnych sieci wirtualnych i 1 godzinę dla niekrytycznych wirtualnych sieci wirtualnych.

Można również skonfigurować analizy ruchu przy użyciu polecenia cmdlet [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell w programie Azure PowerShell. Uruchom, `Get-Module -ListAvailable Az` aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Wyświetlanie analizy ruchu

Aby wyświetlić analizę ruchu, wyszukaj **obserwatora sieci** na pasku wyszukiwania portalu. Po wejściu do obserwatora sieci, aby zapoznać się z analizą ruchu i jego możliwościami, wybierz **analizę ruchu** z lewego menu. 

![Uzyskiwanie dostępu do pulpitu nawigacyjnego analizy ruchu](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Pulpit nawigacyjny może potrwać do 30 minut, ponieważ usługa Traffic Analytics musi najpierw zagregować wystarczającą ilość danych, aby uzyskać znaczące informacje, zanim będzie mogła generować jakiekolwiek raporty.

## <a name="usage-scenarios"></a>Scenariusze użytkowania

Niektóre z informacji, które można uzyskać po pełnej konfiguracji usługi Traffic Analytics, są następujące:

### <a name="find-traffic-hotspots"></a>Znajdowanie punktów aktywnych ruchu

**Szukać**

- Które hosty, podsieci i sieci wirtualne wysyłają lub odbierają najwięcej ruchu, przemierzając maksymalny złośliwy ruch i blokując znaczące przepływy?
    - Sprawdź wykres porównawczy dla hosta, podsieci i sieci wirtualnej. Zrozumienie, które hosty, podsieci i sieci wirtualne wysyłają lub odbierają najwięcej ruchu, może pomóc w zidentyfikowaniu hostów, które przetwarzają najwięcej ruchu, oraz czy dystrybucja ruchu odbywa się prawidłowo.
    - Można ocenić, czy natężenie ruchu jest odpowiednie dla hosta. Czy natężenie ruchu jest normalne zachowanie, czy też zasługuje na dalsze badania?
- Ile ruchu przychodzącego/wychodzącego jest?
    -   Czy oczekuje się, że host otrzyma więcej ruchu przychodzącego niż wychodzących lub odwrotnie?
- Statystyki zablokowanego ruchu.
    - Dlaczego host blokuje znaczną ilość łagodnego ruchu? To zachowanie wymaga dalszego zbadania i prawdopodobnie optymalizacji konfiguracji
- Statystyki złośliwego dozwolonego/zablokowanego ruchu
  - Dlaczego host odbiera złośliwy ruch i dlaczego przepływy ze złośliwego źródła są dozwolone? To zachowanie wymaga dalszego badania i prawdopodobnie optymalizacji konfiguracji.

    Wybierz **pozycję Zobacz wszystkie**w obszarze **Host**, jak pokazano na poniższej ilustracji:

    ![Pulpit nawigacyjny prezentujący hosta z większością szczegółów ruchu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Poniższy obraz przedstawia trendów czasu dla pięciu najlepszych gadających hostów i szczegółów związanych z przepływem (dozwolone - przychodzące/ wychodzące i odrzucone - przychodzące / wychodzące przepływy) dla hosta:

    ![Top pięć najczęściej mówiących trendów gospodarza](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Szukać**

- Jakie są najbardziej konwersujące pary gospodarzy?
    - Oczekiwane zachowanie, takie jak komunikacja front-end lub back-end lub nieregularne zachowanie, takie jak back-end ruchu internetowego.
- Statystyka dozwolonego/zablokowanego ruchu
    - Dlaczego host zezwala lub blokuje znaczną liczbę ruchów
- Najczęściej używany protokół aplikacji wśród większości par hostów konwersujących:
    - Czy te aplikacje są dozwolone w tej sieci?
    - Czy aplikacje są poprawnie skonfigurowane? Czy używają odpowiedniego protokołu do komunikacji? Wybierz **pozycję Zobacz wszystkie** w obszarze **Częsta konwersacja**, jak pokazano na poniższym rysunku:

        ![Pulpit nawigacyjny prezentujący najczęstszą rozmowę](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Poniższa ilustracja przedstawia wyznaczanie trendów w czasie dla pięciu najlepszych konwersacji i szczegółów związanych z przepływem, takich jak dozwolone i odrzucone przepływy przychodzące i wychodzące dla pary konwersacji:

    ![Pięć najważniejszych szczegółów i trendu rozmowy](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Szukać**

- Który protokół aplikacji jest najczęściej używany w Twoim środowisku, a które pary hostów konwersujących najczęściej używają protokołu aplikacji?
    - Czy te aplikacje są dozwolone w tej sieci?
    - Czy aplikacje są poprawnie skonfigurowane? Czy używają odpowiedniego protokołu do komunikacji? Oczekiwane zachowanie jest typowe porty, takie jak 80 i 443. W przypadku komunikacji standardowej, jeśli są wyświetlane nietypowe porty, mogą one wymagać zmiany konfiguracji. Wybierz **pozycję Zobacz wszystko** w obszarze Port **aplikacji**, na poniższej ilustracji:

        ![Pulpit nawigacyjny przedstawiający najlepsze protokoły aplikacji](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Poniższe zdjęcia przedstawiają wyznaczanie trendów w czasie dla pięciu pierwszych protokołów L7 i szczegółów związanych z przepływem (na przykład dozwolonych i odrzuconych przepływów) dla protokołu L7:

    ![Pięć najważniejszych protokołów warstwy 7 — szczegóły i trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Szczegóły przepływu protokołu aplikacji w wyszukiwaniu dziennika](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Szukać**

- Trendy wykorzystania pojemności bramy sieci VPN w Twoim środowisku.
    - Każda jednostka SKU sieci VPN umożliwia pewną przepustowość. Czy bramy sieci VPN są niedostatecznie przyśpieszowane?
    - Czy twoje bramy osiągają pojemność? Należy uaktualnić do następnej wyższej jednostki SKU?
- Jakie są najbardziej konwersujące hosty, za pośrednictwem której bramy VPN, nad którym portem?
    - Czy ten wzór jest normalny? Wybierz **pozycję Zobacz wszystko** w obszarze bramy sieci **VPN**, jak pokazano na poniższej ilustracji:

        ![Pulpit nawigacyjny przedstawiający najlepsze aktywne połączenia sieci VPN](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Poniższa ilustracja przedstawia trendy czasowe wykorzystania pojemności bramy sieci VPN platformy Azure oraz szczegóły związane z przepływem (takie jak dozwolone przepływy i porty):

    ![Trend wykorzystania bramy SIECI VPN i szczegóły przepływu](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Wizualizuj rozkład ruchu według lokalizacji geograficznej

**Szukać**

- Dystrybucja ruchu na centrum danych, takie jak najważniejsze źródła ruchu do centrum danych, najlepsze nieautoryzowane sieci rozmawiające z centrum danych i najlepsze protokoły aplikacji konwersujących.
  - Jeśli zauważysz większe obciążenie centrum danych, możesz zaplanować wydajną dystrybucję ruchu.
  - Jeśli fałszywe sieci prowadzą rozmowy w centrum danych, popraw reguły sieciowej sieciowej sieciowej, aby je zablokować.

    Wybierz **opcję Wyświetl mapę** **w obszarze Twoje środowisko**, jak pokazano na poniższym rysunku:

    ![Pulpit nawigacyjny przedstawiający dystrybucję ruchu](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Mapa geograficzna pokazuje górną wstążkę wyboru parametrów, takich jak centra danych (wdrożone/bez wdrożenia/aktywne/nieaktywne/usługa analizy ruchu/analiza ruchu nie włączona) oraz kraje/regiony przyczyniające się do łagodnego/złośliwego ruchu do aktywnego wdrożenia:

    ![Widok mapy geograficznej przedstawiający aktywne wdrażanie](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Mapa geograficzna pokazuje rozkład ruchu do centrum danych z krajów/regionów i kontynentów komunikujących się z nim w niebieskich (ruch łagodny) i czerwonych (złośliwy ruch) kolorowych linii:

    ![Widok mapy geograficznej przedstawiający rozkład ruchu w krajach/regionach i kontynentach](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Szczegóły przepływu dla dystrybucji ruchu w wyszukiwaniu dzienników](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Wizualizuj dystrybucję ruchu przez sieci wirtualne

**Szukać**

- Dystrybucja ruchu na sieć wirtualną, topologia, najlepsze źródła ruchu do sieci wirtualnej, najlepsze nieautoryzowane sieci rozmawiające z siecią wirtualną i najlepsze protokoły aplikacji.
  - Wiedząc, która sieć wirtualna jest rozmowy do której sieci wirtualnej. Jeśli konwersacja nie jest oczekiwana, można ją poprawić.
  - Jeśli nieautoryzowane sieci są rozmowy z sieci wirtualnej, można poprawić reguły sieciowej sieciowej, aby zablokować nieautoryzowane sieci.
 
    Wybierz **pozycję Wyświetl sieci wirtualne** w obszarze **Środowisko**, jak pokazano na poniższej ilustracji:

    ![Pulpit nawigacyjny prezentujący dystrybucję sieci wirtualnej](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Topologia sieci wirtualnej pokazuje górną wstążkę do wyboru parametrów, takich jak sieci wirtualnej (Inter virtual network Connections/Active/Inactive), Połączenia zewnętrzne, Aktywne przepływy i Złośliwe przepływy sieci wirtualnej.
- Topologię sieci wirtualnej można filtrować na podstawie subskrypcji, obszarów roboczych, grup zasobów i przedziału czasu. Dodatkowe filtry ułatwiające zrozumienie przepływu to: Typ przepływu (InterVNet, IntraVNET itd.), Kierunek przepływu (przychodzący, wychodzący), Stan przepływu (dozwolony, zablokowany), VNETs (ukierunkowane i połączone), Typ połączenia (komunikacja równorzędna lub brama — P2S i S2S) oraz sieć sieciowa. Użyj tych filtrów, aby skupić się na sieciach wirtualnych, które chcesz zbadać szczegółowo.
- Topologia sieci wirtualnej pokazuje dystrybucję ruchu do sieci wirtualnej w odniesieniu do przepływów (dozwolonych/zablokowanych/przychodzących/wychodzących/łagodnych/złośliwych), protokołów aplikacji i grup zabezpieczeń sieci, na przykład:

    ![Topologia sieci wirtualnej przedstawiająca szczegóły dystrybucji i przepływu ruchu](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologia sieci wirtualnej prezentująca najwyższego poziomu i więcej filtrów](./media/traffic-analytics/virtual-network-filters.png)

    ![Szczegóły przepływu dla dystrybucji ruchu sieci wirtualnej w wyszukiwaniu dzienników](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Szukać**

- Dystrybucja ruchu na podsieć, topologia, najlepsze źródła ruchu do podsieci, najlepsze nieautoryzowane sieci rozmawiające z podsiecią i protokoły aplikacji dokonujących najpopularniejszych.
    - Wiedząc, która podsieć jest konwersa do której podsieci. Jeśli widzisz nieoczekiwane konwersacje, możesz poprawić konfigurację.
    - Jeśli nieautoryzowane sieci są rozmowy z podsieci, można go poprawić, konfigurując reguły sieciowej sieciowej grup y do blokowania nieuczciwych sieci.
- Topologia podsieci pokazuje górną wstążkę do wyboru parametrów, takich jak podsieć Active/Inactive, Połączenia zewnętrzne, Aktywne przepływy i Złośliwe przepływy podsieci.
- Topologia podsieci pokazuje dystrybucję ruchu do sieci wirtualnej w odniesieniu do przepływów (dozwolone/zablokowane/przychodzące/wychodzące/łagodne/złośliwe), protokół aplikacji i sieciowe grupy zabezpieczeń, na przykład:

    ![Topologia podsieci przedstawiająca dystrybucję ruchu w podsieci sieci wirtualnej w odniesieniu do przepływów](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Szukać**

Dystrybucja ruchu na bramę aplikacji & moduł równoważenia obciążenia, topologia, najlepsze źródła ruchu, najlepsze nieautoryzowane sieci rozmawiające z bramą aplikacji & modułem równoważenia obciążenia oraz protokoły aplikacji dokonujących najpopularniejszych. 
    
 - Wiedząc, która podsieć jest konwersa, do której bramy aplikacji lub moduł równoważenia obciążenia. Jeśli zauważysz nieoczekiwane konwersacje, możesz poprawić konfigurację.
 - Jeśli nieautoryzowane sieci są rozmowy z bramy aplikacji lub moduł równoważenia obciążenia, można go poprawić, konfigurując reguły sieciowej sieciowej grup, aby zablokować nieautoryzowane sieci. 

    ![podsieci-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Wyświetlanie portów i maszyn wirtualnych odbierających ruch z Internetu

**Szukać**

- Które otwarte porty są rozmowy przez Internet?
  - Jeśli otwarte zostaną nieoczekiwane porty, można poprawić konfigurację:

    ![Pulpit nawigacyjny przedstawiający porty odbierające i wysyłające ruch do Internetu](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Szczegóły portów i hostów docelowych platformy Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Szukać**

Czy masz złośliwy ruch w swoim środowisku? Skąd pochodzi? Gdzie jest przeznaczony do?

![Szczegóły złośliwych przepływów ruchu w wyszukiwaniu dzienników](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Wizualizuj trendy w trafieniach reguł sieciowej/sieciowej/sieciowej

**Szukać**

- Które reguły NSG/NSG mają najwięcej trafień na wykresie porównawczym z rozkładem przepływów?
- Jakie są najlepsze pary konwersacji źródłowej i docelowej zgodnie z regułami sieciowej sieciowej/sieciowej sieciowej?

    ![Dashboard prezentujący statystyki trafień nsg](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Poniższe zdjęcia przedstawiają trendy w czasie dla trafień reguł sieciowej grupy zabezpieczeń i szczegółów przepływu miejsca docelowego dla sieciowej grupy zabezpieczeń:

  - Szybko wykrywaj, które sieciowe grupy zabezpieczeń i reguły sieciowej grupy zabezpieczeń przechodzą przez złośliwe przepływy, a które są głównymi złośliwymi adresami IP uzyskującymi dostęp do środowiska chmury
  - Określenie, które reguły sieciowej grupy/sieciowej grupy sieciowej zezwalają/blokują znaczący ruch sieciowy
  - Wybierz filtry górne do szczegółowej kontroli reguł sieciowej sieciowej lub sieciowej sieciowej

    ![Prezentacja trendów czasowych dla trafień reguł sieciowej i najlepszych reguł sieciowej sieciowej](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Najważniejsze szczegóły statystyk reguł sieciowej sieciowej w wyszukiwaniu dzienników](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Aby uzyskać odpowiedzi na często zadawane pytania, zobacz Często zadawane pytania [dotyczące analizy ruchu.](traffic-analytics-faq.md)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak włączyć dzienniki przepływu, zobacz [Włączanie rejestrowania przepływu nsg](network-watcher-nsg-flow-logging-portal.md).
- Aby zrozumieć schemat i szczegóły przetwarzania usługi Traffic Analytics, zobacz [Schemat analizy ruchu](traffic-analytics-schema.md).
