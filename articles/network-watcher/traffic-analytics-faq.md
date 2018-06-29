---
title: Analiza ruchu Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na niektóre często zadawane pytania dotyczące analizy ruchu.
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
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: a4b87d92751c84d96bc70915d16adae7943c145e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062881"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analiza ruchu — często zadawane pytania

W tym artykule zbiera w jednym miejscu wiele najczęściej zadawane pytania dotyczące analizy ruchu w obserwatora sieciowego Azure.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jakie są wymagania wstępne dotyczące korzystania analizy ruchu?

Analiza ruchu wymagane są następujące wymagania wstępne:

- Subskrypcja obserwatora sieciowego włączony.
- Dzienniki przepływu grupy zabezpieczeń sieci (NSG) włączone dla grupy NSG, który chcesz monitorować.
- Konto usługi Azure Storage do przechowywania raw flog dzienniki.
- Obszarem roboczym usługi Analiza dzienników Azure, z odczytu i zapisu.

Twoje konto musi spełniać jeden z następujących umożliwiające analizy ruchu:

- Twoje konto musi należeć do jednej z następujących ról na poziomie subskrypcji: konto administratora, administratorem usługi ani współadministratorem.
- Twoje konto musi mieć jeden z następujących ról kontroli dostępu opartej na rolach w zakresie subskrypcji: właściciela, współautora, czytelnika lub współautora sieci.
- Jeśli Twoje konto nie jest przypisany do jednej z wyżej wymienionych ról, musi zostać przypisane do niestandardowej roli zabezpieczeń przypisane następujące czynności na poziomie subskrypcji.
            
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

1. Logowanie do platformy Azure przy użyciu **Login-AzureRmAccount**. 

2. Wybierz wymagane subskrypcję za pomocą **Select-AzureRmSubscription**. 

3. Aby wyświetlić listę wszystkich ról, które są przypisane do określonego użytkownika, należy użyć **Get AzureRmRoleAssignment - SignInName [adres e-mail użytkownika] - IncludeClassicAdministrators**. 

Jeśli nie widać żadnych danych wyjściowych, skontaktuj się z administratorem odpowiednich subskrypcji, uzyskania dostępu do uruchamiania polecenia. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontroli dostępu opartej na rolach przy użyciu programu Azure PowerShell](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>W systemie Azure, które regiony są analizy ruchu dostępne?

Korzystając z analizy ruchu dla grup NSG w jednym z następujących obsługiwanych regionów: zachodnie centralnej nam, wschodnie stany USA, wschodnie stany USA 2, północno-środkowe stany, południowo-środkowe stany, środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Europa Zachodnia, Europa Północna, Wielka Brytania Zachodnia, Wielka Brytania Południowa, Australia Wschodnia, Australia Południowo-Wschodnia i Azja południowo-wschodnia. Obszar roboczy analizy dzienników musi istnieć w zachodnie centralnej nam wschodnie stany USA, Europa Zachodnia, Wielka Brytania Południowa, Południowo-Wschodnia Australia albo regionu Azja południowo-wschodnia.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Można włączyć przepływ grup NSG dzienniki znajdować się w różnych regionach niż obszar Mój obszar roboczy?

Tak, te grupy NSG można w różnych regionach niż obszaru roboczego analizy dzienników.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Można skonfigurować wiele grup NSG w obrębie jednego obszaru roboczego?

Tak.

## <a name="can-i-use-an-existing-workspace"></a>Można użyć istniejącego obszaru roboczego?

Tak. W przypadku wybrania istniejący obszar roboczy, upewnij się, że został już zmigrowany do nowego języka zapytań. Jeśli nie chcesz uaktualnić obszaru roboczego, musisz utworzyć nowy. Aby uzyskać więcej informacji dotyczących nowego języka zapytań, zobacz [Analiza dzienników Azure uaktualnienia do nowego wyszukiwania dziennika](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-operations-management-suite-workspace-be-in-a-different-subscription"></a>Można moim koncie magazynu Azure w ramach jednej subskrypcji i Mój obszar roboczy usługi Operations Management Suite jest w innej subskrypcji?

Tak, w jednej subskrypcji można konta magazynu Azure i obszar roboczy usługi Operations Management Suite może znajdować się w innej subskrypcji.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Czy mogę przechowywać raw dzienniki w innej subskrypcji

Nie. Nieprzetworzona dzienniki można przechowywać w dowolne konto magazynu, jeśli grupa NSG jest włączona dzienniki przepływu. Jednak zarówno dzienniki raw, jak i konto magazynu musi być w tej samej subskrypcji i regionu.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co zrobić, jeśli nie można skonfigurować grupy NSG do analizy ruchu z powodu błędu "Nie została odnaleziona"?

Wybierz obsługiwany region. W przypadku wybrania z systemem innym niż obsługiwany region komunikat o błędzie "Nie została odnaleziona". Obsługiwane regiony są wymienione we wcześniejszej części tego artykułu.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co zrobić, jeśli otrzymuję stan "nie można załadować," w obszarze strony dzienniki przepływu NSG?

Dostawca elemencie Microsoft.Insights musi być zarejestrowana do przepływu rejestrowania działała poprawnie. Jeśli nie masz pewności, czy dostawca elemencie Microsoft.Insights jest zarejestrowany dla Twojej subskrypcji, Zastąp *xxxxx-xxxxx-xxxxxx-xxxx* w następujące polecenie, a następnie uruchom następujące polecenia ze środowiska PowerShell:

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Konfigurowanie rozwiązania zostało. Dlaczego nie widzę żadnych czynności na pulpicie nawigacyjnym?

Pulpit nawigacyjny może potrwać do 30 minut pojawiają się po raz pierwszy. Rozwiązanie musi najpierw agregacji wystarczającej ilości danych dla niej pochodzić wgląd w istotne dane. Następnie generuje raporty. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co zrobić, jeśli otrzymuję ten komunikat: "nie znaleziono żadnych danych w tym obszarze roboczym dla wybranego okresu. Spróbuj zmienić okres lub wybierz inny obszar roboczy. "?

Spróbuj następujących opcji:
- Zmień czas na górnym pasku.
- Wybierz inny obszar roboczy analizy dzienników na górnym pasku.
- Spróbuj uzyskać dostęp do analizy ruchu po 30 minutach, jeśli niedawno została włączona.
    
Jeśli problemy będą się powtarzać, Zgłoś problemy w [forum głos użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co w przypadku otrzymania tego komunikatu: "analizowanie danych Twojej grupy NSG przepływać dzienników po raz pierwszy. Ten proces może potrwać 20-30 minut. Sprawdź ponownie za jakiś czas. 2) Jeśli nie działa powyższy krok i obszaru roboczego znajduje się w bezpłatnej wersji produktu, sprawdź wykorzystanie sieci obszaru roboczego, można sprawdzić poprawności za pośrednictwem przydziału, przeciwnym wypadku zapoznaj się często zadawane pytania w celu uzyskania dodatkowych informacji. "?

Ten komunikat zostanie wyświetlony, ponieważ:
- Analiza ruchu niedawno została włączona i może nie jeszcze ma łączy wystarczającej ilości danych dla niej pochodzić wgląd w istotne dane.
- Korzystania z bezpłatnej wersji obszaru roboczego usługi Operations Management Suite i przekroczył limit przydziału. Może być konieczne używanie obszaru roboczego o większej pojemności.
    
Jeśli problemy będą się powtarzać, Zgłoś problemy w [forum głos użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co w przypadku otrzymania tego komunikatu: "wygląda na to mamy danych zasobów (topologii) i żadne informacje przepływów. W tym samym czasie, kliknij tutaj, aby wyświetlić dane zasobów i dotyczą — często zadawane pytania, aby uzyskać więcej informacji. "?

Pojawia się informacje o zasobach na pulpicie nawigacyjnym; jednak nie statystyki związane z przepływem są obecne. Dane mogą nie być obecny ze względu na nie przepływów komunikacji między zasobami. Poczekaj 60 minut, a następnie ponownie sprawdzić stan. Jeśli problem będzie się powtarzać, a masz pewności, czy istnieją przepływów komunikacji między zasobami, budzić zastrzeżenia co w [forum głos użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Można skonfigurować przy użyciu programu PowerShell analizy ruchu lub szablonu usługi Azure Resource Manager lub klienta?

Analiza ruchu można skonfigurować przy użyciu środowiska Windows PowerShell z wersji 6.2.1 i jego nowszych wersjach. Aby skonfigurować rejestrowanie przepływu i analiza ruchu dla określonej grupy NSG, za pomocą polecenia cmdlet Set, zobacz [AzureRmNetworkWatcherConfigFlowLog zestawu](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0). Rejestrowanie przepływu i stan analizy ruchu dla określonej grupy NSG, można znaleźć [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0).

Obecnie nie można użyć szablonu usługi Azure Resource Manager do konfigurowania analizy ruchu.

Aby skonfigurować analizy ruchu przy użyciu klienta usługi Azure Resource Manager, zobacz poniższe przykłady.

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
**Pobierz przykładowe polecenia cmdlet:**
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



## <a name="how-is-traffic-analytics-priced"></a>Jak kosztuje analizy ruchu

Analiza ruchu taryfowych. Pomiaru opiera się na przetwarzanie przepływu danych dziennika przez usługę, a także przechowywanie powstałe w ten sposób zaawansowane dzienniki w obszarze roboczym analizy dzienników. Aby uzyskać więcej informacji, zobacz [cenową planu](https://azure.microsoft.com/en-us/pricing/details/network-watcher/). 

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak można przejść za pomocą klawiatury w widoku mapy geograficznie?

Strona mapy geograficznie zawiera dwóch głównych sekcji:
    
- **Transparent**: banerze w górnej części mapy geograficznie zawiera przyciski, aby wybrać filtry dystrybucji ruchu (na przykład wdrożenia, ruch z innych krajów i złośliwym kodem). Po wybraniu przycisku odpowiednich filtr zostanie zastosowany na mapie. Na przykład w przypadku wybrania przycisku mapy prezentuje active centrów danych w danym wdrożeniu.
- **Mapa**: poniżej transparentu, sekcja mapy zawiera Dystrybucja ruchu między centrach danych platformy Azure i krajów.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacji klawiatury na banerze
    
- Domyślnie zaznaczenie na stronie mapy geograficznie transparentu jest filtru "Kontrolerów domeny platformy Azure".
- Aby przenieść na inny filtr, użyj `Tab` lub `Right arrow` klucza. Aby przejść do tyłu, użyj `Shift+Tab` lub `Left arrow` klucza. Przeglądania do przodu jest od lewej do prawej, a następnie góry do dołu.
- Naciśnij klawisz `Enter` lub `Down` klawisz, aby zastosować wybrany filtr. Na podstawie wyboru filtru i wdrażania, są wyróżnione jednego lub wielu węzłów w sekcji mapy.
- Aby przełączyć między transparent i mapy, naciśnij klawisz `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Nawigacji klawiatury na mapie
    
- Po wybrać dowolny filtr na banerze i naciśnięciu `Ctrl+F6`, fokus do jednego z węzłów wyróżnione (**centrum danych Azure** lub **Kraj/Region**) w widoku mapy.
- Aby przenieść do innych wyróżnione węzły na mapie, użyj `Tab` lub `Right arrow` klucza dla ruch do przodu. Użyj `Shift+Tab` lub `Left arrow` klucza dla ruchu z poprzednimi wersjami.
- Aby wybrać wyróżnione węzły na mapie, użyj `Enter` lub `Down arrow` klucza.
- Wyboru tych węzłów, fokus do **informacji przybornika** dla węzła. Domyślnie fokus do zamkniętego przycisk **informacji przybornika**. Dodatkowo przenoszenia wewnątrz **pole** przeglądać, `Right arrow` i `Left arrow` kluczy, aby przejść do przodu i do tyłu, odpowiednio. Naciśnięcie przycisku `Enter` ma sam efekt co wybierając przycisk ukierunkowanych na **informacji przybornika**.
- Po naciśnięciu `Tab` podczas, gdy fokus jest ustawiony na **informacji przybornika**, fokusu do punktów końcowych na tym samym kontynencie jako wybranego węzła. Użyj `Right arrow` i `Left arrow` klucze do przechodzenia między tymi punktami końcowymi.
- Aby przenieść do innych punktów końcowych przepływu lub klastry kontynencie, użyj `Tab` dla ruch do przodu i `Shift+Tab` dla ruchu z poprzednimi wersjami.
- Gdy fokus jest ustawiony na **klastrów kontynencie**, użyj `Enter` lub `Down` klawisze strzałek, aby wyróżnić punkty końcowe wewnątrz kontynencie klastra. Aby przenieść za pomocą punktów końcowych i przycisku Zamknij w polu informacje kontynencie klastra, użyj `Right arrow` lub `Left arrow` klucza do przeniesienia do przodu i do tyłu, odpowiednio. W dowolnym punkcie końcowym, można użyć `Shift+L` przejść do linii połączenia z wybranego węzła do punktu końcowego. Możesz nacisnąć przycisk `Shift+L` ponownie, aby przejść do wybranego punktu końcowego.
        
### <a name="keyboard-navigation-at-any-stage"></a>Nawigacji klawiatury na każdym etapie
    
- `Esc` Zwija zaznaczenie rozwinięte.
- `Up arrow` Klucza wykonuje ta sama akcja co `Esc`. `Down arrow` Klucza wykonuje ta sama akcja co `Enter`.
- Użyj `Shift+Plus` Aby powiększyć, i `Shift+Minus` Aby pomniejszyć.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak można nawigować przy użyciu klawiatury w widoku topologii sieci wirtualnej?

Na stronie topologia sieci wirtualne zawiera dwóch głównych sekcji:
    
- **Transparent**: banerze w górnej części topologii sieci wirtualnych zapewnia przyciski, aby wybrać filtry dystrybucji ruchu (na przykład połączone sieci wirtualne, odłączonych sieciach wirtualnych i publicznych adresów IP). Po wybraniu przycisku odpowiednich filtr zostanie zastosowany w topologii. Na przykład w przypadku wybrania przycisku topologii prezentuje active sieci wirtualnych w danym wdrożeniu.
- **Topologia**: poniżej transparentu, sekcja topologia przedstawia Dystrybucja ruchu między sieciami wirtualnymi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacji klawiatury na banerze
    
- Domyślnie zaznaczenie na stronie topologia sieci wirtualnych na banerze jest filtr "Połączone sieci wirtualne".
- Aby przenieść do innego filtru, należy użyć `Tab` klawisz, aby przejść do przodu. Aby przejść do tyłu, użyj `Shift+Tab` klucza. Przeglądania do przodu jest od lewej do prawej, a następnie góry do dołu.
- Naciśnij klawisz `Enter` wybrany filtr. Na podstawie wyboru filtru i wdrażania, jednego lub wielu węzłów (sieci wirtualnej) w sekcji topologii są wyróżnione.
- Aby przełączyć między transparentu i topologii, naciśnij klawisz `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacji klawiatury od topologii
    
- Po wybrać dowolny filtr na banerze i naciśnięciu `Ctrl+F6`, fokus do jednego z węzłów wyróżnione (**sieci wirtualnej**) w widoku topologii.
- Aby przenieść do innych wyróżnione węzły w widoku topologii, użyj `Shift+Right arrow` klucza dla ruch do przodu. 
- Na wyróżnione węzły fokus do **informacji przybornika** dla węzła. Domyślnie przenosi fokus **szczegółowe** znajdującego się na **informacji przybornika**. Dodatkowo przenoszenia wewnątrz **pole** przeglądać, `Right arrow` i `Left arrow` kluczy, aby przejść do przodu i do tyłu, odpowiednio. Naciśnięcie przycisku `Enter` ma sam efekt co wybierając przycisk ukierunkowanych na **informacji przybornika**.
- Wyboru tych węzłów, użytkownik może odwiedzić jego połączenia pojedynczo, naciskając `Shift+Left arrow` klucza. Przenieś fokus do **informacji przybornika** tego połączenia. W dowolnym momencie można przesunięte fokus do węzła, naciskając klawisz `Shift+Right arrow` ponownie.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak można nawigować przy użyciu klawiatury w widoku topologii podsieci?

Strona topologii podsieci wirtualne zawiera dwóch głównych sekcji:
    
- **Transparent**: banerze w górnej części topologii podsieci wirtualnych zapewnia przyciski, aby wybrać filtry dystrybucji ruchu (na przykład podsieci aktywny, średni i bramy). Po wybraniu przycisku odpowiednich filtr zostanie zastosowany w topologii. Na przykład w przypadku wybrania przycisku topologii prezentuje active podsieci wirtualnych w danym wdrożeniu.
- **Topologia**: poniżej transparentu, sekcja topologia przedstawia Dystrybucja ruchu między podsieci wirtualnych.
    
### <a name="keyboard-navigation-on-the-banner"></a>Nawigacji klawiatury na banerze
    
- Domyślnie zaznaczenie na stronie topologii podsieci wirtualnych na banerze jest filtr "Podsieci".
- Aby przenieść do innego filtru, należy użyć `Tab` klawisz, aby przejść do przodu. Aby przejść do tyłu, użyj `Shift+Tab` klucza. Przeglądania do przodu jest od lewej do prawej, a następnie góry do dołu.
- Naciśnij klawisz `Enter` wybrany filtr. Na podstawie wyboru filtru i wdrażania, jednego lub wielu węzłów (podsieć) w sekcji topologii są wyróżnione.
- Aby przełączyć między transparentu i topologii, naciśnij klawisz `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Nawigacji klawiatury od topologii
    
- Po wybrać dowolny filtr na banerze i naciśnięciu `Ctrl+F6`, fokus do jednego z węzłów wyróżnione (**podsieci**) w widoku topologii.
- Aby przenieść do innych wyróżnione węzły w widoku topologii, użyj `Shift+Right arrow` klucza dla ruch do przodu. 
- Na wyróżnione węzły fokus do **informacji przybornika** dla węzła. Domyślnie przenosi fokus **szczegółowe** znajdującego się na **informacji przybornika**. Dodatkowo przenoszenia wewnątrz **pole** przeglądać, `Right arrow` i `Left arrow` kluczy, aby przejść do przodu i do tyłu, odpowiednio. Naciśnięcie przycisku `Enter` ma sam efekt co wybierając przycisk ukierunkowanych na **informacji przybornika**.
- Wyboru tych węzłów, użytkownik może odwiedzić jego połączenia pojedynczo, naciskając `Shift+Left arrow` klucza. Przenieś fokus do **informacji przybornika** tego połączenia. W dowolnym momencie można przesunięte fokus do węzła, naciskając klawisz `Shift+Right arrow` ponownie.    

