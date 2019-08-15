---
title: Rejestrowanie, Inspekcja i widoczność bramy w Australii platformy Azure
description: Jak skonfigurować rejestrowanie, inspekcję i widoczność w regionach australijskich w celu spełnienia określonych wymagań australijskich zasad, przepisów i przepisów obowiązujących dla instytucji rządowych.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: b7a9f28d06b5e921b5f1b8defa151641bb039940
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990214"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Rejestrowanie, Inspekcja i widoczność bramy w Australii platformy Azure

Wykrywanie i reagowanie na zagrożenia bezpieczeństwa cybernetycznymi polega na generowaniu, zbieraniu i analizowaniu danych związanych z działaniem systemu.

Firma Microsoft udostępnia wbudowane narzędzia platformy Azure, które ułatwiają zaimplementowanie rejestrowania, inspekcji i widoczności w celu zarządzania zabezpieczeniami systemów wdrożonych na platformie Azure. Istnieje również architektura referencyjna, która jest wyrównana do wskazówek dotyczących odbiorców australijskiego cybernetycznymi Security Centre (ACSC) oraz zamiaru podręcznika zabezpieczeń informacji (ISM).

Bramy działają jako mechanizmy kontroli przepływu informacji w warstwie sieciowej i mogą również kontrolować informacje na wyższych warstwach modelu Open System Interconnect (OSI). Bramy są niezbędne do kontrolowania przepływów danych między domenami zabezpieczeń i zapobiegania nieautoryzowanemu dostępowi z sieci zewnętrznych. Ze względu na krytyczność bram kontrolujących przepływ informacji między domenami zabezpieczeń, wszelkie niepowodzenia, szczególnie w przypadku wyższych klasyfikacji, mogą mieć poważne konsekwencje. W związku z tym niezawodne mechanizmy powiadamiania pracowników o sytuacjach, które mogą spowodować, że zdarzenia dotyczące zabezpieczeń cybernetycznymi są szczególnie ważne w przypadku bram.

Zaimplementowanie funkcji rejestrowania i tworzenia alertów dla bram może pomóc w wykrywaniu zdarzeń zabezpieczeń cybernetycznymi, próbach włamania i nietypowych wzorców użycia. Ponadto przechowywanie dzienników zdarzeń na osobnym serwerze zabezpieczonym dziennikiem zwiększa trudność atakującej do usunięcia informacji rejestrowania, aby zniszczyć dowód nakierowanych na cybernetycznymi włamania.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Australijskie wymagania dotyczące cybernetycznymi Security Centre (ACSC)

Ogólne wymagania dotyczące zabezpieczeń systemów Wspólnoty są zdefiniowane w podręczniku ACSC Information Security (ISM). Aby ułatwić podmiotom Wspólnoty spełnienie tych wymagań w ramach platformy Azure, *Podręcznik klienta ACSC — Microsoft Azure na* raportach z CERTYFIKATem chronionym i *ACSC — Microsoft Azure* publikacje zawierające szczegółowe informacje dotyczące następujących wymagań do rejestrowania, inspekcji i widoczności:

1. Aby zmniejszyć ryzyko wynikające z używania udostępnionych zasobów w chmurze, podmioty Wspólnoty muszą zadecydować, aby Microsoft Azure dostępne możliwości, w tym Centrum zabezpieczeń Azure, Azure Monitor, Azure Policy i Azure Advisor, aby pomóc podmiotom w wykonywaniu monitorowanie obciążeń platformy Azure w czasie rzeczywistym

2. ACSC zaleca również, aby jednostki Wspólnoty przekazały wszystkie wymagane dzienniki zabezpieczeń do ACSC w całości z australijskiego monitorowania rządowego

3. Aby pomóc w ograniczeniu ryzyka, jednostki Wspólnoty powinny być skonfigurowane w ramach swoich subskrypcji platformy Azure:

    * Włącz usługę Azure Security Centre
    * Uaktualnij do warstwy Standardowa
    * Włącz automatyczną obsługę administracyjną Microsoft Monitoring Agent na obsługiwanych maszynach wirtualnych platformy Azure
    * Regularne przeglądanie, ustalanie priorytetów i łagodzenie zaleceń i alertów dotyczących zabezpieczeń na pulpicie nawigacyjnym Centrum zabezpieczeń

4. Jednostki rządowe muszą włączyć przekazywanie dzienników i zdarzeń z subskrypcji platformy Azure do ACSC, aby zapewnić ACSC z widocznością niezgodności z tymi wskazówkami. Usługa Azure Event Hubs zapewnia możliwość wykonywania przesyłania strumieniowego na zewnętrznym dzienniku do systemów ACSC i lokalnych należących do jednostki Wspólnoty

5. Jednostki Wspólnoty powinny wyrównać rejestrowanie włączane na platformie Azure zgodnie z wymaganiami określonymi w ISM

6. Firma Microsoft przechowuje dzienniki na platformie Azure przez 90 dni. Jednostki klienta muszą implementować system archiwizowania dzienników, aby zapewnić, że dzienniki mogą być przechowywane przez siedem lat w ramach USUWAMY AFDA

7. Jednostki Wspólnoty, które mają lokalne lub oparte na platformie Azure funkcje zabezpieczeń i zarządzania zdarzeniami (SIEM), mogą również przekazywać dzienniki do tych systemów

8. Jednostki Wspólnoty powinny implementować Network Watcher dzienniki przepływu dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i Virtual Machines. Dzienniki te powinny być przechowywane na dedykowanym koncie magazynu zawierającym tylko dzienniki zabezpieczeń, a dostęp do konta magazynu powinien być zabezpieczony przy użyciu kontroli dostępu opartej na rolach

9. Jednostki Wspólnoty muszą implementować wskazówki konsumentów ACSC, aby zapewnić, że obciążenia platformy Azure spełniają wymagania usługi ISM na potrzeby rejestrowania i monitorowania. Jednostki Wspólnoty muszą także skorzystać z możliwości platformy Azure, które pomagają ACSC na otrzymywanie monitorowania w czasie rzeczywistym, alertów i dzienników związanych z australijskim użyciem firmy Microsoft na poziomie instytucji rządowych.

## <a name="architecture"></a>Architektura

Aby dobrze zrozumieć ruch sieciowy wprowadzany i opuszczający środowisko platformy Azure, konieczne jest włączenie rejestrowania w odpowiednim zestawie składników. Zapewnia to pełną widoczność środowiska i udostępnia dane niezbędne do wykonania analizy.

![Architektura monitorowania platformy Azure](media/visibility.png)

## <a name="components"></a>Składniki

Powyższa architektura zawiera składniki dyskretne, które udostępniają funkcję źródeł dziennika, zbierania dzienników, przechowywania dzienników, analizy dzienników lub reagowania na zdarzenia. Ta architektura obejmuje poszczególne składniki, które zwykle są objęte wdrożeniami platformy Azure dostępnym z Internetu.

|Funkcje|Składniki|
|---|---|
|Źródła dzienników|<ul><li>Application Gateway</li><li>VPN Gateway</li><li>Azure Firewall</li><li>Wirtualne urządzenia sieciowe</li><li>Azure Load Balancer</li><li>Maszyny wirtualne</li><li>Serwery systemu nazw domen (DNS)</li><li>Dziennik systemowy i/lub serwery zbierania dzienników</li><li>sieciowych grup zabezpieczeń</li><li>Dziennik aktywności platformy Azure</li><li>Dziennik diagnostyczny platformy Azure</li><li>Azure Policy</li></ul>|
|Zbieranie dzienników|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Przechowywanie dziennika|<ul><li>Azure Storage</li></ul>|
|Analiza dzienników|<ul><li>Azure Security Center (ASC)</li><li>Azure Advisor</li><li>Rozwiązania Log Analytics<ul><li>Analiza ruchu</li><li>Analiza DNS (wersja zapoznawcza)</li><li>Analiza dzienników aktywności</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|Reagowanie na zdarzenia|<ul><li>Alerty platformy Azure</li><li>Azure Automation</li></ul>|
|

Architektura działa przez pierwsze generowanie dzienników z niezbędnych źródeł, a następnie gromadzenie ich do scentralizowanych repozytoriów. Po zebraniu dzienników mogą to być:

* używane przez usługi Azure Analysis Services do uzyskiwania szczegółowych informacji
* przekazanie do systemów zewnętrznych lub
* Zapoznaj się z magazynem w celu długoterminowego przechowywania.

Aby odpowiedzieć na kluczowe zdarzenia lub zdarzenia identyfikowane przez narzędzia analityczne, można skonfigurować alerty, a Automatyzacja opracowana w celu podjęcia odpowiednich działań w celu aktywnego zarządzania i odpowiedzi.

## <a name="general-guidance"></a>Ogólne wytyczne

Podczas wdrażania składników wymienionych w tym artykule są stosowane następujące ogólne wskazówki:

* Zweryfikuj dostępność regionów usług, upewniając się, że wszystkie dane pozostają w zatwierdzonych lokalizacjach i wdróż je w trybie AU Central lub AU Central 2 jako pierwsze preferencje dotyczące chronionych obciążeń

* Zapoznaj się z *raportem certyfikacji platformy Azure-ACSC — chronioną* publikacją 2018 dla stanu certyfikacji poszczególnych usług i wykonaj własne oceny dotyczące wszelkich odpowiednich składników, które nie zostały uwzględnione w raporcie zgodnie z przewodnikiem *klienta ACSC. Microsoft Azure w OCHRONie*

* W przypadku składników, do których nie odwołuje się w tym artykule, jednostki Wspólnoty powinny przestrzegać zasad dotyczących generowania, przechwytywania, analizowania i zachowywania dzienników

* Identyfikowanie i ustalanie priorytetów rejestrowania, inspekcji i widoczności w systemach o wysokiej wartości, a także wszystkich punktów ruchu przychodzącego i wychodzącego do systemów hostowanych na platformie Azure

* Konsolidowanie dzienników i minimalizowanie liczby wystąpień narzędzi do rejestrowania, takich jak konta magazynu, Log Analytics obszary robocze i Event Hubs

* Ograniczanie uprawnień administracyjnych za pośrednictwem kontroli dostępu opartej na rolach

* Korzystanie z uwierzytelniania wieloskładnikowego (MFA) dla kont administrowanie lub Konfigurowanie zasobów na platformie Azure

* Podczas scentralizowanej zbierania dzienników w wielu subskrypcjach upewnij się, że administratorzy mają odpowiednie uprawnienia w każdej subskrypcji

* Upewnij się, że łączność sieciowa i wymagana konfiguracja serwera proxy dla Virtual Machines, w tym wirtualne urządzenia sieciowe (urządzeń WUS), serwery zbierania dzienników i serwery DNS, aby połączyć się z niezbędnymi usługami platformy Azure, takimi jak Log Analytics obszary robocze, Event Hubs i magazyn

* Skonfiguruj Microsoft Monitoring Agent (MMA) do korzystania z protokołu TLS w wersji 1,2

* Używanie Azure Policy do monitorowania i wymuszania zgodności z wymaganiami

* Wymuszaj szyfrowanie wszystkich repozytoriów danych, takich jak magazyn i bazy danych

* Używaj magazynu lokalnie nadmiarowego (LRS) i migawek, aby uzyskać dostęp do kont magazynu i skojarzonych z nimi danych

* Rozważ użycie magazynu geograficznie nadmiarowego (GRS) lub magazynu poza lokacją, aby dostosować je do strategii odzyskiwania po awarii

|Resource|URL|
|---|---|
|Dokumenty zgodności z przepisami i zasadami|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Produkty platformy Azure — regiony australijskie i inne niż regionalne|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional Australia-środkowe, Australia-środkowe-2, Australia Wschodnia, Australia Południowo-Wschodnia](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Dokument Security and Audit Microsoft Azure zarządzanie dziennikami|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Konfiguracja Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>Wskazówki dotyczące składników

Ta sekcja zawiera informacje dotyczące przeznaczenia każdego składnika i jego roli w ogólnej architekturze rejestrowania, inspekcji i widoczności. Dodatkowe linki są udostępniane w celu uzyskania dostępu do przydatnych zasobów, takich jak dokumentacja dokumentacji, przewodniki i samouczki.

## <a name="log-sources"></a>Źródła dzienników

Przed rozpoczęciem analizy można generować alerty lub raportowanie, muszą zostać wygenerowane niezbędne dzienniki. Dzienniki systemu Azure są podzielone na dzienniki kontroli i zarządzania, dzienniki płaszczyzny danych i przetwarzane zdarzenia.

|Type|Opis|
|---|---|
|Dzienniki kontroli/zarządzania|Podaj informacje o Azure Resource Manager operacjach|
|Dzienniki płaszczyzny danych|Podaj informacje o zdarzeniach wywoływanych w ramach użycia zasobów platformy Azure, takich jak dzienniki na maszynie wirtualnej i dzienniki diagnostyki dostępne za pomocą Azure Monitor|
|Zdarzenia przetworzone|Podaj informacje o analizowanych zdarzeniach/alertach, które zostały przetworzone przez platformę Azure, na przykład w przypadku, gdy Azure Security Center przetworzył i przeanalizowano subskrypcje w celu zapewnienia alertów zabezpieczeń|
|

### <a name="application-gateway"></a>Application Gateway

Usługa Azure Application Gateway to jeden z możliwych punktów wejścia w środowisku platformy Azure, dzięki czemu należy przechwytywać informacje związane z połączeniami przychodzącymi komunikacji z aplikacjami sieci Web. Application Gateway mogą zapewnić kluczowe informacje dotyczące użycia aplikacji sieci Web oraz pomagające w wykrywaniu zdarzeń zabezpieczeń cybernetycznymi. Application Gateway wysyła metadane do dziennika aktywności i dzienników diagnostycznych w Azure Monitor, gdzie można go wykorzystać w Log Analytics lub dystrybuowanie do centrum zdarzeń lub konta magazynu.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Application Gateway|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Przewodnik Szybki Start Application Gateway|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>VPN Gateway

VPN Gateway to potencjalny punkt wejścia dla szerokiego zakresu komunikacji w środowisku platformy Azure, na przykład połączenie ze środowiskiem lokalnym i ruchem administracyjnym. Rejestrowanie w bramach sieci VPN zapewnia wgląd i możliwość śledzenia połączeń wykonywanych w środowisku platformy Azure. Rejestrowanie może zapewnić inspekcję i analizę oraz pomóc w wykrywaniu lub sprawdzaniu złośliwych lub nietypowych połączeń. Dzienniki VPN Gateway są wysyłane do dziennika aktywności Azure Monitor, gdzie mogą być wykorzystywane w Log Analytics lub dystrybuowane do centrum zdarzeń lub konta magazynu.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi VPN Gateway|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|Wskazówki VPN Gatewayowe dla instytucji rządowych w Australii|[Konfiguracja usługi Azure VPN Gateway](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Zapora systemu Azure udostępnia kontrolowany punkt wyjścia ze środowiska platformy Azure, a generowane dzienniki, w tym informacje o próbach i pomyślnych połączeniach wychodzących, są ważnym elementem strategii rejestrowania. Te dzienniki mogą sprawdzać, czy systemy działają zgodnie z założeniami, a także ułatwiają wykrywanie złośliwego kodu lub aktorów próbujących połączyć się z nieautoryzowanymi systemami zewnętrznymi. Zapora platformy Azure zapisuje dzienniki w dzienniku aktywności i dziennikach diagnostycznych w Azure Monitor, gdzie mogą być używane w Log Analytics lub dystrybuowane do centrum zdarzeń lub konta magazynu.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Azure Firewall|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|Samouczek: Monitorowanie dzienników i metryk usługi Azure Firewall|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>Wirtualne urządzenia sieciowe (urządzenie WUS)

Urządzeń WUS może służyć do uzupełniania możliwości zabezpieczeń dostępnych natywnie na platformie Azure. Dzienniki wygenerowane w urządzeń WUS mogą być cennymi zasobami w wykrywaniu zdarzeń zabezpieczeń cybernetycznymi i stanowią kluczową część ogólnej strategii rejestrowania, inspekcji i widoczności. Aby przechwycić dzienniki z urządzeń WUS, wykorzystuje Microsoft Monitoring Agent (MMA). W przypadku urządzeń WUS, które nie obsługują instalacji MMA, należy rozważyć użycie dziennika systemowego lub innego serwera zbierania dzienników do przekazywania dzienników.

|Zasoby|Łącze|
|---|---|
|Przegląd sieciowych urządzeń wirtualnych|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Dokumentacja urządzenie WUS|Zapoznaj się z dokumentacją dostawcy dotyczącą wdrożenia odpowiednich urządzenie WUS na platformie Azure|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Dzienniki Azure Load Balancer są używane do uzyskiwania przydatnych informacji o połączeniach i użyciu związanych z systemami wdrożonymi na platformie Azure. Ta wartość może służyć do monitorowania kondycji i dostępności, ale również stanowi kolejny składnik klucza w celu uzyskania niezbędnego wglądu w ruch komunikacyjny i Wykrywanie złośliwych lub nietypowych wzorców ruchu. Azure Load Balancer dzienniki dzienników aktywności i dzienników diagnostycznych w Azure Monitor, w których mogą być wykorzystywane w Log Analytics lub dystrybuowane do centrum zdarzeń lub konta magazynu.

|Zasoby|Łącze|
|---|---|
|Dokumentacja Azure Load Balancer|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|Metryki i Diagnostyka kondycji dla usługa Load Balancer w warstwie Standardowa|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>Maszyny wirtualne

Virtual Machines to punkty końcowe, które wysyłają i odbierają komunikację sieciową, przetwarzają dane i świadczą usługi. Jako Virtual Machines mogą hostować dane lub kluczowe usługi systemowe, gwarantując, że działają prawidłowo i wykrywanie zdarzeń zabezpieczeń cybernetycznymi może być krytyczne. Virtual Machines zbierać różne dzienniki zdarzeń i inspekcji, które mogą śledzić działanie systemu i akcje wykonywane w tym systemie. Dzienniki zebrane na Virtual Machines mogą być przekazywane do Log Analytics obszaru roboczego przy użyciu Microsoft Monitoring Agent, gdzie można je analizować za pomocą Azure Security Center i odpowiednich rozwiązań Log Analytics. Virtual Machines można także zintegrować bezpośrednio z platformą Azure Event Hubs lub SIEM bezpośrednio lub za pomocą serwera zbierania dzienników.

|Zasoby|Łącze|
|---|---|
|Maszyny wirtualne|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Zbieranie danych z Virtual Machines|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|Strumieniowe przesyłanie dzienników maszyn wirtualnych do Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>Serwery usług nazw domen (DNS)

Dzienniki serwera DNS zapewniają najważniejsze informacje dotyczące usług, do których systemy próbują uzyskać dostęp, wewnętrznie lub zewnętrznie. Przechwytywanie dzienników DNS może pomóc w zidentyfikowaniu zdarzenia dotyczącego zabezpieczeń cybernetycznymi oraz zapewnieniu informacji o typie incydentu i systemach, na które może to dotyczyć. Program Microsoft Management Agent (MMA) może być używany na serwerach DNS do przesyłania dalej dzienników do Log Analytics do użycia w DNS Analytics (wersja zapoznawcza).

|Zasoby|Łącze|
|---|---|
|Rozpoznawanie nazw platformy Azure dla sieci wirtualnych|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Dziennik systemowy i serwery zbierania dzienników

Aby odbierać dzienniki z wirtualnych urządzeń sieciowych lub niestandardowych dzienników zabezpieczeń z innych systemów do użycia w ramach SIEM, można wdrożyć dedykowane serwery w ramach usługi Azure sieci wirtualnych. Dzienniki dziennika systemu mogą być zbierane na serwerze dziennika systemu i przekazywane do Log Analytics na potrzeby analizy. Serwer zbierania dzienników jest ogólnym terminem dla każdej agregacji dziennika i możliwości dystrybucji używanej przez Scentralizowane systemy monitorowania lub rozwiązań Siem. Mogą one służyć do uproszczenia architektury sieci i zabezpieczeń oraz filtrowania i agregowania dzienników przed dystrybucją do scentralizowanej funkcji.

|Zasoby|Łącze|
|---|---|
|SYSLOG źródeł danych w usłudze Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|Serwer zbierania dzienników|Szczegółowe informacje na temat monitorowania i architektury SIEM można znaleźć w dokumentacji dostawcy.|
|

### <a name="network-security-groups-nsgs"></a>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)

Sieciowych grup zabezpieczeń kontrolować ruch do i z sieci wirtualnych na platformie Azure. Sieciowych grup zabezpieczeń stosują reguły dla przepływów ruchu, które są dozwolone lub odrzucane, które obejmują ruch na platformie Azure oraz między systemem Azure i sieciami zewnętrznymi, takimi jak lokalne lub internetowe. Sieciowych grup zabezpieczeń są stosowane do podsieci w obrębie sieci wirtualnej lub do poszczególnych interfejsów sieciowych. Aby przechwytywać informacje o ruchu wprowadzanym i opuszczaniu systemów na platformie Azure, dzienniki sieciowej grupy zabezpieczeń można włączyć za pomocą funkcji rejestrowania przepływów sieciowej grupy zabezpieczeń w usłudze Network Watcher. Te dzienniki są używane do tworzenia linii bazowej dla standardowej operacji systemu i są źródłem danych dla Analiza ruchu, które zapewniają szczegółowe informacje o wzorcach ruchu w systemach hostowanych na platformie Azure.

|Zasoby|Łącze|
|---|---|
|Dokumentacja sieciowej grupy zabezpieczeń|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|Samouczek: Rejestruj ruch sieciowy do i z maszyny wirtualnej przy użyciu Azure Portal|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

Dziennik aktywności platformy Azure, który jest częścią Azure Monitor, to dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Dziennik aktywności może pomóc w ustaleniu, kto i kiedy "dla operacji zapisu (PUT, POST, DELETE) wykonanych ***na*** zasobach w ramach subskrypcji. Dziennik aktywności jest kluczowy do śledzenia zmian konfiguracji wprowadzonych w środowisku platformy Azure. Dzienniki aktywności platformy Azure są automatycznie dostępne do użycia w rozwiązaniach Log Analytics i mogą być wysyłane do Event Hubs lub usługi Azure Storage w celu ich przetworzenia lub przechowywania.

|Zasoby|Łącze|
|---|---|
|Dokumentacja dziennika aktywności platformy Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Prześlij strumieniowo dziennik aktywności platformy Azure do Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Dziennik diagnostyczny platformy Azure

Azure Monitor dzienników diagnostycznych są dzienniki wysyłane przez usługę platformy Azure, które zapewniają rozbudowane, częste dane dotyczące operacji tej usługi. Dzienniki diagnostyczne zapewniają wgląd w działanie zasobu na szczegółowym poziomie i mogą być używane do różnych wymagań, takich jak Inspekcja lub Rozwiązywanie problemów. Dzienniki diagnostyczne platformy Azure są automatycznie dostępne do użycia w rozwiązaniach Log Analytics i mogą być wysyłane do Event Hubs lub usługi Azure Storage w celu ich przetworzenia lub przechowywania.

|Zasoby|Łącze|
|---|---|
|Dokumentacja dzienników diagnostycznych platformy Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|Usługi pomocy technicznej dla dzienników diagnostycznych|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy wymusza reguły wdrażania zasobów, takich jak typ, lokalizacja i konfiguracja. Azure Policy można skonfigurować w celu zapewnienia, że zasoby mogą być wdrażane tylko wtedy, gdy są zgodne z wymaganiami. Azure Policy to podstawowy składnik służący do utrzymywania integralności środowiska platformy Azure. Zdarzenia związane z Azure Policy są rejestrowane w dzienniku aktywności platformy Azure i są automatycznie dostępne do użycia w rozwiązaniach Log Analytics lub mogą być wysyłane do usługi Event Hubs lub Azure Storage w celu przetworzenia lub przechowywania.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Azure Policy|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Korzystanie z szablonów Azure Policy i Menedżer zasobów przy użyciu planów platformy Azure|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>Zbieranie dzienników

Dzienniki, które są generowane na podstawie wielu źródeł dzienników, muszą być przechowywane w scentralizowanej lokalizacji na potrzeby ciągłego dostępu i analizy. Platforma Azure udostępnia wiele metod i opcji zbierania dzienników, które mogą być używane w zależności od typu dziennika i wymagań.

### <a name="event-hubs"></a>Event Hubs

Celem centrum zdarzeń jest agregowanie danych dzienników dla różnych źródeł do dystrybucji. W centrum zdarzeń dane dziennika mogą być wysyłane do SIEM, do ACSC w celu zapewnienia zgodności oraz do przechowywania długoterminowego przechowywania.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Event Hubs|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Wskazówki dotyczące Event Hubs i narzędzi zewnętrznych|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics jest częścią Azure Monitor i służy do analizy dzienników. Log Analytics używa obszaru roboczego jako mechanizmu magazynu, w którym można udostępnić dane dzienników dla różnych narzędzi i rozwiązań analitycznych dostępnych w ramach platformy Azure. Program Log Analytics integruje się bezpośrednio z szeroką gamę składników platformy Azure, a także Virtual Machines za pomocą Microsoft Monitoring Agent.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Log Analytics|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|Samouczek: Analizowanie danych w Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Network Watcher

Korzystanie z Network Watcher jest zalecane przez ACSC, aby pomóc w zrozumieniu i przechwytywaniu ruchu sieciowego w ramach subskrypcji platformy Azure. Dzienniki przepływu sieciowej grupy zabezpieczeń zapewniają dane wejściowe rozwiązania Analiza ruchu w Log Analytics, co zapewnia lepszą widoczność, analizę i raportowanie w sposób natywny za pomocą platformy Azure. Network Watcher udostępnia również możliwość przechwytywania pakietów bezpośrednio z Azure Portal bez konieczności logowania się do maszyny wirtualnej. Przechwytywanie pakietów umożliwia tworzenie sesji przechwytywania pakietów do śledzenia ruchu do i z maszyny wirtualnej.

|Zasoby|Łącze|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|Omówienie przechwytywania pakietów|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>Przechowywanie dziennika

W przypadku instytucji rządowych australijskich, dzienniki przechwycone w ramach platformy Azure muszą być przechowywane zgodnie z krajowymi archiwami [urzędu usuwania funkcji administracyjnych programu Australia (AFDA)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx), który określa przechowywanie dzienników do siedmiu lat.

|Lokalizacja dziennika|Okres przechowywania|
|---|---|
|Dziennik aktywności platformy Azure|Do 90 dni|
|Obszar roboczy usługi Log Analytics|Maksymalnie dwa lata|
|Centrum zdarzeń|Do siedmiu dni|
|

Jest odpowiedzialny za zapewnienie, że dzienniki są odpowiednio archiwizowane, tak aby były zgodne z AFDA i innymi wymaganiami prawnymi.

### <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage to repozytorium dzienników do długoterminowego przechowywania na platformie Azure. Usługa Azure Storage może służyć do archiwizowania dzienników z platformy Azure, w tym Event Hubs, dziennika aktywności platformy Azure i dzienników diagnostycznych platformy Azure. Okres przechowywania danych w magazynie może być ustawiony na zero lub może być określony jako liczba dni. Przechowywanie przez zero dni oznacza, że dzienniki są przechowywane w nieskończoność, w przeciwnym razie wartość może być dowolną liczbą dni z przedziału od 1 do 2147483647.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Azure Storage|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Przechwytywanie zdarzeń za pomocą usługi Azure Event Hubs w usłudze Azure Blob Storage lub magazynu usługi Azure Data Lake|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Samouczek: Archiwizowanie metryk platformy Azure i zapisywanie danych w dzienniku przy użyciu usługi Azure Storage|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Replikacja usługi Azure Storage|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|Tworzenie migawki obiektu BLOB|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Analiza dzienników

Po wygenerowaniu i umieszczeniu w scentralizowanej lokalizacji dzienniki muszą być analizowane, aby pomóc w wykrywaniu prób lub udanych zdarzeń zabezpieczeń. W przypadku wykrycia zdarzeń zabezpieczeń Agencja musi mieć możliwość reagowania na te zdarzenia oraz do śledzenia, zawierania i korygowania zagrożeń.

### <a name="azure-security-center-asc"></a>Azure Security Center (ASC)

Azure Security Center zapewnia ujednolicone Zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami. Azure Security Center mogą stosować zasady zabezpieczeń w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Azure Security Center udostępnia pulpity nawigacyjne i analizę dla szerokiego zakresu składników platformy Azure. Użycie Azure Security Center jest określone jako wymóg w wytycznych dla konsumentów ACSC.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Azure Security Center|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|Szybki start: Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>Analiza ruchu

Analiza ruchu to rozwiązanie oparte na chmurze, które zapewnia wgląd w aktywność użytkowników i aplikacji na platformie Azure. Analizy analizy ruchu Network Watcher dzienniki przepływu sieciowej grupy zabezpieczeń, aby uzyskać wgląd w przepływ ruchu na platformie Azure. Analiza ruchu służy do udostępniania pulpitów nawigacyjnych, raportów, analiz i możliwości odpowiedzi na zdarzenia związane z ruchem sieciowym, które są widoczne w sieciach wirtualnych. Analiza ruchu zapewnia znaczący wgląd w szczegółowe dane i pomaga w identyfikowaniu i rozwiązywaniu problemów z bezpieczeństwem cybernetycznymi.

|Zasoby|Łącze|
|---|---|
|Dokumentacja Analiza ruchu|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor analizy zasobów i inne dane, aby zalecać rozwiązania pomagające ulepszyć wydajność, bezpieczeństwo i wysoką dostępność zasobów, a jednocześnie szukasz możliwości zredukowania ogólnych wydatków na platformę Azure. Azure Advisor jest zalecana przez ACSC i oferuje łatwo dostępne i szczegółowe porady dotyczące konfiguracji środowiska platformy Azure.

|Zasoby|Łącze|
|---|---|
|Dokumentacja usługi Azure Advisor|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Wprowadzenie do usługi Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>Analiza DNS (wersja zapoznawcza)

DNS Analytics to Log Analytics rozwiązanie służące do zbierania, analizy i skorelowania dzienników analitycznych i inspekcji systemu Windows DNS oraz innych powiązanych danych. DNS Analytics identyfikuje klientów próbujących rozpoznać złośliwe nazwy domen, nieodświeżone rekordy zasobów, często badane nazwy domen i próg klientów DNS. DNS Analytics zapewnia również wgląd w żądania obciążenia na serwerach DNS i błędy rejestracji dynamicznej usługi DNS. DNS Analytics służy do udostępniania pulpitów nawigacyjnych, raportów, analiz i możliwości odpowiedzi na zdarzenia związanych z zapytaniami DNS w środowisku platformy Azure. DNS Analytics zapewnia znaczący wgląd w szczegółowe dane i pomaga w identyfikowaniu i rozwiązywaniu problemów z bezpieczeństwem cybernetycznymi.

|Zasoby|Łącze|
|---|---|
|Analiza DNS — dokumentacja|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Analiza dzienników aktywności

Activity Log Analytics to Log Analytics rozwiązanie, które ułatwia analizowanie i przeszukiwanie dzienników aktywności platformy Azure w wielu subskrypcjach platformy Azure. Activity Log Analytics służy do zapewniania scentralizowanych pulpitów nawigacyjnych, raportów, analiz i możliwości odpowiedzi na zdarzenia związane z akcjami, które są wykonywane w odniesieniu do zasobów całego środowiska platformy Azure. Activity Log Analytics może pomóc z inspekcją i badaniem.

|Zasoby|Łącze|
|---|---|
|Zbieraj i Analizuj Dzienniki aktywności platformy Azure w usłudze Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>Informacje o zabezpieczeniach i zarządzanie zdarzeniami (SIEM)

SIEM to system, który zapewnia scentralizowany magazyn, inspekcję i analizę dzienników zabezpieczeń, ze zdefiniowanymi mechanizmami do pozyskiwania szerokiego zakresu danych dzienników oraz inteligentnymi narzędziami do analizy, raportowania i wykrywania zdarzeń oraz odpowiedzi. Możesz użyć możliwości SIEM, które obejmują informacje o rejestrowaniu platformy Azure, aby uzupełnić funkcje zabezpieczeń udostępniane natywnie na platformie Azure. Jednostki Wspólnoty mogą wykorzystać SIEM hostowane Virtual Machines na platformie Azure, lokalnie lub jako funkcja oprogramowania jako usługi (SaaS), w zależności od określonych wymagań.

|Zasoby|Łącze|
|---|---|
|Azure (wersja zapoznawcza)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|Dokumentacja SIEM|Zapoznaj się z dokumentacją dostawcy dotyczącą architektury SIEM i wskazówki|
|Używanie Azure Monitor do integrowania z narzędziami SIEM|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Australia cybernetycznymi Security Centre

Australia cybernetycznymi Security Centre (ACSC) jest potencjalnym liderem instytucji rządowych Australii w zakresie bezpieczeństwa narodowego cybernetycznymi. Wiąże się ona ze sobą funkcje zabezpieczeń cybernetycznymi od rządu Australii w celu poprawy odporności cybernetycznymi australijskiej Wspólnoty oraz wspierania gospodarczego i społecznego dobrobytu Australii w wieku cyfrowym. ACSC zaleca, aby jednostki Wspólnoty przesyłali wszystkie wymagane pliki dzienników, zdarzenia i dzienniki wygenerowane przez system do ACSC w całości z australijskiego monitorowania rządowego.

|Zasoby|Łącze|
|---|---|
|Cybernetycznymi witryna sieci Web Centrum zabezpieczeń (Australia)|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Reagowanie na zdarzenia

Generowanie odpowiednich dzienników, zbieranie ich do scentralizowanych repozytoriów i przeprowadzanie analiz zwiększa zrozumienie systemów i udostępnia mechanizmy wykrywania zdarzeń zabezpieczeń cybernetycznymi. Po wykryciu incydentów lub zdarzeń następnym krokiem jest reagowanie na te zdarzenia i wykonywanie działań w celu zapewnienia kondycji systemu i ochrony danych przed naruszeniem. Platforma Azure oferuje kombinację usług, która pozwala na skuteczne reagowanie na wszystkie zdarzenia, które wystąpiły.

### <a name="azure-alerts"></a>Alerty platformy Azure

Za pomocą alertów platformy Azure można powiadamiać pracowników działu pomocy technicznej i zabezpieczeń w odpowiedzi na określone zdarzenia. Pozwala to jednostce Wspólnoty aktywnie reagować na wykrywanie odpowiednich zdarzeń zgłoszonych przez usługi Analysis Services wymienione w tym artykule.

|Zasoby|Łącze|
|---|---|
|Przegląd alertów w Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Odpowiadanie na zdarzenia przy użyciu alertów platformy Azure Monitor|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation umożliwia jednostkom Wspólnoty wyzwalanie akcji w odpowiedzi na zdarzenia. Może to być uruchomienie przechwycenia pakietu na Virtual Machines, uruchomienie przepływu pracy, zatrzymanie lub uruchomienie Virtual Machines lub usług lub przedziały innych zadań. Automatyzacja umożliwia szybkie reagowanie na alerty bez ręcznej interwencji, co zmniejsza czas odpowiedzi i wagę zdarzenia lub zdarzenia.

|Zasoby|Łącze|
|---|---|
|Dokumentacja Azure Automation|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|Przewodnik: Użyj alertu, aby wyzwolić Azure Automation element Runbook|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem dotyczącym [bezpiecznej administracji zdalnej bramy](gateway-secure-remote-administration.md) , aby uzyskać szczegółowe informacje na temat bezpiecznego zarządzania środowiskiem bramy na platformie Azure.
