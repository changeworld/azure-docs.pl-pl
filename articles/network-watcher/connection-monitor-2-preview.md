---
title: Monitor połączeń (wersja zapoznawcza) | Microsoft Docs
description: Informacje dotyczące monitorowania komunikacji sieciowej w środowisku rozproszonym przy użyciu monitora połączeń (wersja zapoznawcza)
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: c993a08a4163d50a9632055da355e39b5bdde004
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026889"
---
# <a name="overview"></a>Przegląd

Monitor połączeń (wersja zapoznawcza) zapewnia ujednolicone kompleksowe funkcje monitorowania połączeń w usłudze Azure Network Watcher na potrzeby wdrożeń hybrydowych i chmurowych platformy Azure. Usługa Azure Network Watcher udostępnia narzędzia do monitorowania, diagnozowania i wyświetlania metryk związanych z łącznością dla wdrożeń platformy Azure.

Najważniejsze przypadki użycia:

- Masz maszynę wirtualną serwera frontonu sieci Web, która komunikuje się z maszyną wirtualną serwera bazy danych w aplikacji wielowarstwowej. Chcesz sprawdzić łączność sieciową między dwiema maszynami wirtualnymi.
- Chcesz, aby maszyny wirtualne w regionie Wschodnie stany USA mogli wysyłać polecenia ping do maszyn wirtualnych w regionie Środkowe stany USA i porównywać opóźnienia sieci między regionami
- W miejscowościach, takich jak Seattle, masz wiele lokalnych lokacji biurowych. Łączenie z adresami URL pakietu Office 365. Chcesz porównać opóźnienia napotkane przez użytkowników korzystających z adresów URL pakietu Office 365 z Seattle i Ashburn.
- Masz skonfigurowaną aplikację hybrydową, która wymaga połączenia z punktem końcowym usługi Azure Storage. Chcesz porównać opóźnienia między lokacją lokalną i aplikacją platformy Azure łączącą się z tym samym punktem końcowym usługi Azure Storage.
- Chcesz sprawdzić łączność z maszyn wirtualnych platformy Azure, które obsługują aplikację w chmurze, w ustawieniach lokalnych.

W tej fazie w wersji zapoznawczej rozwiązanie wiąże się z najlepszymi dwoma kluczami Network Watcher monitor [połączeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) i Network Performance Monitor (npm) [.](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)

Świateł

* Ujednolicone, intuicyjne środowisko dla platform Azure i wymagania dotyczące monitorowania hybrydowego
* Międzyregionowe Monitorowanie łączności między obszarami roboczymi
* Wyższe częstotliwości sondowania i lepszy wgląd w wydajność sieci
* Szybsze generowanie alertów dla wdrożeń hybrydowych
* Obsługa kontroli połączeń HTTP, TCP i ICMP
* Metryki i Log Analytics obsługujące konfiguracje testów platformy Azure i innych niż platformy Azure

![Monitor połączeń](./media/connection-monitor-2-preview/hero-graphic.png)

Wykonaj kroki opisane poniżej, aby rozpocząć monitorowanie przy użyciu monitora połączeń (wersja zapoznawcza)

## <a name="step-1-install-monitoring-agents"></a>Krok 1. Instalowanie agentów monitorowania

Monitor połączeń opiera się na lekkich plikach wykonywalnych do uruchamiania kontroli łączności.  Obsługujemy testy łączności zarówno z platformy Azure, jak i środowisk lokalnych. Określony plik wykonywalny, który ma być używany, zależy od tego, czy maszyna wirtualna jest hostowana na platformie Azure, czy lokalnie.

### <a name="agents-for-azure-virtual-machines"></a>Agenci usługi Azure Virtual Machines

Aby monitor połączeń rozpoznawał maszyny wirtualne platformy Azure jako źródło do monitorowania, należy zainstalować na nich rozszerzenie maszyny wirtualnej Network Watcher Agent (znane także jako rozszerzenie Network Watcher). Rozszerzenie agenta Network Watcher jest wymaganiem do wyzwalania kompleksowego monitorowania i innych zaawansowanych funkcji na maszynach wirtualnych platformy Azure. Można [utworzyć maszynę wirtualną i zainstalować](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[na niej](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)rozszerzenie Network Watcher.  Można również oddzielnie zainstalować, skonfigurować i rozwiązywać problemy z rozszerzeniami Network Watcher dla systemów [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) i [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) .

Jeśli sieciowej grupy zabezpieczeń lub reguły zapory blokują komunikację między źródłem a miejscem docelowym, monitor połączeń wykryje problem i pokaże go jako komunikat diagnostyczny w topologii. Aby włączyć monitorowanie połączeń, upewnij się, że reguły sieciowej grupy zabezpieczeń i zapory zezwalają na pakiety za pośrednictwem protokołu TCP lub ICMP między źródłem a miejscem docelowym.

### <a name="agents-for-on-premise-machines"></a>Agenci dla maszyn lokalnych

W przypadku monitora połączeń do rozpoznawania lokalnych maszyn jako źródeł monitorowania należy zainstalować agenta Log Analytics na maszynach i włączyć rozwiązanie do monitorowania wydajności sieci. Ci agenci są połączeni z obszarami roboczymi Log Analytics i muszą mieć skonfigurowany identyfikator obszaru roboczego i klucz podstawowy przed rozpoczęciem monitorowania.

Aby zainstalować Log Analytics agenta dla maszyn z systemem Windows, postępuj zgodnie z instrukcjami wymienionymi w [tym linku](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Upewnij się, że lokalizacja docelowa jest osiągalna, jeśli w ścieżce znajdują się zapory lub urządzenia sieci wirtualnej (urządzenie WUS).

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Krok 2. Włączanie Network Watcher w ramach subskrypcji

Wszystkie subskrypcje z siecią wirtualną są włączane przy użyciu Network Watcher. Po utworzeniu sieci wirtualnej w ramach subskrypcji Network Watcher zostanie automatycznie włączona w regionie i subskrypcji tego Virtual Network. Nie ma to wpływu na zasoby ani skojarzone opłaty za automatyczne włączanie Network Watcher. Upewnij się, że Network Watcher nie jest jawnie wyłączona w Twojej subskrypcji. Aby uzyskać więcej informacji, zobacz [włączanie Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Krok 3. Tworzenie monitora połączeń 

_Monitor połączeń_ monitoruje komunikację w regularnych odstępach czasu i informuje o zmianach, opóźnieniach i topologii sieci między agentami źródłowymi i docelowymi punktami końcowymi. Źródła mogą być maszynami wirtualnymi platformy Azure lub maszynami lokalnymi z zainstalowanym agentem monitorowania. Docelowymi punktami końcowymi mogą być adresy URL pakietu Office 365, adresy URL Dynamics 365, niestandardowe adresy URL, identyfikatory zasobów maszyn wirtualnych platformy Azure, IPv4, IPv6, nazwy FQDN lub dowolna nazwa domeny.

### <a name="accessing-connection-monitor-preview"></a>Uzyskiwanie dostępu do monitora połączeń (wersja zapoznawcza)

1. Network Watcher dostępu przy użyciu następującego linku:[https://ms.portal.azure.com/?Microsoft\_Azure\_Network\_connectionmonitorpreview=true#blade/Microsoft\_Azure\_Network/NetworkWatcherMenuBlade/connectionMonitorPreview](https://ms.portal.azure.com/?Microsoft_Azure_Network_connectionmonitorpreview=true#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/connectionMonitorPreview)
2. Kliknij kartę "Monitor połączeń (wersja zapoznawcza)" w sekcji monitorowanie w okienku po lewej stronie Network Watcher. Ta karta będzie widoczna tylko wtedy, gdy dostęp do Network Watcher jest uzyskiwany przy użyciu linku określonego w kroku 1.
3. Można wyświetlić wszystkie monitory połączeń, które są tworzone za pomocą funkcji Monitor połączeń (wersja zapoznawcza). Wszystkie monitory połączeń utworzone przy użyciu klasycznego środowiska karty Monitor połączeń będą widoczne na karcie Monitor połączeń.

    ![Tworzenie monitora połączeń](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Tworzenie monitora połączeń

Monitory połączeń utworzone przy użyciu monitora połączeń (wersja zapoznawcza) umożliwiają dodawanie zarówno lokalnych, jak i maszyn wirtualnych platformy Azure jako źródeł i monitorowanie łączności z punktami końcowymi, które mogą obejmować platformę Azure lub dowolny inny adres URL/IP.

Poniżej przedstawiono jednostki w monitorze połączeń:

* Zasób monitora połączeń — zasób platformy Azure określony dla regionu. Wszystkie jednostki wymienione poniżej są właściwościami zasobu monitora połączeń.
* Punkty końcowe — wszystkie źródła i miejsca docelowe, które uczestniczą w sprawdzaniu łączności, są wywoływane jako punkty końcowe. Przykłady punktów końcowych — maszyny wirtualne platformy Azure, agenci lokalną, adresy URL, adresy IP
* Konfiguracja testu — Każda konfiguracja testu jest specyficzna dla protokołu. W zależności od wybranego protokołu można zdefiniować port, progi, częstotliwość testów i inne parametry
* Grupa testowa — każda grupa testowa zawiera źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Każdy monitor połączeń może zawierać więcej niż jedną grupę testową
* Test — kombinacja źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu przetworzy jeden test. Test jest najniższym poziomem, na którym są dostępne dane monitorowania (sprawdzanie nie powiodło się% i RTT)

 ![Tworzenie monitora połączeń](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>Z portalu

Aby utworzyć monitor połączeń, wykonaj poniższe czynności:

1. W obszarze Pulpit nawigacyjny monitora połączeń (wersja zapoznawcza) kliknij pozycję "Utwórz" w lewym górnym rogu.
2. Na karcie Podstawowe wprowadź informacje dotyczące monitora połączeń
   1. Nazwa monitora połączeń — nazwa monitora połączenia. W tym miejscu są stosowane standardowe reguły nazewnictwa dla zasobów platformy Azure.
   2. Subskrypcja — wybierz subskrypcję monitora połączeń.
   3. Region — Wybierz region dla zasobu monitora połączeń. Można wybrać tylko źródłowe maszyny wirtualne, które są tworzone w tym regionie.
   4. Konfiguracja obszaru roboczego — można użyć domyślnego obszaru roboczego utworzonego przez Monitor połączeń do przechowywania danych monitorowania, klikając pole wyboru domyślne. Aby wybrać niestandardowy obszar roboczy, usuń zaznaczenie tego pola. Wybierz subskrypcję i region, aby wybrać obszar roboczy, w którym będą przechowywane dane monitorowania.
   5. Kliknij przycisk "dalej: grupy testowe", aby dodać grupy testowe

      ![Tworzenie monitora połączeń](./media/connection-monitor-2-preview/create-cm-basics.png)

3. Aby dodać grupę testową, na karcie grupy testów kliknij pozycję "+ Grupa testów". Użyj _tworzenia grup testów w monitorze połączeń_ , aby dodać grupy testowe. Kliknij pozycję "Przejrzyj + Utwórz", aby przejrzeć monitor połączeń.

   ![Tworzenie monitora połączeń](./media/connection-monitor-2-preview/create-tg.png)

4. Na karcie "przegląd + tworzenie" Przejrzyj podstawowe informacje i grupy testowe przed utworzeniem monitora połączenia. Aby edytować monitor połączeń z widoku "Recenzja + tworzenie":
   1. Aby edytować podstawowe informacje, użyj ikony ołówka zgodnie z opisem w polu 1 w obrazie 2.
   2. Aby edytować pojedyncze grupy testów, kliknij grupę testową, którą chcesz edytować, aby otworzyć grupę testową w trybie edycji.
   3. Bieżący koszt/miesiąc wskazują koszt w trakcie okresu zapoznawczego. Nie jest obecnie naliczana opłata za korzystanie z monitora połączeń, dlatego w tej kolumnie będzie wyświetlana wartość zero. Rzeczywisty koszt na miesiąc wskazuje cenę, która zostanie obciążona po ogólnej dostępności. Należy pamiętać, że opłaty za pozyskiwanie usługi log Analytics będą stosowane nawet w ramach wersji zapoznawczej.

5. Na karcie "przegląd + tworzenie" kliknij przycisk "Utwórz", aby utworzyć monitor połączeń.

   ![Tworzenie monitora połączeń](./media/connection-monitor-2-preview/review-create-cm.png)

6.  Monitor połączeń (wersja zapoznawcza) utworzy zasób monitora połączeń w tle.

#### <a name="from-armclient"></a>Z Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Polecenie wdrożenia:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Tworzenie grup testowych w monitorze połączeń

Każda grupa testowa w monitorze połączeń zawiera źródła i miejsce docelowe, które są przetestowane w oparciu o parametry sieci sprawdzenia, a w konfiguracji testów nie powiodły się i RTT.

#### <a name="from-portal"></a>Z portalu

Aby utworzyć grupę testową w monitorze połączeń, należy określić wartość poniżej wymienionych pól:

1. Wyłącz grupę testową — zaznaczenie tego pola spowoduje wyłączenie monitorowania dla wszystkich źródeł i miejsc docelowych określonych w grupie testowej. Ta opcja jest domyślnie zaznaczona.
2. Nazwa — nazwa grupy testowej
3. Źródła — możesz określić maszyny wirtualne platformy Azure i maszyny lokalne jako źródła, jeśli agenci są na nich zainstalowani. Zapoznaj się z krok 1, aby zainstalować agenta specyficzny dla Twojego źródła.
   1. Kliknij kartę "agenci platformy Azure", aby wybrać agentów platformy Azure. Zostaną wyświetlone tylko te maszyny wirtualne, które są powiązane z regionem określonym w momencie tworzenia monitora połączeń. Maszyny wirtualne są domyślnie pogrupowane w subskrypcję, do której należą, i grupy są zwijane. Możesz przejść do szczegółów z poziomu subskrypcji na inne poziomy w hierarchii:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      Możesz również zmienić wartość pola "Grupuj według", aby uruchomić drzewo z dowolnego innego poziomu. Na przykład: Grupuj według — Sieć wirtualna będzie wyświetlać maszyny wirtualne z agentami w hierarchii sieci wirtualnych-\&gt; Podsieci-\&gt; Maszyny wirtualne z agentami.

      ![Dodaj źródła](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Kliknij kartę "agenci spoza platformy Azure", aby wybrać agentów lokalnych. Domyślnie w regionie będą widoczne agenci pogrupowani w obszary robocze. Zostaną wyświetlone tylko te obszary robocze, dla których skonfigurowano rozwiązanie Network Performance Monitor. Dodaj rozwiązanie Network Performance Monitor do obszaru roboczego z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Można również użyć procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) . Domyślnie zobaczysz wybrany region na karcie Informacje podstawowe w widoku tworzenie monitora połączeń. Można zmienić region i wybrać opcję agenci z obszarów roboczych z nowo wybranego regionu. Możesz również zmienić wartość pola "Grupuj według", aby grupować w podsieciach.

      ![Źródła spoza platformy Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Kliknij pozycję "Przejrzyj", aby przejrzeć wybrane agenci platformy Azure i spoza platformy Azure.

      ![Przejrzyj źródła](./media/connection-monitor-2-preview/review-sources.png)

   4. Kliknij przycisk "gotowe" po zakończeniu wybierania źródeł.

4. Miejsca docelowe — możesz monitorować łączność z maszynami wirtualnymi platformy Azure lub dowolnym punktem końcowym (publicznym adresem IP, adresem URL, nazwą FQDN) przez określenie ich jako miejsc docelowych. W jednej grupie testowej można dodać maszyny wirtualne platformy Azure, adresy URL usługi O365, adresy URL D365 lub niestandardowe punkty końcowe.

   1. Kliknij kartę maszyny wirtualne platformy Azure, aby wybrać maszyny wirtualne platformy Azure jako miejsca docelowe. Domyślnie maszyny wirtualne platformy Azure pogrupowane w hierarchię subskrypcji znajdują się w tym samym regionie, który został wybrany na karcie Informacje podstawowe w widoku tworzenie monitora połączenia. Możesz zmienić region i wybrać maszyny wirtualne platformy Azure w nowo wybranym regionie. Możesz przejść do szczegółów z poziomu subskrypcji na inne poziomy w hierarchii, takie jak agenci platformy Azure.

      ![Dodaj miejsca docelowe](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Dodaj miejsca docelowe 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Kliknij kartę "punkty końcowe", aby wybrać maszyny wirtualne platformy Azure jako miejsca docelowe. Lista punktów końcowych zostanie wypełniona przy użyciu adresów URL testów usługi O365 i D365, pogrupowanych według nazwy.  Możesz również wybrać punkt końcowy utworzony w innych grupach testowych w tym samym monitorze połączeń. Aby dodać nowy punkt końcowy, kliknij "+ punkt końcowy" w prawym górnym rogu ekranu i podaj adres URL punktu końcowego/IP/FQDN i nazwę

      ![Dodaj punkty końcowe](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Kliknij pozycję "Przejrzyj", aby przejrzeć wybrane agenci platformy Azure i spoza platformy Azure.
   4. Kliknij przycisk "gotowe" po zakończeniu wybierania źródeł.

5. Konfiguracja testu — można skojarzyć dowolną liczbę konfiguracji testów w danej grupie testowej. Portal ogranicza go do jednej konfiguracji testowej na grupę testową, ale aby dodać więcej, użyj Armclient.
   1. Nazwa — nazwa dla konfiguracji testu
   2. Protokół — można wybrać między TCP, ICMP lub HTTP. Aby zmienić protokół HTTP na HTTPS, wybierz pozycję HTTP as Protocol i 443 jako port
   3. Utwórz konfigurację testu sieci — to pole wyboru będzie widoczne tylko w przypadku wybrania opcji HTTP w polu protokół. Włącz to pole, aby utworzyć inną konfigurację testu przy użyciu tych samych źródeł i miejsc docelowych określonych w krokach 3 i 4 za pośrednictwem protokołu TCP/ICMP. Nowo utworzona konfiguracja testu ma nazwę "\&lt; nazwa określona w 5. a\&gt;\_networkTestConfig "
   4. Wyłącz traceroute — to pole będzie stosowane dla grup testowych z protokołem TCP lub ICMP jako protokołem.  Zaznacz to pole, aby zatrzymać odnajdywanie topologii i czas błądzenia przeskoków przez przeskok.
   5. Port docelowy — możesz dostosować to pole tak, aby było umieszczane w wybranym porcie docelowym.
   6. Częstotliwość testów — to pole decyduje o tym, jak często źródła będą wysyłać polecenia ping do miejsc docelowych w protokole i porcie określonym powyżej. Możesz wybrać jedną z 30 sekund, 1 minutę, 5 minut, 15 minut i 30 minut. Źródła przetestują łączność do miejsc docelowych na podstawie wybranej wartości.  Jeśli na przykład wybierzesz 30 sekund, źródła będą sprawdzać łączność z miejscem docelowym co najmniej raz w ciągu 30 sekund — okres.
   7. Progi kondycji — można ustawić progi dla parametrów sieci wymienionych poniżej
      1. Sprawdzanie nie powiodło się w%-procent kontroli nie powiodło się, gdy źródła sprawdzają łączność z miejscem docelowym przy użyciu kryteriów określonych powyżej. W przypadku protokołu TCP/ICMP sprawdzenie nie powiodło się w% może być równe utracie pakietu%. W przypadku protokołu HTTP to pole reprezentuje liczbę żądań HTTP, które nie dotarły do odpowiedzi.
      2. RTT w milisekundach — czas błądzenia w milisekundach, gdy źródła łączą się z miejscem docelowym przez konfigurację testu określoną powyżej.

      ![Dodaj TG](./media/connection-monitor-2-preview/add-test-config.png)

Wszystkie źródła i miejsca docelowe dodane do grupy testowej z określoną konfiguracją testu są podzielone na poszczególne testy. Przykład:

* Grupa testowa: TG1
* Źródła: 3 (A, B, C)
* Miejsca docelowe: 2 (D, E)
* Konfiguracja testu: 2 (Konfiguracja 1, konfiguracja 2)
* Utworzone testy: łącznie = 12

| **Numer testu** | **Element źródłowy** | **Punktu** | **Nazwa konfiguracji testu** |
| --- | --- | --- | --- |
| 1 | A | D | Konfiguracja 1 |
| 2 | A | D | Konfiguracja 2 |
| 3 | A | E | Konfiguracja 1 |
| 4 | A | E | Konfiguracja 2 |
| 5 | B | D | Konfiguracja 1 |
| 6 | B | D | Konfiguracja 2 |
| 7 | B | E | Konfiguracja 1 |
| 8 | B | E | Konfiguracja 2 |
| 9 | C | D | Konfiguracja 1 |
| 10 | C | D | Konfiguracja 2 |
| 11 | C | E | Konfiguracja 1 |
| 12 | C | E | Konfiguracja 2 |

### <a name="scale-limits"></a>Limity skalowania

* Maksymalna liczba monitorów połączeń na subskrypcję na region — 100
* Maksymalna liczba grup testów na monitor połączenia — 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączenia — 100
* Maksymalna liczba konfiguracji testu na monitor połączenia — 20 za pośrednictwem Armclient. 2 za pośrednictwem portalu.

## <a name="step-4--data-analysis-and-alerts"></a>Krok 4. Analiza danych i alerty

Po utworzeniu monitora połączeń źródła sprawdzają łączność z miejscem docelowym na podstawie określonej konfiguracji testu.

### <a name="checks-in-a-test"></a>Sprawdza w teście

W oparciu o protokół wybrany przez użytkownika w konfiguracji testu, monitor połączeń (wersja zapoznawcza) uruchamia serię kontroli dla pary źródłowej miejsca docelowego na wybranej częstotliwości testu.

W przypadku wybrania protokołu HTTP usługa oblicza liczbę odpowiedzi HTTP, które zwróciły kod odpowiedzi, aby określić, że sprawdzenia nie powiodły się%.  Aby obliczyć RTT mierzy czas potrzebny do odebrania odpowiedzi na wywołanie HTTP.

Jeśli wybrano opcję TCP lub ICMP, usługa oblicza pakiet%, aby określić, że sprawdzenia nie powiodły się%. Aby obliczyć RTT mierzy czas potrzebny na odebranie potwierdzenia dla wysłanych pakietów. W przypadku włączenia danych traceroute dla testów sieci można zobaczyć utratę skoku i opóźnienie dla sieci lokalnej.

### <a name="states-of-a-test"></a>Stany testu

Na podstawie danych zwracanych przez testy testowe każdy test może następnie mieć następujące stany:

* Pass = Jeśli rzeczywiste wartości dla operacji Check nie powiodły się%, a czas RTT znajduje się w określonych progach
* Niepowodzenie = Jeśli rzeczywiste wartości dla operacji checks nie powiodły się% lub przekroczenia czasu RTT przez określony próg. Jeśli nie określono progu, test zostanie oznaczony jako zakończony niepowodzeniem w przypadku niepowodzenia sprawdzenia% = 100%
* Ostrzeżenie — gdy nie określono kryteriów dla testów%. W takim przypadku monitor połączeń (wersja zapoznawcza) używa kryterium Autoset jako progu, a gdy próg jest naruszony, stan testu jest ustawiony na "ostrzeżenie"

### <a name="data-collection-analysis-and-alerts"></a>Zbieranie danych, analiza i alerty

Wszystkie dane zebrane przez Monitor połączeń (wersja zapoznawcza) są przechowywane w obszarze roboczym Log Analytics skonfigurowanym w momencie tworzenia monitora połączeń. Dane monitorowania są również dostępne w metrykach Azure Monitor. Możesz użyć Log Analytics, aby zachować swoje dane monitorowania tak długo, jak chcesz, ale Azure Monitor przechowuje metryki domyślnie przez 30 dni **.** Następnie można [ustawić alerty na podstawie metryk dla danych](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Monitorowanie pulpitów nawigacyjnych w rozwiązaniu monitora połączeń

Zostanie wyświetlona lista monitorów połączeń, do których masz dostęp, dla danego wyboru subskrypcji, regionów, sygnatur czasowych, źródła i typów docelowych.

Po przejściu do monitora połączeń (wersja zapoznawcza) z usługi Network Watcher można **wyświetlić**następujące opcje:

* Monitor połączeń (domyślnie) — lista wszystkich monitorów połączeń utworzonych dla wybranych subskrypcji, regionów, sygnatur czasowych, źródła i typów docelowych
* Grupy testów — lista wszystkich grup testowych utworzonych dla wybranych subskrypcji, regionów, sygnatury czasowej, źródła i typów docelowych. Te grupy testowe nie są filtrowane w monitorze połączeń
* Tests — lista wszystkich testów uruchomionych dla wybranych subskrypcji, regionów, sygnatur czasowych, typów źródłowych i docelowych. Te testy nie są filtrowane w monitorze połączeń ani w grupach testowych.

Każdy monitor połączeń można rozwinąć do grup testowych i każdej grupy testowej do różnych poszczególnych testów, które są uruchomione na pulpicie nawigacyjnym. Oznaczone jako [1] na poniższej ilustracji.

Listę można filtrować na podstawie:

* Filtry najwyższego poziomu — subskrypcje, regiony, Źródło sygnatur czasowych i typy docelowe. Oznaczone jako [2] na poniższej ilustracji.
* Filtry oparte na stanie — filtr drugiego poziomu dla stanu monitora/grupy testów/testu. Oznaczone jako [3] na poniższej ilustracji.
* Pole wyszukiwania — wybierz opcję "wszystkie", aby wykonać wyszukiwanie ogólne. Aby wyszukać określoną jednostkę, Użyj listy rozwijanej, aby zawęzić wyniki wyszukiwania. Oznaczone jako [4] na poniższej ilustracji.

![Filtruj testy](./media/connection-monitor-2-preview/cm-view.png)

Przykład:

1. Aby sprawdzić wszystkie testy przez cały monitor połączeń (wersja zapoznawcza), gdzie Source IP = 10.192.64.56:
   1. Zmień widok na "testy"
   2. Przeszukiwanie: 10.192.64.56
   3. Użyj listy rozwijanej obok pozycji wartość, aby wybrać "źródła"
2. Aby odfiltrować tylko testy zakończone niepowodzeniem na wszystkich monitorach połączeń (wersja zapoznawcza), gdzie Source IP = 10.192.64.56
   1. Zmień widok na "testy"
   2. Wybierz pozycję "Niepowodzenie" z filtrów opartych na stanie.
   3. Pole wyszukiwania = 10.192.64.56
   4. Użyj listy rozwijanej obok pozycji wartość, aby wybrać "źródła"
3. Aby odfiltrować tylko testy zakończone niepowodzeniem na wszystkich monitorach połączeń (wersja zapoznawcza), gdzie miejsce docelowe to outlook.office365.com
   1. Zmień widok na "testy"
   2. Wybierz pozycję "Niepowodzenie" z filtrów opartych na stanie.
   3. Pole wyszukiwania = outlook.office365.com
   4. Użyj listy rozwijanej obok pozycji wartość, aby wybrać "miejsca docelowe"

   ![Testy zakończone niepowodzeniem](./media/connection-monitor-2-preview/tests-view.png)

Aby wyświetlić trendy testów zakończonych niepowodzeniem% i RTT dla:

1. Monitor połączeń
   1. Kliknij monitor połączeń, który chcesz szczegółowo zbadać
   2. Domyślnie dane monitorowania można przeglądać według "grup testów"

      ![Wyświetl metryki według](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Wybierz grupę testową, która ma zostać szczegółowo zbadana

      ![Metryki według TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Zobaczysz 5 najważniejszych testów zakończonych niepowodzeniem w przypadku niepowodzenia sprawdzenia,% lub RTT MS dla grupy testowej wybranej w poprzednim kroku. Dla każdego testu zobaczysz linie trendu dla czeków zakończonych niepowodzeniem% i RTT MS
   5. Wybierz test z powyższej listy lub wybierz inny test, aby szczegółowo zbadać.
   6. W przypadku wybranego interwału czasowego dla operacji sprawdzania nie powiodło się, zobaczysz wartości progowe i rzeczywiste. W przypadku czasu RTT MS można zobaczyć wartości progowe, średnie, minimalne i maksymalne.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Zmień przedział czasu, aby wyświetlić więcej danych
  8. Widok można zmienić w kroku b i wybrać opcję wyświetlania według źródeł, miejsc docelowych lub konfiguracji testów. Następnie wybierz źródło na podstawie testów zakończonych niepowodzeniem i zbadaj 5 najważniejszych testów zakończonych niepowodzeniem.  Na przykład: Wybierz widok według: źródła i miejsca docelowe, aby zbadać wszystkie testy, które są wykonywane między tą kombinacją w wybranym monitorze połączenia.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Grupa testowa
   1. Kliknij grupę testową, którą chcesz szczegółowo zbadać
   2. Domyślnie dane monitorowania są wyświetlane według "Źródło + miejsce docelowe + Konfiguracja testowa (test)"

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Wybierz test, który chcesz szczegółowo zbadać
   4. W przypadku wybranego interwału czasowego dla operacji sprawdzania nie powiodło się, zobaczysz wartości progowe i rzeczywiste. W przypadku czasu RTT MS można zobaczyć wartości progowe, średnie, minimalne i maksymalne. Zobaczysz również wyzwolone alerty specyficzne dla wybranego testu.
   5. Zmień przedział czasu, aby wyświetlić więcej danych
   6. Widok można zmienić w kroku b i wybrać opcję wyświetlania według źródeł, miejsc docelowych lub konfiguracji testów. Następnie wybierz jednostkę, aby zbadać 5 najważniejszych testów zakończonych niepowodzeniem.  Na przykład: Wybierz widok według: źródła i miejsca docelowe, aby zbadać wszystkie testy, które są wykonywane między tą kombinacją w wybranym monitorze połączenia.

3. Test
   1. Kliknij konfigurację źródłową i docelową i testową, którą chcesz szczegółowo zbadać
   2. W przypadku wybranego interwału czasowego dla operacji sprawdzania nie powiodło się, zobaczysz wartości progowe i rzeczywiste. W przypadku czasu RTT MS można zobaczyć wartości progowe, średnie, minimalne i maksymalne. Zobaczysz również wyzwolone alerty specyficzne dla wybranego testu.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. Możesz również kliknąć pozycję "topologia", aby wyświetlić topologię sieci w dowolnym momencie.

      ![TEST2](./media/connection-monitor-2-preview/test-topo.png)

   4. Możesz kliknąć dowolny przeskok linku do sieci platformy Azure, aby zobaczyć problemy zidentyfikowane przez Monitor połączeń. Ta funkcja jest obecnie niedostępna dla sieci lokalnych.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Rejestruj zapytania w Azure Monitor Log Analytics

Użyj Log Analytics, aby utworzyć niestandardowe widoki danych monitorowania. Wszystkie dane wyświetlane w interfejsie użytkownika są wypełniane z Log Analytics. Można przeprowadzić interaktywną analizę danych w repozytorium i skorelować dane z różnych źródeł, takich jak kondycja agenta i inne aplikacje oparte na Log Analytics. Możesz również wyeksportować dane do programu Excel, Power BI lub link możliwego do udostępniania.

#### <a name="metrics-in-azure-monitor"></a>Metryki w usłudze Azure Monitor

W przypadku monitora połączeń, który został utworzony przed rozpoczęciem pracy z monitorem połączeń (wersja zapoznawcza), dostępne są wszystkie 4 metryki. W przypadku monitorów połączeń utworzonych za pomocą funkcji Monitor połączeń (wersja zapoznawcza) dane będą dostępne tylko dla metryk oznaczonych za pomocą "(wersja zapoznawcza)".

Typ zasobu — Microsoft. Network/networkWatchers/connectionMonitors

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % Sond nie powiodło się | Procent | Średnia | % sond monitorowania łączności nie powiodło się | Nie wymiarów |
| AverageRoundtripMs | Średni czas błądzenia (MS) | MilliSeconds | Średnia | Średni czas błądzenia sieci (MS) dla sond monitorowania łączności przesyłanych między źródłem a miejscem docelowym |             Nie wymiarów |
| ChecksFailedPercent (wersja zapoznawcza) | % Sprawdzenia nie powiodło się (wersja zapoznawcza) | Procent | Średnia | % kontroli nie powiodło się dla testu |List:-ConnectionMonitorResourceId-SourceAddress-SourceName-identyfikator sourceresourceid-SourceType-Protocol-DestinationAddress-DestinationName-DestinationResourceId-DestinationType-DestinationPort-TestGroupName-TestConfigurationName- Okolicy |
| RoundTripTimeMs (wersja zapoznawcza) | Czas błądzenia (MS) (wersja zapoznawcza) | MS | Średnia | Czas błądzenia (MS) dla czeków wysyłanych między źródłem a miejscem docelowym. Ta wartość nie jest średnia | List:-ConnectionMonitorResourceId-SourceAddress-SourceName-identyfikator sourceresourceid-SourceType-Protocol-DestinationAddress-DestinationName-DestinationResourceId-DestinationType-DestinationPort-TestGroupName-TestConfigurationName- Okolicy |

 ![Monitorowanie metryk](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Alerty metryk w Azure Monitor

Aby utworzyć alert:

1. Wybieranie zasobu monitora połączeń utworzonego przy użyciu monitora połączeń (wersja zapoznawcza)
2. Upewnij się, że "Metryka" jest wyświetlana jako typ sygnału dla zasobu wybranego w poprzednim kroku
3. W obszarze Dodaj warunek wybierz pozycję Nazwa sygnału jako ChecksFailedPercent (wersja zapoznawcza) lub RoundTripTimeMs (wersja zapoznawcza) i typ sygnału jako metryki. Przykład: Wybierz ChecksFailedPercent (wersja zapoznawcza)
4. Zostaną wyświetlone wszystkie wymiary odpowiednie dla każdej metryki.  Wybierz nazwę wymiaru i wartość wymiaru. Przykład: Wybierz adres źródłowy i podaj adres IP dowolnego źródła związanego z zasobem monitora połączeń wybranym w kroku 1
5. W obszarze logika alertu wybierz:
   1. Typ warunku — statyczny
   2. Warunek i próg
   3. Stopień szczegółowości agregacji i częstotliwość oceny — monitor połączeń (wersja zapoznawcza) aktualizuje dane co 1 minutę.
6.  W obszarze Akcje wybierz grupę akcji
7. Podaj szczegóły alertu
8. Utwórz regułę alertu

   ![Alerty](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Krok 5. diagnozowanie problemów w sieci

Monitor połączeń pomoże Ci zdiagnozować problemy odpowiadające zasobowi monitora połączeń i w sieci. Problemy w sieci hybrydowej zostaną wykryte przez agentów Log Analytics zainstalowanych w kroku 1, a problemy na platformie Azure zostaną wykryte przez rozszerzenie Network Watcher.  Problemy w sieci hybrydowej będą widoczne na stronie Diagnostyka i problemy w sieci platformy Azure będą widoczne w topologii sieci.

W przypadku sieci z lokalnymi maszynami wirtualnymi jako źródła wykrywamy:

* Przekroczono limit czasu żądania
* Punkt końcowy nie został rozpoznany przez system DNS — tymczasowy lub trwały. Nieprawidłowy adres URL.
* Nie znaleziono hostów.
* Źródło nie może połączyć się z miejscem docelowym. Element docelowy jest nieosiągalny za poorednictwem protokołu ICMP.
* Problem związany z certyfikatem — certyfikat klienta wymagany do uwierzytelnienia agenta, lista relokacji certyfikatów jest niedostępna. Nazwa hosta punktu końcowego nie jest zgodna z podmiotem lub alternatywną nazwą podmiotu certyfikatu, brakuje certyfikatu głównego w magazynie zaufanych urzędów certyfikacji komputera lokalnego, certyfikat SSL wygasł/nieprawidłowy/unieważniony, niezgodny

W przypadku sieci z maszynami wirtualnymi platformy Azure wykrywamy następujące źródła:

* Problemy z agentem — Agent został zatrzymany, rozpoznawanie nazw DNS nie powiodło się, brak nasłuchu aplikacji/odbiornika na porcie docelowym, nie można otworzyć gniazda
* Problemy ze stanem maszyny wirtualnej — uruchamianie, zatrzymywanie, zatrzymane, cofanie alokacji, cofnięto przydział, ponowne uruchamianie, nie przydzielono
* Brak wpisu tabeli ARP
* Ruch zablokowany ze względu na problemy z zaporą lokalną, reguły sieciowej grupy zabezpieczeń
* Brama sieci wirtualnej — Brak tras, tunel między dwoma bramami jest odłączony lub nie istnieje lub nie znaleziono drugiej bramy przez tunel, nie znaleziono informacji o komunikacji równorzędnej
* Brak trasy w MS Edge.
* Ruch zatrzymany z powodu tras systemowych lub UDR
* Nie włączono protokołu BGP dla połączenia bramy
* Sondowanie DIP w dół w Load Balancer
