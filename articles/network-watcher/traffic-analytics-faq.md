---
title: Analiza ruchu na platformie Azure często zadawane pytania | Dokumenty firmy Microsoft
description: Uzyskaj odpowiedzi na niektóre z najczęściej zadawanych pytań dotyczących analizy ruchu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: 5e31ed905f05070c8715a63ef3386b0006df0a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840625"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analiza ruchu często zadawane pytania

W tym artykule zbiera się w jednym miejscu wiele najczęściej zadawanych pytań dotyczących analizy ruchu w usłudze Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jakie są wymagania wstępne do korzystania z analizy ruchu?

Usługa Traffic Analytics wymaga następujących wymagań wstępnych:

- Subskrypcja z włączoną funkcją Obserwatora sieci.
- Dzienniki przepływu sieciowej grupy zabezpieczeń (NSG) włączone dla sieciowej grupy zabezpieczeń, które chcesz monitorować.
- Konto usługi Azure Storage do przechowywania dzienników przepływu nieprzetworzonego.
- Obszar roboczy usługi Azure Log Analytics z dostępem do odczytu i zapisu.

Aby umożliwić analizę ruchu, Twoje konto musi spełniać jedną z następujących czynności:

- Twoje konto musi mieć jedną z następujących ról kontroli dostępu opartej na rolach (RBAC) w zakresie subskrypcji: właściciela, współautora, czytelnika lub współautora sieci.
- Jeśli twoje konto nie jest przypisane do jednej z wcześniej wymienionych ról, musi być przypisane do roli niestandardowej, która jest przypisana do następujących akcji, na poziomie subskrypcji.
            
    - Microsoft.Network/applicationGateways/read
    - Sieć Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Aby sprawdzić role przypisane do użytkownika dla subskrypcji:

1. Zaloguj się na platformie Azure przy użyciu **login-AzAccount**. 

2. Wybierz wymaganą subskrypcję za pomocą **select-azSubscription**. 

3. Aby wyświetlić listę wszystkich ról przypisanych do określonego użytkownika, należy użyć **funkcji Get-AzRoleAssignment -SignInName [user email] -IncludeClassicAdministrators**. 

Jeśli nie widzisz żadnych danych wyjściowych, skontaktuj się z odpowiednim administratorem subskrypcji, aby uzyskać dostęp do uruchamiania poleceń. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontrolą dostępu opartą na rolach za pomocą programu Azure PowerShell.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>W jakich regionach platformy Azure jest dostępna usługa Traffic Analytics?

Można użyć analizy ruchu dla grup zabezpieczeń w dowolnym z następujących obsługiwanych regionów:
- Kanada Środkowa
- Zachodnio-środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Północno-środkowe stany USA
- Południowo-środkowe stany USA
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
- US Gov Wirginia
- Chiny Wschodnie 2

Obszar roboczy usługi Log Analytics musi istnieć w następujących regionach:
- Kanada Środkowa
- Zachodnio-środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Północno-środkowe stany USA
- Południowo-środkowe stany USA
- Środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Francja Środkowa
- Europa Zachodnia
- Europa Północna
- Zachodnie Zjednoczone Królestwo
- Południowe Zjednoczone Królestwo
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Azja Wschodnia
- Azja Południowo-Wschodnia 
- Korea Środkowa
- Indie Środkowe
- Japonia Wschodnia
- US Gov Wirginia
- Chiny Wschodnie 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Czy nsgs mogę włączyć dzienniki przepływu, aby być w różnych regionach niż mój obszar roboczy?

Tak, te witryny zabezpieczeń mogą znajdować się w różnych regionach niż obszar roboczy usługi Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Czy wiele grup zabezpieczeń można skonfigurować w jednym obszarze roboczym?

Tak.

## <a name="can-i-use-an-existing-workspace"></a>Czy mogę użyć istniejącego obszaru roboczego?

Tak. Jeśli wybierzesz istniejący obszar roboczy, upewnij się, że został zmigrowany do nowego języka kwerendy. Jeśli nie chcesz uaktualniać obszaru roboczego, musisz utworzyć nowy. Aby uzyskać więcej informacji na temat nowego języka zapytań, zobacz [Azure Monitor dzienniki uaktualnienia do nowego wyszukiwania dziennika](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Czy moje konto usługi Azure Storage może znajdować się w jednej subskrypcji, a mój obszar roboczy usługi Log Analytics jest w innej subskrypcji?

Tak, twoje konto usługi Azure Storage może znajdować się w jednej subskrypcji, a obszar roboczy usługi Log Analytics może znajdować się w innej subskrypcji.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Czy mogę przechowywać nieprzetworzone dzienniki w innej subskrypcji?

Nie. Nieprzetworzone dzienniki można przechowywać na dowolnym koncie magazynu, na którym jest włączona usługa NSG dla dzienników przepływu. Jednak zarówno konto magazynu, jak i nieprzetworzone dzienniki muszą znajdować się w tej samej subskrypcji i regionie.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co zrobić, jeśli nie mogę skonfigurować ndg do analizy ruchu z powodu błędu "Nie znaleziono"?

Wybierz obsługiwany region. Jeśli wybierzesz region nieobjęty obsługą, zostanie wyświetlony błąd "Nie znaleziono". Obsługiwane regiony są wymienione wcześniej w tym artykule.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co zrobić, jeśli otrzymają stan "Nie można załadować" na stronie dzienników przepływu nsg?

Dostawca usługi Microsoft.Insights musi być zarejestrowany, aby rejestrowanie przepływu działało poprawnie. Jeśli nie masz pewności, czy dostawca microsoft.insights jest zarejestrowany dla subskrypcji, zastąp *xxxxx-xxxxx-xxxxxx-xxxx* w następującym poleceniu i uruchom następujące polecenia z programu PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Skonfigurowałem rozwiązanie. Dlaczego nie widzę niczego na desce rozdzielczej?

Pierwsze wyświetlenie pulpitu nawigacyjnego może potrwać do 30 minut. Rozwiązanie musi najpierw zagregować wystarczającą ilość danych, aby uzyskać znaczące szczegółowe informacje. Następnie generuje raporty. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co zrobić, jeśli otrzymuję ten komunikat: "Nie możemy znaleźć żadnych danych w tym obszarze roboczym dla wybranego przedziału czasu. Spróbuj zmienić przedział czasu lub wybrać inny obszar roboczy."?

Wypróbuj następujące opcje:
- Zmień przedział czasu na górnym pasku.
- Wybierz inny obszar roboczy usługi Log Analytics na górnym pasku.
- Spróbuj uzyskać dostęp do analizy ruchu po 30 minutach, jeśli została ostatnio włączona.
    
Jeśli problemy będą się powtarzać, zgłaszaj obawy na [forum głosowym użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli otrzymam ten komunikat: "Analizowanie dzienników przepływu nsg po raz pierwszy. Ten proces może potrwać 20-30 minut. Sprawdź po pewnym czasie. 2) Jeśli powyższy krok nie działa, a obszar roboczy znajduje się w bezpłatnej jednostce SKU, sprawdź użycie obszaru roboczego tutaj, aby sprawdzić poprawność przydziału, w przeciwnym razie zapoznaj się z często zadawanymi pytaniami, aby uzyskać więcej informacji."?

Może zostać wyświetlony ten komunikat, ponieważ:
- Usługa Traffic Analytics została niedawno włączona i może jeszcze nie zagregować wystarczającej ilości danych, aby uzyskać znaczące informacje.
- Używasz bezpłatnej wersji obszaru roboczego usługi Log Analytics i przekroczyła limity przydziału. Może być konieczne użycie obszaru roboczego o większej pojemności.
    
Jeśli problemy będą się powtarzać, zgłaszaj obawy na [forum głosowym użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co zrobić, jeśli otrzymuję ten komunikat: "Wygląda na to, że mamy dane zasobów (topologia) i nie ma informacji o przepływach. Tymczasem kliknij tutaj, aby zobaczyć dane zasobów i zapoznaj się z często zadawanymi pytaniami, aby uzyskać więcej informacji."?

Widzisz informacje o zasobach na pulpicie nawigacyjnym; nie istnieją jednak żadne statystyki związane z przepływem. Dane mogą nie być obecne z powodu braku przepływów komunikacji między zasobami. Poczekaj 60 minut i ponownie spraw, czy stan. Jeśli problem będzie się powtarzał i masz pewność, że istnieje przepływ komunikacji między zasobami, należy zgłaszać obawy na [forum głosowym użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Czy mogę skonfigurować analizę ruchu przy użyciu programu PowerShell lub szablonu lub klienta usługi Azure Resource Manager?

Analizę ruchu można skonfigurować przy użyciu programu Windows PowerShell od wersji 6.2.1. Aby skonfigurować rejestrowanie przepływu i analizę ruchu dla określonej sieciowej sieciowej sieciowej za pomocą polecenia cmdlet Set, zobacz [Set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Aby uzyskać stan rejestrowania przepływu i analizy ruchu dla określonej sieciowej sieciowej sieciowej, zobacz [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Obecnie nie można użyć szablonu usługi Azure Resource Manager do konfigurowania analizy ruchu.

Aby skonfigurować analizę ruchu przy użyciu klienta usługi Azure Resource Manager, zobacz poniższe przykłady.

**Ustawianie przykładu polecenia cmdlet:**
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
**Pobierz przykład polecenia cmdlet:**
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


## <a name="how-is-traffic-analytics-priced"></a>Jak wyceniono traffic analytics?

Analiza ruchu jest mierzona. Pomiar jest oparty na przetwarzaniu danych dziennika przepływu przez usługę i przechowywaniu wynikowych rozszerzonych dzienników w obszarze roboczym usługi Log Analytics. 

Na przykład, zgodnie z [planem cenowym](https://azure.microsoft.com/pricing/details/network-watcher/), biorąc pod uwagę region Zachodnio-Środkowe STANY ZJEDNOCZONE, jeśli dane dziennika przepływu przechowywane na koncie magazynu przetwarzane przez Traffic Analytics wynosi 10 GB, a rozszerzone dzienniki połkowane w obszarze roboczym usługi Log Analytics wynosi 1 GB, stosowane opłaty to: 10 x 2,3$ + 1 x 2,76$ = 25,76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Jak często usługa Traffic Analytics przetwarza dane?

Zapoznaj się z [sekcją agregacji danych](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) w schemacie analizy ruchu i dokumencie agregacji danych

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>W jaki sposób usługa Traffic Analytics decyduje, że adres IP jest złośliwy? 

Usługa Traffic Analytics opiera się na wewnętrznych systemach analizy zagrożeń firmy Microsoft, aby uznać adres IP za złośliwy. Systemy te wykorzystują różne źródła telemetryczne, takie jak produkty i usługi firmy Microsoft, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) i zewnętrzne kanały informacyjne i budują na nim wiele informacji. Niektóre z tych danych to Microsoft Internal. Jeśli znany adres IP jest coraz oflagowane jako złośliwy, należy podnieść bilet pomocy technicznej, aby poznać szczegóły.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Jak ustawić alerty dotyczące danych usługi Traffic Analytics?

Usługa Traffic Analytics nie obsługuje wbudowanych alertów. Jednak ponieważ dane usługi Traffic Analytics są przechowywane w usłudze Log Analytics, można pisać zapytania niestandardowe i ustawiać na nich alerty. Kroki:
- Możesz użyć shortlink dla analizy dzienników w analizie ruchu. 
- Pisanie zapytań za pomocą [schematu udokumentowanego w tym miejscu](traffic-analytics-schema.md) 
- Kliknij "Nowa reguła alertu", aby utworzyć alert
- Aby utworzyć alert, zapoznaj się z [dokumentacją alertów dziennika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premise-traffic"></a>Jak sprawdzić, które maszyny wirtualne odbierają najwięcej ruchu lokalnego

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            | where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
             | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
            | render timechart

  W przypadku usług IP:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
            | render timechart

Dla czasu, użyj formatu: yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-recieved-by-my-vms-from-on-premise-machines"></a>Jak sprawdzić odchylenie standardowe ruchu odbierane przez maszyny wirtualne z maszyn lokalnych

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by vm


W przypadku usług IP:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by IP
            
## <a name="how-do-i-check-which-ports-are-reachable-or-bocked-between-ip-pairs-with-nsg-rules"></a>Jak sprawdzić, które porty są osiągalne (lub bocked) między parami IP z regułami NSG

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
            | extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
            destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
            | mvexpand SourceIp = sourceIPs to typeof(string)
            | mvexpand DestIp = destIPs to typeof(string)
            | project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
            | summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak poruszać się za pomocą klawiatury w widoku mapy geograficznej?

Strona mapy geograficznej zawiera dwie główne sekcje:
    
- **Baner:** Baner w górnej części mapy geograficznej zawiera przyciski, aby wybrać filtry dystrybucji ruchu (na przykład Wdrażanie, Ruch z krajów/regionów i Złośliwy). Po wybraniu przycisku odpowiedni filtr jest stosowany na mapie. Na przykład jeśli wybierzesz przycisk Aktywny, mapa podświetla aktywne centra danych we wdrożeniu.
- **Mapa:** Pod banerem sekcja mapy pokazuje rozkład ruchu między centrami danych platformy Azure i krajami/regionami.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja za pomocą klawiatury na banerze
    
- Domyślnie wybór na stronie mapy geograficznej dla banera jest filtrem "Kontrolery domeny platformy Azure".
- Aby przejść do innego filtru, użyj klucza `Tab` lub klucza. `Right arrow` Aby przejść do tyłu, `Shift+Tab` użyj `Left arrow` klawisza lub klucza. Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij `Enter` lub `Down` klawisz strzałki, aby zastosować wybrany filtr. Na podstawie wyboru filtru i wdrażania wyróżniony jest jeden lub wiele węzłów w sekcji mapy.
- Aby przełączyć się między `Ctrl+F6`banerem a mapą, naciśnij przycisk .
        
### <a name="keyboard-navigation-on-the-map"></a>Nawigacja za pomocą klawiatury na mapie
    
- Po wybraniu dowolnego filtru na `Ctrl+F6`banerze i naciśnięciu fokus zostanie przeniesiony do jednego z wyróżnionych węzłów **(centrum danych platformy Azure** lub **kraj/region)** w widoku mapy.
- Aby przejść do innych wyróżnionych węzłów na `Tab` mapie, użyj jednego z nich lub klucza `Right arrow` do ruchu do przodu. Użyj `Shift+Tab` lub `Left arrow` klucza do ruchu wstecz.
- Aby wybrać dowolny wyróżniony węzeł na `Enter` `Down arrow` mapie, użyj klucza lub klucza.
- Przy wyborze takich węzłów fokus przenosi się do **pola narzędzi informacyjnych** dla węzła. Domyślnie fokus przechodzi do zamkniętego przycisku w **polu Narzędzia informacyjne**. Aby dalej poruszać się wewnątrz `Right arrow` `Left arrow` widoku **Pole,** użyj i klawiszy, aby przejść do przodu i do tyłu, odpowiednio. Naciśnięcie `Enter` ma taki sam efekt, jak wybranie przycisku fokusu w **polu narzędzi informacyjnych**.
- Po naciśnięciu, `Tab` gdy fokus znajduje się na **polu narzędzi informacyjnych,** fokus przesuwa się do punktów końcowych na tym samym kontynencie co wybrany węzeł. Użyj `Right arrow` i `Left arrow` klucze, aby przejść przez te punkty końcowe.
- Aby przejść do innych punktów końcowych przepływu `Tab` lub klastrów `Shift+Tab` kontynentu, należy użyć do ruchu do przodu i do tyłu.
- Gdy fokus znajduje się na `Enter` `Down` **klastrach kontynentu,** użyj klawiszy strzałek lub klawiszy strzałek, aby wyróżnić punkty końcowe wewnątrz klastra kontynentu. Aby przejść przez punkty końcowe i przycisk zamknij w polu informacyjnym `Right arrow` `Left arrow` klastra kontynentu, należy użyć klucza lub klucza dla ruchu do przodu i do tyłu, odpowiednio. W dowolnym punkcie końcowym `Shift+L` można przełączyć się do linii połączenia z wybranego węzła do punktu końcowego. Można nacisnąć `Shift+L` ponownie, aby przejść do wybranego punktu końcowego.
        
### <a name="keyboard-navigation-at-any-stage"></a>Nawigacja za pomocą klawiatury na każdym etapie
    
- `Esc`zwija rozwinięty wybór.
- Klucz `Up arrow` wykonuje tę samą `Esc`akcję co . Klucz `Down arrow` wykonuje tę samą `Enter`akcję co .
- Służy `Shift+Plus` do powiększania `Shift+Minus` i pomniejszania.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak nawigować za pomocą klawiatury w widoku topologii sieci wirtualnej?

Strona topologii sieci wirtualnych zawiera dwie główne sekcje:
    
- **Baner:** Baner w górnej części topologii sieci wirtualnych zawiera przyciski, aby wybrać filtry dystrybucji ruchu (na przykład Połączone sieci wirtualne, Rozłączone sieci wirtualne i Publiczne adresy IP). Po wybraniu przycisku odpowiedni filtr jest stosowany do topologii. Na przykład po wybraniu przycisku Active topologia wyróżnia aktywne sieci wirtualne we wdrożeniu.
- **Topologia**: Pod banerem sekcja topologii pokazuje rozkład ruchu między sieciami wirtualnymi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja za pomocą klawiatury na banerze
    
- Domyślnie wybór na stronie topologii sieci wirtualnych dla banera jest filtrem "Połączone sieci wirtualne".
- Aby przejść do innego `Tab` filtru, użyj klawisza, aby przejść do przodu. Aby przejść do tyłu, użyj klawisza. `Shift+Tab` Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij, `Enter` aby zastosować wybrany filtr. Na podstawie wyboru filtru i wdrożenia wyróżniony jest jeden lub wiele węzłów (sieć wirtualna) w sekcji topologii.
- Aby przełączyć się między banerem a topologią, naciśnij przycisk `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacja za pomocą klawiatury w topologii
    
- Po wybraniu dowolnego filtru na `Ctrl+F6`banerze i naciśnięciu fokus zostanie przeniesiony do jednego z podświetlonych węzłów **(VNet)** w widoku topologii.
- Aby przejść do innych wyróżnionych węzłów w `Shift+Right arrow` widoku topologii, użyj klawisza do przesunięcia do przodu. 
- W wyróżnionych węzłach fokus przenosi się do **pola narzędzi informacyjnych** dla węzła. Domyślnie fokus przechodzi do przycisku **Więcej szczegółów** w polu **Narzędzia informacyjne**. Aby dalej poruszać się wewnątrz `Right arrow` widoku `Left arrow` **Pole,** użyj klawiszy i klawiszy, aby przejść do przodu i do tyłu, odpowiednio. Naciśnięcie `Enter` ma taki sam efekt, jak wybranie przycisku fokusu w **polu narzędzi informacyjnych**.
- Przy wyborze takich węzłów możesz odwiedzić wszystkie jego połączenia, jeden po `Shift+Left arrow` drugim, naciskając klawisz. Fokus zostanie przeniesiony do **pola narzędzi informacyjnych** tego połączenia. W dowolnym momencie fokus można przesunąć `Shift+Right arrow` z powrotem do węzła, naciskając ponownie.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak nawigować za pomocą klawiatury w widoku topologii podsieci?

Strona topologii podsieci wirtualnych zawiera dwie główne sekcje:
    
- **Baner:** Baner u góry topologii podsieci wirtualnych zawiera przyciski służące do wybierania filtrów dystrybucji ruchu (na przykład podsieci Aktywne, Średnie i Bramy). Po wybraniu przycisku odpowiedni filtr jest stosowany do topologii. Na przykład po wybraniu przycisku Active topologia podświetla aktywną podsieć wirtualną we wdrożeniu.
- **Topologia**: Pod banerem sekcja topologii pokazuje rozkład ruchu wśród podsieci wirtualnych.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacja za pomocą klawiatury na banerze
    
- Domyślnie zaznaczenie na stronie topologii podsieci wirtualnych baneru jest filtrem "Podsieci".
- Aby przejść do innego `Tab` filtru, użyj klawisza, aby przejść do przodu. Aby przejść do tyłu, użyj klawisza. `Shift+Tab` Nawigacja do przodu jest od lewej do prawej, a następnie od góry do dołu.
- Naciśnij, `Enter` aby zastosować wybrany filtr. Na podstawie wyboru filtru i wdrażania wyróżniony jest jeden lub wiele węzłów (podsieci) w sekcji topologii.
- Aby przełączyć się między banerem a topologią, naciśnij przycisk `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacja za pomocą klawiatury w topologii
    
- Po wybraniu dowolnego filtru na `Ctrl+F6`banerze i naciśnięciu fokus zostanie przeniesiony do jednego z podświetlonych węzłów **(podsieci)** w widoku topologii.
- Aby przejść do innych wyróżnionych węzłów w `Shift+Right arrow` widoku topologii, użyj klawisza do przesunięcia do przodu. 
- W wyróżnionych węzłach fokus przenosi się do **pola narzędzi informacyjnych** dla węzła. Domyślnie fokus przechodzi do przycisku **Więcej szczegółów** w polu **Narzędzia informacyjne**. Aby dalej poruszać się wewnątrz `Right arrow` `Left arrow` widoku **Pole,** użyj i klawiszy, aby przejść do przodu i do tyłu, odpowiednio. Naciśnięcie `Enter` ma taki sam efekt, jak wybranie przycisku fokusu w **polu narzędzi informacyjnych**.
- Przy wyborze takich węzłów możesz odwiedzić wszystkie jego połączenia, jeden `Shift+Left arrow` po drugim, naciskając klawisz. Fokus zostanie przeniesiony do **pola narzędzi informacyjnych** tego połączenia. W dowolnym momencie fokus można przesunąć `Shift+Right arrow` z powrotem do węzła, naciskając ponownie.    

