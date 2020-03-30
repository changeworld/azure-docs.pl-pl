---
title: Często zadawane pytania dotyczące usługi Azure Network Watcher | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471860"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Często zadawane pytania dotyczące usługi Azure Network Watcher
Usługa [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) udostępnia zestaw narzędzi do monitorowania, diagnozowania, wyświetlania metryk oraz włączania lub wyłączania dzienników zasobów w sieci wirtualnej platformy Azure. Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi.

## <a name="general"></a>Ogólne

### <a name="what-is-network-watcher"></a>Co to jest usługa Network Watcher?
Kontrola sieci jest przeznaczona do monitorowania i naprawy kondycji sieci składników IaaS (Infrastructure-as-a-Service), która obejmuje maszyny wirtualne, sieci wirtualne, bramy aplikacji, moduły równoważenia obciążenia i inne zasoby w sieci wirtualnej platformy Azure. Nie jest to rozwiązanie do monitorowania infrastruktury PaaS (Platform-as-a-Service) ani uzyskiwania analityki sieci web/mobilnej.

### <a name="what-tools-does-network-watcher-provide"></a>Jakie narzędzia zapewnia Network Watcher?
Network Watcher zapewnia trzy główne zestawy możliwości
* Monitorowanie
  * [Widok topologii](https://docs.microsoft.com/azure/network-watcher/view-network-topology) pokazuje zasoby w sieci wirtualnej i relacje między nimi.
  * [Monitor połączeń](https://docs.microsoft.com/azure/network-watcher/connection-monitor) umożliwia monitorowanie łączności i opóźnień między maszyną wirtualną a innym zasobem sieciowym.
  * [Monitor wydajności sieci](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) umożliwia monitorowanie łączności i opóźnień między architekturami sieci hybrydowych, obwodami usługi Expressroute i punktami końcowymi usługi/aplikacji.  
* Diagnostyka
  * [Weryfikacja przepływu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) umożliwia wykrywanie problemów z filtrowaniem ruchu na poziomie maszyny Wirtualnej.
  * [Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) pomaga zweryfikować trasy ruchu i wykryć problemy z routingiem.
  * [Rozwiązywanie problemów z połączeniem](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) umożliwia sprawdzanie jednorazowej łączności i opóźnienia między maszyną wirtualną a innym zasobem sieciowym.
  * [Przechwytywanie pakietów](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) umożliwia przechwytywanie całego ruchu na maszynie Wirtualnej w sieci wirtualnej.
  * [Rozwiązywanie problemów z siecią VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) uruchamia wiele kontroli diagnostycznych bram sieci VPN i połączeń, aby pomóc w debugowaniu problemów.
* Rejestrowanie
  * [Dzienniki przepływu sieciowej](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sieciowej umożliwiają rejestrowanie całego ruchu w [sieciowych grupach zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Usługa Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) przetwarza dane dziennika przepływu sieciowej sieciowej, umożliwiając wizualizację, zapytanie, analizę i zrozumienie ruchu sieciowego.


Aby uzyskać bardziej szczegółowe informacje, zobacz [stronę Przegląd obserwatora sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Jak działa cennik Network Watcher?
Odwiedź [stronę Cennik](https://azure.microsoft.com/pricing/details/network-watcher/) składników Network Watcher i ich cen.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>W jakich regionach jest obsługiwany/dostępny obserwator sieciowy?
Najnowszą dostępność regionalną można wyświetlić na [stronie Dostępności usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Jakie uprawnienia są potrzebne do korzystania z funkcji Kontrola sieci?
Zobacz listę [uprawnień RBAC wymaganych do korzystania z funkcji Kontrola sieci](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). Do wdrażania zasobów potrzebne są uprawnienia współautora do usługi NetworkWatcherRG (zobacz poniżej).

### <a name="how-do-i-enable-network-watcher"></a>Jak mogę włączyć usługę Network Watcher?
Usługa Kontrola sieci jest [włączona automatycznie](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) dla każdej subskrypcji.

### <a name="what-is-the-network-watcher-deployment-model"></a>Co to jest model wdrażania network watcher?
Zasób nadrzędny obserwatora sieci jest wdrażany z unikatowym wystąpieniem w każdym regionie. Format nazewnictwa: NetworkWatcher_RegionName. Przykład: NetworkWatcher_centralus jest zasobem Obserwatora sieci dla regionu "Central US".

### <a name="what-is-the-networkwatcherrg"></a>Co to jest NetworkWatcherRG?
Zasoby Obserwatora sieci znajdują się w ukrytej grupie zasobów **NetworkWatcherRG,** która jest tworzona automatycznie. Na przykład zasób dzienników przepływu sieciowej sieciowej jest zasobem podrzędnym obserwatora sieci i jest włączony w NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Dlaczego muszę zainstalować rozszerzenie Network Watcher? 
Rozszerzenie Obserwatora sieci jest wymagane dla każdej funkcji, która musi generować lub przechwytywać ruch z maszyny Wirtualnej. 

### <a name="which-features-require-the-network-watcher-extension"></a>Które funkcje wymagają rozszerzenia Network Watcher?
Funkcje Przechwytywania pakietów, rozwiązywania problemów z połączeniami i Monitora połączeń wymagają obecności rozszerzenia Kontrola sieci.

### <a name="what-are-resource-limits-on-network-watcher"></a>Co to są limity zasobów w programie Network Watcher?
Wszystkie [limity usługi można znaleźć](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) na stronie Limity usług.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Dlaczego tylko jedno wystąpienie obserwatora sieci jest dozwolone w każdym regionie? 
Network Watcher po prostu musi być włączony raz dla subskrypcji, aby jego funkcje działały, nie jest to limit usługi.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Jak zarządzać zasobem obserwatora sieci? 
Zasób Obserwator sieci reprezentuje usługę wewnętrznej bazy danych dla obserwatora sieci i jest w pełni zarządzany przez platformę Azure. Klienci nie muszą nim zarządzać. Operacje takie jak przenoszenie nie są obsługiwane w zasobie. Jednak [zasób można usunąć](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## <a name="nsg-flow-logs"></a>Dzienniki przepływu nsg

### <a name="what-does-nsg-flow-logs-do"></a>Do czego prowadzą dzienniki przepływu nsg?
Zasoby sieci platformy Azure można łączyć i zarządzać nimi za pośrednictwem sieciowych grup zabezpieczeń .Azure network resources can be combined and managed through [Network Security Groups (NSG) (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) Dzienniki przepływu nsg umożliwiają rejestrowanie 5-krotki informacji o przepływie o całym ruchu za pośrednictwem nsgs. Dzienniki przepływu nieprzetworzonego są zapisywane na koncie usługi Azure Storage, z którego mogą być dalej przetwarzane, analizowane, wyszukiwane lub eksportowane w razie potrzeby.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Jak używać dzienników przepływu sieciowej sieciowej z kontem magazynu za zaporą?

Aby użyć konta magazynu za zaporą, musisz podać wyjątek dla zaufanych usług firmy Microsoft, aby uzyskać dostęp do konta magazynu:

* Przejdź do konta magazynu, wpisując nazwę konta magazynu w wyszukiwaniu globalnym w portalu lub na [stronie Konta magazynu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* W sekcji **USTAWIENIA** wybierz pozycję **Zapory i sieci wirtualne**
* W "Zezwalaj na dostęp z", wybierz **wybrane sieci**. Następnie w obszarze **Wyjątki**zaznacz pole wyboru **"Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu"** 
* Jeśli jest ona już zaznaczona, nie trzeba wprowadzać żadnych zmian.  
* Znajdź docelową stronę NSG na [stronie przeglądowej dzienników przepływu nsg](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) i włącz dzienniki przepływu nsg z wybranym powyższym kontem magazynu.

Możesz sprawdzić dzienniki magazynu po kilku minutach — powinna być widoczna zaktualizowana sygnatura czasowa lub utworzony nowy plik JSON.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Jak używać dzienników przepływu nsg z kontem magazynu za punktem końcowym usługi?

Dzienniki przepływu sieciowej sieciowej są zgodne z punktami końcowymi usługi bez konieczności dodatkowej konfiguracji. Zapoznaj się z [samouczkiem na temat włączania punktów końcowych usługi](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) w sieci wirtualnej.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Jaka jest różnica między dziennikami przepływu w wersjach 1 & 2?
Dzienniki przepływu w wersji 2 wprowadza pojęcie *Flow State* & przechowuje informacje o bajtach i pakietach przesyłanych. [Dowiedz się więcej](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Następne kroki
 - Przejdź do naszej [strony przeglądu dokumentacji,](https://docs.microsoft.com/azure/network-watcher/) aby zapoznać się z samouczkami, aby rozpocząć korzystanie z usługi Network Watcher.
