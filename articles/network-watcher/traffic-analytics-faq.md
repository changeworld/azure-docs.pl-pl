---
title: Analiza ruchu platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na niektóre często zadawane pytania na temat analizy ruchu.
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
ms.date: 03/08/2018
ms.author: kumud
ms.openlocfilehash: e4e9ef4f3a50aeac4db4d2cc2f2b6cbafcc47268
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051625"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analiza ruchu — często zadawane pytania

W tym artykule zbiera dane w jednym miejscu wielu najczęściej zadawane pytania na temat analizy ruchu w usłudze Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jakie są wymagania wstępne dotyczące Korzystanie z analizy ruchu?

Analiza ruchu wymaga następujących wymagań wstępnych:

- Subskrypcja z włączonym Agentem Network Watcher.
- Dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) włączone dla sieciowych grup zabezpieczeń, którą chcesz monitorować.
- Konto usługi Azure Storage do przechowywania dzienników przepływu raw.
- Obszarem roboczym usługi Azure Log Analytics, za pomocą odczytu i zapisu.

Twoje konto musi spełniać jeden z następujących czynności, aby włączyć analizę ruchu:

- Twoje konto musi mieć jeden z następujących ról kontroli dostępu opartej na rolach w zakresie subskrypcji: właściciel, współautor, Czytelnik lub Współautor sieci.
- Jeśli Twoje konto nie jest przypisany do jednej z wyżej wymienionych ról, musi ona zostać przypisana do rolę niestandardową, która jest przypisana do następujących czynności, na poziomie subskrypcji.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Aby sprawdzić role przypisane do użytkownika w ramach subskrypcji:

1. Logowanie do platformy Azure przy użyciu **AzAccount logowania**. 

2. Wybierz subskrypcję, wymagane przy użyciu **AzSubscription wybierz**. 

3. Aby wyświetlić listę wszystkich ról, które są przypisane do określonego użytkownika, należy użyć **Get AzRoleAssignment - SignInName [adres e-mail użytkownika] - IncludeClassicAdministrators**. 

Jeśli nie widzisz żadnych danych wyjściowych, skontaktuj się z administratorem odpowiednią subskrypcję, aby uzyskać dostęp do poleceń. Aby uzyskać więcej informacji, zobacz [zarządzanie kontrolą dostępu opartej na rolach przy użyciu programu Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>W jakich regionach platformy Azure jest dostępne rozwiązanie analiza ruchu?

Korzystanie z analizy ruchu dla sieciowych grup zabezpieczeń w jednym z następujących obsługiwanych regionów:
- Kanada Środkowa
- Środkowo-zachodnie stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Środkowo-północne stany USA
- Środkowo-południowe stany USA
- Środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Francja Środkowa
- Europa Zachodnia
- Europa Północna
- Brazylia Południowa
- Zachodnie Zjednoczone Królestwo
- Południowe Zjednoczone Królestwo
- Australia Wschodnia
- Australia Południowo-Wschodnia 
- Azja Wschodnia
- Azja Południowo-Wschodnia
- Korea Środkowa
- Indie Środkowe
- Indie Południowe
- Japonia Wschodnia
- Japonia Zachodnia
- Administracja USA — Wirginia

Obszar roboczy usługi Log Analytics muszą znajdować się w następujących regionach:
- Kanada Środkowa
- Środkowo-zachodnie stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Środkowo-południowe stany USA
- Środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Francja Środkowa
- Europa Zachodnia
- Europa Północna
- Południowe Zjednoczone Królestwo
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Azja Wschodnia
- Azja Południowo-Wschodnia 
- Korea Środkowa
- Indie Środkowe
- Japonia Wschodnia
- Administracja USA — Wirginia

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Można włączyć przepływu sieciowych grup zabezpieczeń dzienniki mieścić się w różnych regionach niż Mój obszar roboczy?

Tak, te sieciowe grupy zabezpieczeń mogą być w różnych regionach niż obszaru roboczego usługi Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Wiele sieciowych grup zabezpieczeń można skonfigurować w jeden obszar roboczy?

Tak.

## <a name="can-i-use-an-existing-workspace"></a>Czy można użyć istniejącego obszaru roboczego?

Tak. Wybranie istniejącego obszaru roboczego, upewnij się, że jej została zmigrowana do nowego języka zapytań. Jeśli nie chcesz uaktualnić obszar roboczy, musisz utworzyć nowy. Aby uzyskać więcej informacji na temat nowego języka zapytań, zobacz [nową funkcją przeszukiwania dzienników usługi Azure Monitor rejestruje uaktualnienia](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Moje konto usługi Azure Storage można w jednej subskrypcji i Mój obszar roboczy usługi Log Analytics jest w innej subskrypcji?

Tak, konta usługi Azure Storage może być w jednej subskrypcji i obszaru roboczego usługi Log Analytics może znajdować się w innej subskrypcji.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Czy mogę przechowywać nieprzetworzonych dzienników w innej subskrypcji?

Nie. Nieprzetworzonych dzienników można przechowywać w wszystkich kont magazynu, w której włączono dla dzienników przepływu sieciowej grupy zabezpieczeń. Jednak zarówno pierwotne dzienniki, jak i konto magazynu musi być w tej samej subskrypcji i regionu.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co zrobić, jeśli nie można skonfigurować sieciowej grupy zabezpieczeń do analizy ruchu z powodu błędu "Nie znaleziono"?

Wybierz obsługiwany region. Jeśli wybierzesz-obsługiwany region, komunikat o błędzie "Nie znaleziono". Obsługiwane regiony są wymienione we wcześniejszej części tego artykułu.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co zrobić, jeśli pojawia się jego stan, "nie można załadować," w obszarze strony dzienniki przepływu sieciowej grupy zabezpieczeń?

Dostawcy Microsoft.Insights musi być zarejestrowany dla rejestrowania, aby zapewnić prawidłowe działanie usługi flow. Jeśli nie masz pewności, czy dostawca Microsoft.Insights jest zarejestrowany dla Twojej subskrypcji, należy zastąpić *xxxxx-xxxxx-xxxxxx-xxxx* w następujące polecenia i uruchom następujące polecenia programu PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Czy mogę skonfigurować rozwiązania. Dlaczego nie widzę żadnych na pulpicie nawigacyjnym?

Pulpit nawigacyjny może potrwać do 30 minut po raz pierwszy. Rozwiązanie musi najpierw agregacji wystarczającej ilości danych dla niego do uzyskania istotnych informacji. Następnie generuje raporty. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co zrobić, jeśli pojawia się ten komunikat: "Nie znaleziono żadnych danych w tym obszarze roboczym dla wybranego okresu. Spróbuj zmienić przedział czasu, lub wybierz inny obszar roboczy. "?

Wypróbuj następujące opcje:
- Zmień przedział czasu na górnym pasku.
- Wybierz inny obszar roboczy usługi Log Analytics na górnym pasku.
- Spróbuj uzyskać dostęp do analizy ruchu, po upływie 30 minut, jeśli niedawno została włączona.
    
Jeśli problemy będą się powtarzać, budzić w [User voice forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli pojawia się ten komunikat: "Analizuje dzienniki przepływu sieciowej grupy zabezpieczeń po raz pierwszy. Ten proces może potrwać 20 – 30 minut. Sprawdź ponownie po pewnym czasie. (2) Jeśli Twój obszar roboczy jest w obszarze bezpłatna jednostka SKU powyższy krok nie zadziała, Sprawdź tutaj użycie obszaru roboczego, można zweryfikować za pośrednictwem przydziału, wypadku zapoznaj się z często zadawane pytania dotyczące uzyskania dodatkowych informacji. "?

Może zostać wyświetlony ten komunikat, ponieważ:
- Analiza ruchu niedawno został włączony i może nie jeszcze mają zagregowane wystarczającej ilości danych dla niego do uzyskania istotnych informacji.
- Używasz bezpłatną wersję obszaru roboczego usługi Log Analytics i przekroczenia limitów przydziału. Może być konieczne używanie obszaru roboczego o większej pojemności.
    
Jeśli problemy będą się powtarzać, budzić w [User voice forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli pojawia się ten komunikat: "Wygląda na to mamy dane zasobów (topologia) i żadnych informacji przepływów. W międzyczasie, kliknij tutaj, aby wyświetlić dane zasobów i odnoszą się do — często zadawane pytania, aby uzyskać więcej informacji. "?

Informacje o zasobach są widoczne na pulpicie nawigacyjnym; jednak nie statystyki związane z przepływem są obecne. Dane mogą być obecne ze względu na Brak przepływów komunikacji między zasobami. Poczekaj, aż 60 minut, a następnie ponownie sprawdzić stan. Jeśli problem będzie się powtarzać, a masz pewności, czy istnieją przepływów komunikacji między zasobami, budzić w [User voice forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Analiza ruchu przy użyciu programu PowerShell można skonfigurować lub szablonu usługi Azure Resource Manager lub klienta?

Analiza ruchu można skonfigurować za pomocą programu Windows PowerShell z wersji 6.2.1 lub nowszy. Aby skonfigurować rejestrowanie przepływu i analiza ruchu dla określonej sieciowej grupy zabezpieczeń, za pomocą polecenia cmdlet Set, zobacz [AzNetworkWatcherConfigFlowLog zestaw](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Rejestrowanie przepływu i stan analizy ruchu dla określonej sieciowej grupy zabezpieczeń, można znaleźć [Get AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Obecnie nie można użyć szablonu usługi Azure Resource Manager do konfigurowania analizy ruchu.

Aby skonfigurować rozwiązanie analiza ruchu przy użyciu klienta usługi Azure Resource Manager, zobacz następujące przykłady.

**Ustaw przykładzie polecenia cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Pobierz przykładowe polecenie cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Jaka jest cena analizy ruchu

Analiza ruchu jest mierzone. Zliczanie opiera się na przetwarzanie danych dziennika przepływu przez usługę i przechowywania, wynikowy rozszerzone dzienników w obszarze roboczym usługi Log Analytics. 

Na przykład zgodnie [planu cenowego](https://azure.microsoft.com/pricing/details/network-watcher/), biorąc pod uwagę w regionie zachodnio-środkowe stany USA, jeśli dzienników przepływów danych przechowywanych na koncie magazynu, przetwarzane przez analizę ruchu wynosi 10 GB i rozszerzone dzienników pozyskanych w obszarze roboczym usługi Log Analytics jest 1 GB, a następnie odpowiednie opłaty są: 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Jak często Traffic Analytics może przetwarzać dane?

Zapoznaj się [sekcji agregacji danych](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) Traffic Analytics schematu i danych agregacji dokumencie

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Jak analiza ruchu zdecydować, czy adres IP jest złośliwego? 

Analiza ruchu opiera się na systemach analizy zagrożeń wewnętrznych firmy Microsoft można uznać za jako złośliwych adresów IP. Te systemy wykorzystuj telemetrii różnych źródeł, takich jak produkty firmy Microsoft i usługi, jednostka przestępstw cyfrowych (DCU) firmy Microsoft, Microsoft Security Response Center (MSRC) i zewnętrznych źródeł danych i tworzenie partii analizy na ich podstawie. Niektóre z tych danych jest najważniejszymi wewnętrzny. Jeśli znanymi adresami IP pobierania z flagą malicios, zgłoś bilet pomocy technicznej, aby poznać szczegóły.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Jak ustawić alerty na danych, analizy ruchu

Analiza ruchu nie ma wbudowana obsługa alertów. Jednak ponieważ analizy ruchu, dane są przechowywane w usłudze Log Analytics można zapisać zapytania niestandardowe i ustawić alerty związane z nimi. Kroki:
- Skróconym można użyć usługi Log Analytics w usłudze Traffic Analytics. 
- Użyj [schematu opisane tutaj](traffic-analytics-schema.md) można zapisać zapytania 
- Kliknij pozycję "Nowa reguła alertu" do utworzenia alertu
- Zapoznaj się [dziennika alertów dokumentacji](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) do utworzenia alertu

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak można przejść za pomocą klawiatury w widoku mapy geograficznej?

Strona mapy geograficznej zawiera dwa główne obszary:
    
- **Transparent**: Baner w górnej części mapy geograficznej zawiera przyciski, aby wybrać filtry dystrybucji ruchu (na przykład wdrożenia, ruch z krajów/regionów i złośliwym kodem). Po wybraniu przycisku, odpowiedni filtr jest stosowany na mapie. Na przykład jeśli wybierzesz przycisk aktywne, mapy wyróżnia active centrów danych w danym wdrożeniu.
- **Mapa**: Poniżej baneru sekcji map pokazuje Dystrybucja ruchu między centrami danych platformy Azure i kraje/regiony.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigowanie przy użyciu klawiatury na banerze
    
- Domyślnie wyboru na stronie mapy geograficznej transparent jest filtrem "Kontrolery domeny platformy Azure".
- Aby przenieść na inny filtr, należy użyć `Tab` lub `Right arrow` klucza. Aby przejść do tyłu, użyj `Shift+Tab` lub `Left arrow` klucza. Przeglądania do przodu jest lewej do prawej, a następnie góry do dołu.
- Naciśnij klawisz `Enter` lub `Down` klawisz strzałki, aby zastosować wybrany filtr. Na podstawie wyboru filtrów i wdrożenia, jednego lub wielu węzłów w sekcji mapie są wyróżnione.
- Aby przełączać się między transparent i mapy, naciśnij klawisze `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Nawigowanie przy użyciu klawiatury na mapie
    
- Po naciśnięciu i wybrany dowolny filtr na banerze `Ctrl+F6`, Przenieś fokus do jednego z węzłów wyróżnione (**centrum danych platformy Azure** lub **Kraj/Region**) w widoku mapy.
- Aby przejść do innych węzłów wyróżnione na mapie, należy użyć `Tab` lub `Right arrow` klucza w przypadku przenoszenia do przodu. Użyj `Shift+Tab` lub `Left arrow` klucza w przypadku przenoszenia z poprzednimi wersjami.
- Aby wybrać dowolny węzeł wyróżnione na mapie, użyj `Enter` lub `Down arrow` klucza.
- Po wybraniu tych węzłów, Przenieś fokus do **informacji przybornika** dla węzła. Domyślnie, Przenieś fokus do zamkniętego przycisku **informacji przybornika**. Dodatkowo przenieść wewnątrz **pole** widoku `Right arrow` i `Left arrow` kluczy, aby przejść do przodu i Wstecz, odpowiednio. Naciśnięcie klawisza `Enter` ma ten sam efekt, wystarczy wybrać przycisk ukierunkowane na **informacji przybornika**.
- Po naciśnięciu klawisza `Tab` gdy fokus jest ustawiony na **informacji przybornika**, fokusu do punktów końcowych w ten sam kontynent jako wybranego węzła. Użyj `Right arrow` i `Left arrow` klucze do przechodzenia między tymi punktami końcowymi.
- Aby przejść do innych punktów końcowych usługi flow lub klastry kontynent, należy użyć `Tab` w przypadku przenoszenia do przodu i `Shift+Tab` w przypadku przenoszenia z poprzednimi wersjami.
- Gdy fokus jest ustawiony na **klastrów kontynent**, użyj `Enter` lub `Down` klawiszy strzałek, aby wyróżnić punkty końcowe wewnątrz klastra kontynentu. Można przenieść pole informacji kontynent klastra za pośrednictwem punktów końcowych i przycisk Zamknij, za pomocą albo `Right arrow` lub `Left arrow` klucza w przypadku przenoszenia do przodu i do tyłu, odpowiednio. W dowolnym punkcie końcowym, możesz użyć `Shift+L` przejść do wiersza połączenia z wybranego węzła do punktu końcowego. Możesz nacisnąć przycisk `Shift+L` ponownie, aby przejść do wybranego punktu końcowego.
        
### <a name="keyboard-navigation-at-any-stage"></a>Nawigowanie przy użyciu klawiatury na każdym etapie
    
- `Esc` Zwija rozwinięte zaznaczenie.
- `Up arrow` Klucz wykonuje ta sama akcja co `Esc`. `Down arrow` Klucz wykonuje ta sama akcja co `Enter`.
- Użyj `Shift+Plus` powiększenie fragmentu ekranu, a `Shift+Minus` Aby pomniejszyć.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak można przejść za pomocą klawiatury w widoku topologii sieci wirtualnej?

Na stronie topologia sieci wirtualnych zawiera dwa główne obszary:
    
- **Transparent**: Baner u góry topologii sieci wirtualnych zawiera przyciski, aby wybrać filtry dystrybucji ruchu (na przykład połączonych sieci wirtualnych, odłączonych sieciach wirtualnych i publicznych adresów IP). Po wybraniu przycisku, odpowiedni filtr jest stosowany na topologii. Na przykład jeśli wybierzesz przycisk aktywne, topologia wyróżnia active sieci wirtualnych w danym wdrożeniu.
- **Topologia**: Poniżej baneru sekcji topologia przedstawia Dystrybucja ruchu między sieciami wirtualnymi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigowanie przy użyciu klawiatury na banerze
    
- Domyślnie na stronie topologia sieci wirtualnych transparent wybrano filtru "Połączone sieci wirtualne".
- Można przenieść na inny filtr, za pomocą `Tab` klucz umożliwiający przenoszenie do przodu. Aby przenieść do tyłu, użyj `Shift+Tab` klucza. Przeglądania do przodu jest lewej do prawej, a następnie góry do dołu.
- Naciśnij klawisz `Enter` do zastosowania w wybranym filtrze. Na podstawie wyboru filtrów i wdrożenia, jeden lub wiele węzłów (sieci wirtualnej) w sekcji topologia są wyróżnione.
- Aby przełączać się między transparent i topologii, naciśnij klawisze `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigowanie przy użyciu klawiatury na topologii
    
- Po naciśnięciu i wybrany dowolny filtr na banerze `Ctrl+F6`, Przenieś fokus do jednego z węzłów wyróżnione (**sieci wirtualnej**) w widoku topologii.
- Aby przejść do innych węzłów wyróżnione w widoku topologii, należy użyć `Shift+Right arrow` klucza w przypadku przenoszenia do przodu. 
- W węzłach wyróżnione fokus do **informacji przybornika** dla węzła. Domyślnie, Przenieś fokus do **szczegółowe** znajdujący się na **informacji przybornika**. Dodatkowo przenieść wewnątrz **pole** widoku `Right arrow` i `Left arrow` kluczy, aby przejść do przodu i Wstecz, odpowiednio. Naciśnięcie klawisza `Enter` ma ten sam efekt, wystarczy wybrać przycisk ukierunkowane na **informacji przybornika**.
- Po wybraniu tych węzłów, można znaleźć wszystkie swoje połączenia, jedno po drugim, naciskając klawisz `Shift+Left arrow` klucza. Przenieś fokus do **informacji przybornika** tego połączenia. W dowolnym momencie można przesunięte fokus do węzła, naciskając klawisz `Shift+Right arrow` ponownie.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak można przejść za pomocą klawiatury w widoku topologii podsieci?

Na stronie topologia podsieci wirtualnych zawiera dwa główne obszary:
    
- **Transparent**: Baner u góry topologii podsieci wirtualnych zawiera przyciski, aby wybrać filtry dystrybucji ruchu (na przykład aktywny, średnia i brama podsieci). Po wybraniu przycisku, odpowiedni filtr jest stosowany na topologii. Na przykład jeśli wybierzesz przycisk aktywne, topologia wyróżnia aktywnych podsieci wirtualnych w danym wdrożeniu.
- **Topologia**: Poniżej baneru sekcji topologia przedstawia Dystrybucja ruchu w ramach podsieci wirtualnych.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigowanie przy użyciu klawiatury na banerze
    
- Domyślnie na stronie topologia podsieci wirtualnej transparent wybrano filtru "Podsieci".
- Można przenieść na inny filtr, za pomocą `Tab` klucz umożliwiający przenoszenie do przodu. Aby przenieść do tyłu, użyj `Shift+Tab` klucza. Przeglądania do przodu jest lewej do prawej, a następnie góry do dołu.
- Naciśnij klawisz `Enter` do zastosowania w wybranym filtrze. Na podstawie wyboru filtrów i wdrożenia, jeden lub wiele węzłów (podsieć) w sekcji topologia są wyróżnione.
- Aby przełączać się między transparent i topologii, naciśnij klawisze `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigowanie przy użyciu klawiatury na topologii
    
- Po naciśnięciu i wybrany dowolny filtr na banerze `Ctrl+F6`, Przenieś fokus do jednego z węzłów wyróżnione (**podsieci**) w widoku topologii.
- Aby przejść do innych węzłów wyróżnione w widoku topologii, należy użyć `Shift+Right arrow` klucza w przypadku przenoszenia do przodu. 
- W węzłach wyróżnione fokus do **informacji przybornika** dla węzła. Domyślnie, Przenieś fokus do **szczegółowe** znajdujący się na **informacji przybornika**. Dodatkowo przenieść wewnątrz **pole** widoku `Right arrow` i `Left arrow` kluczy, aby przejść do przodu i Wstecz, odpowiednio. Naciśnięcie klawisza `Enter` ma ten sam efekt, wystarczy wybrać przycisk ukierunkowane na **informacji przybornika**.
- Po wybraniu tych węzłów, można znaleźć wszystkie swoje połączenia, jedno po drugim, naciskając klawisz `Shift+Left arrow` klucza. Przenieś fokus do **informacji przybornika** tego połączenia. W dowolnym momencie można przesunięte fokus do węzła, naciskając klawisz `Shift+Right arrow` ponownie.    

