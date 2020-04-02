---
title: Monitor połączeń (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Dowiedz się, jak monitorować komunikację sieciową w środowisku rozproszonym za pomocą Monitora połączeń (Preview).
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: d926a9f686f0f4c39203b8a217a7c608cfad926e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548118"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitorowanie łączności sieciowej za pomocą Monitora połączeń (wersja zapoznawcza)

Monitor połączeń (w wersji zapoznawczej) zapewnia ujednolicone monitorowanie połączenia end-to-end w usłudze Azure Network Watcher. Funkcja Monitora połączeń (w wersji zapoznawczej) obsługuje wdrożenia w chmurze hybrydowej i platformy Azure. Kontrola sieci udostępnia narzędzia do monitorowania, diagnozowania i wyświetlania metryk związanych z łącznością dla wdrożeń platformy Azure.

Oto kilka przypadków użycia Monitora połączeń (Podgląd):

- Maszyna wirtualna serwera sieci Web frontu komunikuje się z maszyną wirtualną serwera bazy danych w aplikacji wielowarstwowej. Chcesz sprawdzić łączność sieciową między dwoma maszynami wirtualnymi.
- Chcesz, aby maszyny wirtualne w regionie Wschodnie stany USA ping ować maszyny wirtualne w regionie Środkowe stany USA i chcesz porównać opóźnienia sieci między regionami.
- Masz wiele lokalnych biur w Seattle, Waszyngton i w Ashburn w stanie Wirginia. Witryny pakietu Office łączą się z adresami URL usługi Office 365. Dla użytkowników adresów URL usługi Office 365 porównaj opóźnienia między Seattle i Ashburn.
- Aplikacja hybrydowa wymaga łączności z punktem końcowym usługi Azure Storage. Witryna lokalna i aplikacja platformy Azure łączą się z tym samym punktem końcowym usługi Azure Storage. Chcesz porównać opóźnienia lokacji lokalnej z opóźnieniami aplikacji platformy Azure.
- Chcesz sprawdzić łączność między konfiguracjami lokalnymi a maszynami wirtualnymi platformy Azure, które hostuje aplikację w chmurze.

W fazie podglądu Monitor połączeń łączy w sobie dwie najlepsze funkcje: funkcję [Monitor połączeń obserwatora](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) sieci i monitor [łączności usługi](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) Monitor wydajności sieci (NPM).

Oto kilka zalet Monitora połączeń (Podgląd):

* Ujednolicenie, intuicyjne środowisko dla platformy Azure i potrzeb monitorowania hybrydowego
* Monitorowanie łączności międzyregionowej między obszarami roboczymi
* Wyższe częstotliwości sondowania i lepszy wgląd w wydajność sieci
* Szybsze ostrzeganie o wdrożeniach hybrydowych
* Obsługa kontroli łączności opartych na protokołach HTTP, TCP i ICMP 
* Obsługa metryk i usługi Log Analytics zarówno dla konfiguracji testów platformy Azure, jak i innych niż azure

![Diagram przedstawiający interakcje Monitora połączeń z maszynami wirtualnymi platformy Azure, hostami nienawiązanych z platformą Azure, punktami końcowymi i lokalizacjami magazynu danych](./media/connection-monitor-2-preview/hero-graphic.png)

Aby rozpocząć korzystanie z Monitora połączeń (Podgląd) do monitorowania, wykonaj następujące kroki: 

1. Zainstaluj agentów monitorowania.
1. Włącz funkcję Kontrola sieci w ramach subskrypcji.
1. Utwórz monitor połączenia.
1. Konfigurowanie analizy danych i alertów.
1. Diagnozowanie problemów w sieci.

W poniższych sekcjach podano szczegółowe informacje dotyczące tych kroków.

## <a name="install-monitoring-agents"></a>Instalowanie agentów monitorujących

Monitor połączeń opiera się na lekkich plikach wykonywalnych do uruchamiania kontroli łączności.  Obsługuje kontrole łączności zarówno ze środowisk platformy Azure, jak i środowisk lokalnych. Używany plik wykonywalny zależy od tego, czy maszyna wirtualna jest hostowana na platformie Azure, czy lokalnie.

### <a name="agents-for-azure-virtual-machines"></a>Agenci maszyn wirtualnych platformy Azure

Aby Monitor połączeń rozpoznał maszyny wirtualne platformy Azure jako źródła monitorowania, zainstaluj na nich rozszerzenie maszyny wirtualnej agenta kontroli sieci. To rozszerzenie jest również znane jako *rozszerzenie Network Watcher*. Maszyny wirtualne platformy Azure wymagają rozszerzenia do wyzwalania kompleksowego monitorowania i innych zaawansowanych funkcji. 

Rozszerzenie Obserwatora sieciowego można zainstalować podczas [tworzenia maszyny wirtualnej](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Można również osobno zainstalować, skonfigurować i rozwiązać problem z rozszerzeniem Kontrola sieci dla [systemów Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) i [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Reguły sieciowej grupy zabezpieczeń (NSG) lub zapory mogą blokować komunikację między źródłem a miejscem docelowym. Monitor połączeń wykrywa ten problem i pokazuje go jako komunikat diagnostyczny w topologii. Aby włączyć monitorowanie połączeń, upewnij się, że reguły sieciowej grupy sieciowej i zapory zezwalają na pakiety za pomocą protokołu TCP lub ICMP między źródłem a miejscem docelowym.

### <a name="agents-for-on-premises-machines"></a>Agenci maszyn lokalnych

Aby Monitor połączeń rozpoznał komputery lokalne jako źródła monitorowania, zainstaluj agenta usługi Log Analytics na komputerach. Następnie włącz rozwiązanie Monitor wydajności sieci. Agenci ci są połączone z obszarami roboczymi usługi Log Analytics, więc przed rozpoczęciem monitorowania agenci muszą skonfigurować identyfikator obszaru roboczego i klucz podstawowy.

Aby zainstalować agenta analizy dzienników dla maszyn z systemem Windows, zobacz [Rozszerzenie maszyny wirtualnej Usługi Azure Monitor dla systemu Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Jeśli ścieżka zawiera zapory lub sieciowe urządzenia wirtualne (NVA), upewnij się, że miejsce docelowe jest osiągalne.

## <a name="enable-network-watcher-on-your-subscription"></a>Włącz funkcję Obserwatora sieci w ramach subskrypcji

Wszystkie subskrypcje, które mają sieć wirtualną są włączone za pomocą funkcji Kontrola sieci. Podczas tworzenia sieci wirtualnej w ramach subskrypcji funkcja Kontrola sieci jest automatycznie włączana w regionie i subskrypcji sieci wirtualnej. To automatyczne włączenie nie wpływa na zasoby ani nie wiąże się z opłatą. Upewnij się, że kontrola sieci nie jest jawnie wyłączona w subskrypcji. 

Aby uzyskać więcej informacji, zobacz [Włączanie obserwatora sieci .](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="create-a-connection-monitor"></a>Tworzenie monitora połączeń 

Monitor połączeń monitoruje komunikację w regularnych odstępach czasu. Informuje o zmianach w osiągalności i opóźnieniu. Można również sprawdzić bieżącą i historyczną topologię sieci między agentami źródłowymi a docelowymi punktami końcowymi.

Źródłem mogą być maszyny wirtualne platformy Azure lub maszyny lokalne, które mają zainstalowanego agenta monitorowania. Docelowymi punktami końcowymi mogą być adresy URL usługi Office 365, adresy URL Dynamics 365, niestandardowe adresy URL, identyfikatory zasobów maszyn wirtualnych platformy Azure, IPv4, IPv6, FQDN lub dowolną nazwę domeny.

### <a name="access-connection-monitor-preview"></a>Monitor połączeń programu Access (wersja zapoznawcza)

1. Na stronie głównej portalu platformy Azure przejdź do **usługi Network Watcher**.
1. Po lewej stronie w sekcji **Monitorowanie** wybierz pozycję **Monitor połączeń (Podgląd)**.
1. Wszystkie monitory połączeń utworzone w Monitorze połączeń (Podgląd) są widoczne wszystkie monitory połączeń. Aby wyświetlić monitory połączeń utworzone w klasycznym interfejsie Monitora połączeń, przejdź do karty **Monitor połączeń.**

    ![Zrzut ekranu przedstawiający monitory połączeń utworzone w Monitorze połączeń (Wersja zapoznawcza)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Tworzenie monitora połączeń

W monitorach połączeń utworzonych w Monitorze połączeń (Wersja zapoznawcza) można dodać zarówno maszyny lokalne, jak i maszyny wirtualne platformy Azure jako źródła. Te monitory połączeń mogą również monitorować łączność z punktami końcowymi. Punkty końcowe mogą znajdować się na platformie Azure lub innym adresie URL lub adresie IP.

Monitor połączeń (wersja zapoznawcza) obejmuje następujące jednostki:

* **Zasób monitora połączenia** — zasób platformy Azure specyficzny dla regionu. Wszystkie następujące jednostki są właściwości zasobu monitora połączenia.
* **Punkt końcowy** — źródło lub miejsce docelowe, które uczestniczy w kontroli łączności. Przykłady punktów końcowych obejmują maszyny wirtualne platformy Azure, agentów lokalnych, adresy URL i adresy IP.
* **Konfiguracja testu** — konfiguracja specyficzne dla protokołu dla testu. Na podstawie wybranego protokołu można zdefiniować port, progi, częstotliwość testu i inne parametry.
* **Grupa testowa** — grupa zawierająca źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Monitor połączenia może zawierać więcej niż jedną grupę testową.
* **Test** — kombinacja źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu. Test jest najbardziej szczegółowym poziomem, na którym dostępne są dane monitorowania. Dane monitorowania obejmują procent kontroli, które nie powiodły się i czas podróży w obie strony (RTT).

 ![Diagram przedstawiający monitor połączenia, definiujący relację między grupami testowymi a testami](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Tworzenie monitora połączeń z witryny Azure portal

Aby utworzyć monitor połączenia z witryny Azure portal, wykonaj następujące kroki:

1. Na **pulpicie nawigacyjnym Monitora połączeń (Podgląd)** w lewym górnym rogu wybierz pozycję **Utwórz**.
1. Na karcie **Podstawy** wprowadź informacje dotyczące monitora połączenia:
   * **Nazwa monitora połączeń** — dodaj nazwę monitora połączenia. Użyj standardowych reguł nazewnictwa dla zasobów platformy Azure.
   * **Subskrypcja** — wybierz subskrypcję monitora połączenia.
   * **Region** — wybierz region dla monitora połączenia. Można wybrać tylko źródłowe maszyny wirtualne, które są tworzone w tym regionie.
   * **Konfiguracja obszaru roboczego** — obszar roboczy przechowuje dane monitorowania. Można użyć niestandardowego obszaru roboczego lub domyślnego obszaru roboczego. 
       * Aby użyć domyślnego obszaru roboczego, zaznacz to pole wyboru. 
       * Aby wybrać niestandardowy obszar roboczy, wyczyść to pole wyboru. Następnie wybierz subskrypcję i region niestandardowego obszaru roboczego. 
1. U dołu karty wybierz pozycję **Dalej: Grupy testowe**.

   ![Zrzut ekranu przedstawiający kartę Podstawy w Monitorze połączeń](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Na karcie **Grupy testowe** wybierz pozycję **+ Grupa testu**. Aby skonfigurować grupy testowe, zobacz [Tworzenie grup testowych w Monitorze połączeń](#create-test-groups-in-a-connection-monitor). 
1. U dołu karty wybierz pozycję **Dalej: Przejrzyj + utwórz,** aby przejrzeć monitor połączenia.

   ![Zrzut ekranu przedstawiający kartę Grupy testowe i okienko, w którym dodajesz szczegóły grupy testowej](./media/connection-monitor-2-preview/create-tg.png)

1. Na karcie **Recenzja + tworzenie** przejrzyj podstawowe informacje i grupy testowe przed utworzeniem monitora połączenia. Jeśli chcesz edytować monitor połączenia:
   * Aby edytować podstawowe szczegóły, wybierz ikonę ołówka.
   * Aby edytować grupę testową, zaznacz ją.

   > [!NOTE] 
   > Karta **Recenzja + tworzenie** pokazuje koszt miesięczny podczas etapu podglądu Monitora połączenia. Obecnie kolumna **BIEŻĄCY KOSZT** nie jest obciążna. Gdy Monitor połączenia stanie się ogólnie dostępny, w tej kolumnie zostanie wyświetlenie miesięcznej opłaty. 
   > 
   > Nawet na etapie podglądu Monitora połączenia obowiązują opłaty za pozyskiwania w usłudze Log Analytics.

1. Gdy będziesz gotowy do utworzenia monitora połączenia, u dołu karty **Recenzja + utwórz** wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający Monitor połączenia z kartą Recenzja + tworzenie](./media/connection-monitor-2-preview/review-create-cm.png)

Monitor połączeń (Podgląd) tworzy zasób monitora połączeń w tle.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Tworzenie monitora połączeń przy użyciu funkcji ARMClient

Użyj następującego kodu, aby utworzyć monitor połączenia przy użyciu ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

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

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Oto polecenie wdrażania:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Tworzenie grup testowych na monitorze połączenia

Każda grupa testowa na monitorze połączenia zawiera źródła i miejsca docelowe, które są testowane pod względem parametrów sieci. Są one testowane pod kątem procentu kontroli, które nie powiodły się, a RTT w konfiguracjach testowych.

Z witryny Azure Portal, aby utworzyć grupę testową na monitorze połączenia, należy określić wartości dla następujących pól:

* **Wyłącz grupę testową** — można wybrać to pole, aby wyłączyć monitorowanie wszystkich źródeł i miejsc docelowych określanych przez grupę testową. Ten wybór jest domyślnie wyczyszczony.
* **Nazwa** — nazwij grupę testową.
* **Źródła** — można określić zarówno maszyny wirtualne platformy Azure, jak i maszyny lokalne jako źródła, jeśli są zainstalowane na nich agenci. Aby zainstalować agenta źródła, zobacz [Instalowanie agentów monitorowania](#install-monitoring-agents).
   * Aby wybrać agentów platformy Azure, wybierz kartę **Agenci platformy Azure.** W tym miejscu są widoczne tylko maszyny wirtualne, które są powiązane z regionem określonym podczas tworzenia monitora połączenia. Domyślnie maszyny wirtualne są grupowane w subskrypcji, do której należą. Grupy te są zwinięte. 
   
       Można przejść do szczegółów z poziomu subskrypcji do innych poziomów w hierarchii:

      **Grupy** > zasobów subskrypcji**Maszyny** > **wirtualne** > **podsieci** > **z agentami**

      Można również zmienić wartość pola **Grupa według,** aby rozpocząć drzewo z dowolnego innego poziomu. Na przykład jeśli grupujesz według sieci wirtualnej, zobaczysz maszyny wirtualne, które mają agentów w hierarchii maszyn**wirtualnych podsieci** >  **ednetów** > **z agentami**.

      ![Zrzut ekranu przedstawiający Monitor połączeń z panelem Dodawanie źródeł i kartą Agenci platformy Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Aby wybrać agentów lokalnych, wybierz kartę **Agenci nienawiązywać się z platformą Azure.** Domyślnie agenci są grupowani w obszary robocze według regionów. Wszystkie te obszary robocze mają skonfigurowane rozwiązanie Monitor wydajności sieci. 
   
       Jeśli chcesz dodać Monitor wydajności sieci do obszaru roboczego, pobierz go z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Aby uzyskać informacje dotyczące dodawania Monitora wydajności sieci, zobacz [Monitorowanie rozwiązań w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       W widoku **Tworzenie Monitora połączeń** na karcie **Podstawy zaznaczony** jest domyślny region. Jeśli zmienisz region, możesz wybrać agentów z obszarów roboczych w nowym regionie. Można również zmienić wartość pola **Grupa według,** aby pogrupować według podsieci.

      ![Zrzut ekranu przedstawiający Monitor połączeń z panelem Dodawanie źródeł i kartą Agenci nienawiązywani na platformie Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Aby przejrzeć wybrane agenty platformy Azure i nienawiązane z platformą Azure, przejdź do karty **Recenzja.**

      ![Zrzut ekranu przedstawiający Monitor połączeń z panelem Dodawanie źródeł i kartą Recenzja](./media/connection-monitor-2-preview/review-sources.png)

   * Po zakończeniu konfigurowania źródeł u dołu panelu **Dodaj źródła** wybierz pozycję **Gotowe**.

* **Miejsca docelowe** — można monitorować łączność z maszynami wirtualnymi platformy Azure lub dowolnym punktem końcowym (publicznym adresem IP, adresem URL lub FQDN), określając je jako miejsca docelowe. W jednej grupie testowej można dodać maszyny wirtualne platformy Azure, adresy URL usługi Office 365, adresy URL Dynamics 365 i niestandardowe punkty końcowe.

    * Aby wybrać maszyny wirtualne platformy Azure jako miejsca docelowe, wybierz kartę **Maszyny wirtualne platformy Azure.** Domyślnie maszyny wirtualne platformy Azure są pogrupowane w hierarchię subskrypcji, która znajduje się w tym samym regionie, który został wybrany w widoku **Monitora połączeń tworzenia** na karcie **Podstawy.** Możesz zmienić region i wybrać maszyny wirtualne platformy Azure z nowo wybranego regionu. Następnie można przejść do szczegółów z poziomu subskrypcji do innych poziomów w hierarchii, takich jak poziom agentów platformy Azure.

       ![Zrzut ekranu przedstawiający okienko Dodawanie miejsc docelowych z kartą Maszyny wirtualne platformy Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Zrzut ekranu przedstawiający okienko Dodawanie miejsc docelowych przedstawiające poziom subskrypcji](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Aby wybrać punkty końcowe jako miejsca docelowe, wybierz kartę **Punkty końcowe.** Lista punktów końcowych zawiera adresy URL testów usługi Office 365 i adresy URL testów Dynamics 365, pogrupowane według nazwy. Oprócz tych punktów końcowych można wybrać punkt końcowy, który został utworzony w innych grupach testowych na tym samym monitorze połączenia. 
    
        Aby dodać nowy punkt końcowy, w prawym górnym rogu wybierz pozycję **+ Punkty końcowe**. Następnie podaj nazwę punktu końcowego i adres URL, adres IP lub FQDN.

       ![Zrzut ekranu przedstawiający miejsce dodawania punktów końcowych jako miejsc docelowych w Monitorze połączeń](./media/connection-monitor-2-preview/add-endpoints.png)

    * Aby przejrzeć wybrane maszyny wirtualne platformy Azure i punkty końcowe, wybierz kartę **Recenzja.**
    * Po zakończeniu wybierania miejsc docelowych wybierz pozycję **Gotowe**.

* **Konfiguracje testowe** — konfiguracje testów można skojarzyć w grupie testowej. Witryna Azure portal zezwala tylko na jedną konfigurację testu na grupę testów, ale można użyć ARMClient, aby dodać więcej.

    * **Nazwa** — nazwij konfigurację testu.
    * **Protokół** — wybierz protokół TCP, ICMP lub HTTP. Aby zmienić protokół HTTP na HTTPS, wybierz protokół **HTTP** jako protokół i wybierz jako port **443.**
        * **Utwórz konfigurację testu sieciowego** — to pole wyboru jest wyświetlane tylko wtedy, gdy w polu **Protokół** zostanie wybrany **protokół HTTP.** Zaznacz to pole, aby utworzyć inną konfigurację testową, która używa tych samych źródeł i miejsc docelowych, które zostały określone w innym miejscu w konfiguracji. Nowo utworzona konfiguracja `<the name of your test configuration>_networkTestConfig`testu nosi nazwę .
        * **Wyłącz traceroute** — to pole ma zastosowanie do grup testowych, których protokół to TCP lub ICMP. Zaznacz to pole, aby uniemożliwić źródłom odnajdowanie topologii i rtt hop-by-hop.
    * **Port docelowy** — to pole można dostosować za pomocą wybranego portu docelowego.
    * **Częstotliwość testu** — to pole służy do wybierania częstotliwości wysyłania pingów do miejsc docelowych w określonym protokole i porcie. Możesz wybrać 30 sekund, 1 minutę, 5 minut, 15 minut lub 30 minut. Źródła będą testować łączność z miejscami docelowymi na podstawie wartości, którą wybierzesz.  Na przykład jeśli wybierzesz 30 sekund, źródła sprawdzą łączność z miejscem docelowym co najmniej raz w okresie 30 sekund.
    * **Próg sukcesu** — można ustawić progi dla następujących parametrów sieci:
       * **Sprawdzanie nie powiodło się** — ustaw procent kontroli, które mogą zakończyć się niepowodzeniem, gdy źródła sprawdzają łączność z miejscami docelowymi przy użyciu określonych kryteriów. W przypadku protokołu TCP lub ICMP procent nieudanych kontroli można zrównać z procentem utraty pakietów. W przypadku protokołu HTTP to pole reprezentuje procent żądań HTTP, które nie otrzymały odpowiedzi.
       * **Czas podróży** w obie strony — ustaw RTT w milisekundach, jak długo źródła mogą trwać, aby połączyć się z miejscem docelowym za pomocą konfiguracji testowej.
    
       ![Zrzut ekranu przedstawiający miejsce konfigurowania konfiguracji testowej w Monitorze połączeń](./media/connection-monitor-2-preview/add-test-config.png)

Wszystkie źródła, miejsca docelowe i konfiguracje testów, które można dodać do grupy testowej, są podzielone na poszczególne testy. Oto przykład rozkładu źródeł i miejsc docelowych:

* Grupa testowa: TG1
* Źródła: 3 (A, B, C)
* Miejsca docelowe: 2 (D, E)
* Konfiguracje testowe: 2 (Config 1, Config 2)
* Wszystkich utworzonych testów: 12

| Numer badania | Element źródłowy | Element docelowy | Konfiguracja testowa |
| --- | --- | --- | --- |
| 1 | A | D | Konfigura 1 |
| 2 | A | D | Config 2 (właśc. |
| 3 | A | E | Konfigura 1 |
| 4 | A | E | Config 2 (właśc. |
| 5 | B | D | Konfigura 1 |
| 6 | B | D | Config 2 (właśc. |
| 7 | B | E | Konfigura 1 |
| 8 | B | E | Config 2 (właśc. |
| 9 | C | D | Konfigura 1 |
| 10 | C | D | Config 2 (właśc. |
| 11 | C | E | Konfigura 1 |
| 12 | C | E | Config 2 (właśc. |

### <a name="scale-limits"></a>Limity skali

Monitory połączeń mają następujące limity skali:

* Maksymalna liczba monitorów połączeń na subskrypcję na region: 100
* Maksymalna liczba grup testowych na monitor połączenia: 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączenia: 100
* Maksymalna konfiguracja testowa na monitor połączenia: 
    * 20 przez ARMClient
    * 2 za pośrednictwem portalu Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analizowanie danych monitorowania i ustawianie alertów

Po utworzeniu monitora połączenia źródła sprawdzają łączność z miejscami docelowymi na podstawie konfiguracji testowej.

### <a name="checks-in-a-test"></a>Kontrole w teście

Na podstawie protokołu wybranego w konfiguracji testowej Monitor połączeń (Wersja zapoznawcza) uruchamia serię kontroli pary źródło-miejsce docelowe. Kontrole są uruchamiane zgodnie z wybraną częstotliwością testu.

Jeśli używasz protokołu HTTP, usługa oblicza liczbę odpowiedzi HTTP, które zwróciły kod odpowiedzi. Wynik określa procent nieudanych kontroli. Aby obliczyć RTT, usługa mierzy czas między wywołaniem HTTP a odpowiedzią.

Jeśli używasz protokołu TCP lub ICMP, usługa oblicza procent straty pakietów w celu określenia procentu nieudanych kontroli. Aby obliczyć RTT, usługa mierzy czas pochłonięta otrzymaniem potwierdzenia (ACK) dla pakietów, które zostały wysłane. Jeśli włączono traceroute danych dla testów sieciowych, można zobaczyć hop-by-hop utraty i opóźnienia dla sieci lokalnej.

### <a name="states-of-a-test"></a>Stany badanych

Na podstawie danych, które zwracają kontrole, testy mogą mieć następujące stany:

* **Przebieg** — rzeczywiste wartości dla procentu nieudanych kontroli i RTT mieszczą się w określonych progach.
* **Niepowodzenie** — rzeczywiste wartości procentowe nieudanych kontroli lub RTT przekroczyły określone progi. Jeśli nie określono progu, a następnie test osiągnie Fail stanu, gdy procent nieudanych kontroli jest 100.
* **Ostrzeżenie** — nie określono kryteriów procentowych nieudanych kontroli. W przypadku braku określonych kryteriów Monitor połączeń (Podgląd) automatycznie przypisuje próg. Po przekroczeniu tego progu stan testu zmienia się na Ostrzeżenie.

### <a name="data-collection-analysis-and-alerts"></a>Gromadzenie, analiza i alerty danych

Dane zbierane przez Monitor połączeń (Podgląd) są przechowywane w obszarze roboczym usługi Log Analytics. Ten obszar roboczy został skonfigurowany podczas tworzenia monitora połączenia. 

Dane monitorowania są również dostępne w metrykach usługi Azure Monitor. Usługi Log Analytics umożliwiają przechowywanie danych monitorowania tak długo, jak długo chcesz. Usługa Azure Monitor domyślnie przechowuje metryki tylko przez 30 dni. 

Alerty [oparte na danych](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)można ustawić na podstawie danych .

#### <a name="monitoring-dashboards"></a>Monitorowanie pulpitów nawigacyjnych

Na pulpitach nawigacyjnych monitorowania zostanie wyświetlona lista monitorów połączeń, do których można uzyskać dostęp dla subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych.

Po wybraniu monitora połączenia (podglądu) z obserwatora sieciowego można wyświetlić dane, korzystając z:

* **Monitor połączenia** — lista wszystkich monitorów połączeń utworzonych dla subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych. Ten widok jest domyślny.
* **Grupy testowe** — lista wszystkich grup testowych utworzonych dla subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych. Te grupy testowe nie są filtrowane według monitorów połączeń.
* **Test** — lista wszystkich testów, które są uruchamiane dla subskrypcji, regionów, sygnatur czasowych, źródeł i typów docelowych. Te testy nie są filtrowane według monitorów połączeń lub grup testowych.

Na poniższej ilustracji trzy widoki danych są oznaczone strzałką 1.

Na pulpicie nawigacyjnym można rozwinąć każdy monitor połączenia, aby wyświetlić jego grupy testowe. Następnie można rozwinąć każdą grupę testów, aby wyświetlić testy, które są w niej uruchamiane. 

Listę można filtrować na podstawie:

* **Filtry najwyższego poziomu** — wybierz subskrypcje, regiony, źródła sygnatur czasowych i typy docelowych. Zobacz pole 2 na poniższej ilustracji.
* **Filtry oparte na stanie** — filtrowanie według stanu monitora połączenia, grupy testowej lub testu. Zobacz strzałkę 3 na poniższej ilustracji.
* **Filtry niestandardowe** — wybierz **wybierz wszystkie,** aby wykonać wyszukiwanie ogólne. Aby wyszukiwać według określonej encji, wybierz z listy rozwijanej. Zobacz strzałkę 4 na poniższej ilustracji.

![Zrzut ekranu przedstawiający sposób filtrowania widoków monitorów połączeń, grup testowych i testów w Monitorze połączeń (Wersja zapoznawcza)](./media/connection-monitor-2-preview/cm-view.png)

Na przykład, aby przyjrzeć się wszystkim testom w Monitorze połączeń (Podgląd), gdzie źródłowy adres IP to 10.192.64.56:
1. Zmień widok na **Test**.
1. W polu wyszukiwania wpisz *10.192.64.56*
1. Z listy rozwijanej wybierz pozycję **Źródła**.

Aby wyświetlić tylko nieudane testy w Monitorze połączeń (Wersja zapoznawcza), gdzie źródłowy adres IP to 10.192.64.56:
1. Zmień widok na **Test**.
1. W przypadku filtru opartego na stanie wybierz pozycję **Fail**.
1. W polu wyszukiwania wpisz *10.192.64.56*
1. Z listy rozwijanej wybierz pozycję **Źródła**.

Aby wyświetlić tylko testy, które nie powiodły się w Monitorze połączeń (Podgląd), w którym miejsce docelowe jest outlook.office365.com:
1. Zmień widok na **Test**.
1. W przypadku filtru opartego na stanie wybierz pozycję **Fail**.
1. W polu wyszukiwania wprowadź *outlook.office365.com*
1. Z listy rozwijanej wybierz pozycję **Miejsca docelowe**.

   ![Zrzut ekranu przedstawiający widok filtrowany w celu wyświetlenia tylko nieudanych testów dla miejsca docelowego Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Aby wyświetlić trendy w RTT i procent nieudanych kontroli monitora połączeń:
1. Wybierz monitor połączenia, który chcesz zbadać. Domyślnie dane monitorowania są zorganizowane według grupy testowej.

   ![Zrzut ekranu przedstawiający metryki monitora połączenia wyświetlane przez grupę testową](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Wybierz grupę testów, którą chcesz zbadać.

   ![Zrzut ekranu przedstawiający, gdzie wybrać grupę testową](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Widzisz grupy testowej pięciu najlepszych testów nie powiodło się, na podstawie RTT lub procent nie powiodło się kontroli. Dla każdego testu są widoczne wiersze RTT i trendu dla procentu nieudanych kontroli.
1. Wybierz test z listy lub wybierz inny test do zbadania. Dla przedziału czasu i procentu nieudanych kontroli są widoczne wartości progowe i rzeczywiste. W przypadku RTT są widoczne wartości progowe, średnie, minimalne i maksymalne.

   ![Zrzut ekranu przedstawiający wyniki testu dla RTT i procent nieudanych kontroli](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Zmień przedział czasu, aby wyświetlić więcej danych.
1. Zmień widok, aby wyświetlić źródła, miejsca docelowe lub konfiguracje testowe. 
1. Wybierz źródło na podstawie testów nie powiodło się i zbadać pięć najlepszych testów nie powiodło się. Na przykład wybierz **pozycję Wyświetl według** > **źródeł** i **Wyświetl według** > **miejsc docelowych,** aby zbadać odpowiednie testy na monitorze połączenia.

   ![Zrzut ekranu przedstawiający wskaźniki wydajności dla pięciu najlepszych testów, które nie powiodły się](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Aby wyświetlić trendy w RTT i procent nieudanych kontroli dla grupy testowej:

1. Wybierz grupę testów, którą chcesz zbadać. 

    Domyślnie dane monitorowania są rozmieszczone według źródeł, miejsc docelowych i konfiguracji testów (testów). Później można zmienić widok z grup testowych na źródła, miejsca docelowe lub konfiguracje testowe. Następnie wybierz jednostkę, aby zbadać pięć najlepszych testów nie powiodło się. Na przykład zmień widok na źródła i miejsca docelowe, aby zbadać odpowiednie testy na wybranym monitorze połączenia.
1. Wybierz test, który chcesz zbadać.

   ![Zrzut ekranu przedstawiający, gdzie wybrać test](./media/connection-monitor-2-preview/tg-drill.png)

    Dla przedziału czasu i procentu nieudanych kontroli są widoczne wartości progowe i rzeczywiste. W przypadku RTT są widoczne wartości progowe, średnie, minimalne i maksymalne. Zostaną również wyświetlone odpalone alerty dla wybranego testu.
1. Zmień przedział czasu, aby wyświetlić więcej danych.

Aby wyświetlić trendy w RTT i procent nieudanych kontroli dla testu:
1. Wybierz konfigurację źródła, miejsca docelowego i testu, którą chcesz zbadać.

    Dla przedziału czasu i dla procentu nieudanych kontroli są widoczne wartości progowe i rzeczywiste. W przypadku RTT są widoczne wartości progowe, średnie, minimalne i maksymalne. Zostaną również wyświetlone odpalone alerty dla wybranego testu.

   ![Zrzut ekranu przedstawiający metryki dla testu](./media/connection-monitor-2-preview/test-drill.png)

1. Aby wyświetlić topologię sieci, wybierz pozycję **Topologia**.

   ![Zrzut ekranu przedstawiający kartę Topologia sieci](./media/connection-monitor-2-preview/test-topo.png)

1. Aby wyświetlić zidentyfikowane problemy, w topologii wybierz dowolny przeskok w ścieżce. (Te przeskoki to zasoby platformy Azure). Ta funkcja nie jest obecnie dostępna w sieciach lokalnych.

   ![Zrzut ekranu przedstawiający wybrane łącze przeskoku na karcie Topologia](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Rejestrowanie zapytań w usłudze Log Analytics

Użyj usługi Log Analytics, aby utworzyć niestandardowe widoki danych monitorowania. Wszystkie dane wyświetlane przez interfejs użytkownika pochodzą z usługi Log Analytics. Można interaktywnie analizować dane w repozytorium. Skorelować dane z agenta kondycji lub innych rozwiązań, które są oparte na usługi Log Analytics. Wyeksportuj dane do programu Excel lub Power BI lub utwórz łącze współużytkowane.

#### <a name="metrics-in-azure-monitor"></a>Metryki w usłudze Azure Monitor

W monitorach połączeń utworzonych przed monitorowaniem połączeń (wersja zapoznawcza) dostępne są wszystkie cztery metryki: % sondy nie powiodło się, AverageRoundtripMs, ChecksFailedPercent (Wersja zapoznawcza) i RoundTripTimeMs (Wersja zapoznawcza). W monitorach połączeń utworzonych w monitorze połączeń (podgląd) dane są dostępne tylko dla metryk oznaczonych *tagiem (Wersja zapoznawcza)*.

![Zrzut ekranu przedstawiający dane w Monitorze połączeń (wersja zapoznawcza)](./media/connection-monitor-2-preview/monitor-metrics.png)

Podczas korzystania z metryk ustaw typ zasobu jako Microsoft.Network/networkWatchers/connectionMonitors

| Metryka | Nazwa wyświetlana | Jednostka | Typ agregacji | Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (SondaFailedPercent) | % sond nie powiodło się | Procentowe | Średnia | Procent sond monitorowania łączności nie powiodło się. | Brak wymiarów |
| ŚredniaRoundtripMs | Śr. Czas podróży w obie strony (ms) | Milisekund | Średnia | Średnia sieć RTT dla sond monitorowania łączności wysyłanych między źródłem a miejscem docelowym. |             Brak wymiarów |
| ChecksFailedPercent (wersja zapoznawcza) | % kontroli nie powiodło się (wersja zapoznawcza) | Procentowe | Średnia | Procent nieudanych kontroli dla testu. | PowiązanieMonitorResourceId <br>Sourceaddress <br>Nazwaobiektu <br>ŹródłoResourceId <br>SourceType <br>Protocol (Protokół) <br>DestinationAddress (Adres docelowy) <br>Nazwa docelowa <br>DestinationResourceId (łańz miejsca docelowego) <br>Destinationtype <br>Port docelowy <br>Nazwa grupy testowej <br>Nazwa konfiguracji testu <br>Region |
| RoundTripTimeMs (wersja zapoznawcza) | Czas w obie strony (ms) (wersja zapoznawcza) | Milisekund | Średnia | RTT dla kontroli wysyłanych między źródłem a miejscem docelowym. Ta wartość nie jest uśredniona. | PowiązanieMonitorResourceId <br>Sourceaddress <br>Nazwaobiektu <br>ŹródłoResourceId <br>SourceType <br>Protocol (Protokół) <br>DestinationAddress (Adres docelowy) <br>Nazwa docelowa <br>DestinationResourceId (łańz miejsca docelowego) <br>Destinationtype <br>Port docelowy <br>Nazwa grupy testowej <br>Nazwa konfiguracji testu <br>Region |

#### <a name="metric-alerts-in-azure-monitor"></a>Alerty metryki w usłudze Azure Monitor

Aby utworzyć alert w usłudze Azure Monitor:

1. Wybierz zasób monitora połączeń utworzony w Monitorze połączeń (Podgląd).
1. Upewnij się, że **metryka** jest wyświetlana jako typ sygnału dla monitora połączenia.
1. W **obszarze Dodaj warunek**dla nazwy **sygnału**wybierz pozycję **ChecksFailedPercent(Preview)** lub **RoundTripTimeMs(Preview)**(
1. W przypadku **opcji Typ sygnału**wybierz pozycję **Metryki**. Na przykład wybierz **pozycję ChecksFailedPercent(Preview)**.
1. Zostaną wyświetlone wszystkie wymiary danych. Wybierz nazwę wymiaru i wartość wymiaru. Na przykład wybierz **pozycję Adres źródłowy,** a następnie wprowadź adres IP dowolnego źródła na monitorze połączenia.
1. W **logice alertów**wprowadź następujące szczegóły:
   * **Typ warunku**: **Statyczny**.
   * **Warunek** i **próg**.
   * **Ziarnistość agregacji i częstotliwość oceny:** Monitor połączeń (wersja zapoznawcza) aktualizuje dane co minutę.
1. W **opcji Akcje**wybierz grupę akcji.
1. Podaj szczegóły alertu.
1. Utwórz regułę alertu.

   ![Zrzut ekranu przedstawiający obszar reguły tworzenia w usłudze Azure Monitor; Wyróżnione są "Adres źródłowy" i "Nazwa punktu końcowego źródła"](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnozowanie problemów w sieci

Monitor połączeń (Podgląd) ułatwia diagnozowanie problemów na monitorze połączenia i w sieci. Problemy w sieci hybrydowej są wykrywane przez agentów usługi Log Analytics, które zostały zainstalowane wcześniej. Problemy na platformie Azure są wykrywane przez rozszerzenie Kontrola sieci. 

Można wyświetlić problemy w sieci platformy Azure w topologii sieci.

W przypadku sieci, których źródła są lokalnymi maszynami wirtualnymi, można wykryć następujące problemy:

* Limit czasu żądania.
* Punkt końcowy nie został rozwiązany przez DNS — tymczasowy lub trwały. Adres URL nieprawidłowy.
* Nie znaleziono hostów.
* Źródło nie może połączyć się z miejscem docelowym. Cel nie jest osiągalny za pośrednictwem ICMP.
* Problemy związane z certyfikatami: 
    * Certyfikat klienta wymagany do uwierzytelnienia agenta. 
    * Lista relokacji certyfikatów nie jest dostępna. 
    * Nazwa hosta punktu końcowego nie jest zgodna z podmiotem certyfikatu lub nazwą alternatywną podmiotu. 
    * Brak certyfikatu głównego w magazynie zaufanych urzędów certyfikacji komputera lokalnego źródła. 
    * Certyfikat SSL wygasł, jest nieprawidłowy, odwołany lub niezgodny.

W przypadku sieci, których źródłami są maszyny wirtualne platformy Azure, można wykryć następujące problemy:

* Problemy z agentem:
    * Agent zatrzymany.
    * Nie powiodło się rozpoznawanie dns.
    * Brak aplikacji lub odbiornika nasłuchiwania na porcie docelowym.
    * Nie można otworzyć gniazda.
* Problemy ze stanem maszyny Wirtualnej: 
    * Uruchamianie
    * Zatrzymywanie
    * Zatrzymano
    * Cofanie przydziału
    * Cofnięto przydział
    * Ponowne uruchomienie
    * Nie przydzielono
* Brak wpisu tabeli ARP.
* Ruch został zablokowany z powodu problemów z zaporą lokalną lub reguł sieciowej grupy danych sieciowych.
* Problemy z bramą sieci wirtualnej: 
    * Brakujące trasy.
    * Tunel między dwiema bramami jest odłączony lub brakuje.
    * Druga brama nie została znaleziona przez tunel.
    * Nie znaleziono informacji o komunikacji równorzędnej.
* Trasa zaginęła w programie Microsoft Edge.
* Ruch został zatrzymany z powodu tras systemowych lub UDR.
* Protokół BGP nie jest włączony w połączeniu bramy.
* Sonda DIP znajduje się w dół na moduł równoważenia obciążenia.
