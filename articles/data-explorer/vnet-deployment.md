---
title: Wdróż Eksplorator danych platformy Azure w Virtual Network (wersja zapoznawcza)
description: Dowiedz się, jak wdrożyć usługę Azure Eksplorator danych w Virtual Network
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 293a90591a77825279c8ebbae64516b6126d8621
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588267"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Wdróż Eksplorator danych platformy Azure w Virtual Network (wersja zapoznawcza)

W tym artykule opisano zasoby, które są obecne podczas wdrażania klastra usługi Azure Eksplorator danych w niestandardowym Virtual Network platformy Azure. Te informacje ułatwią Wdrożenie klastra w podsieci w Virtual Network (VNet). Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [co to jest usługa azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![diagram sieci wirtualnej](media/vnet-deployment/vnet-diagram.png)

Usługa Azure Eksplorator danych obsługuje wdrażanie klastra w podsieci Virtual Network (VNet). Ta funkcja umożliwia:

* Wymuś reguły [sieciowej grupy zabezpieczeń](/azure/virtual-network/security-overview) (sieciowej grupy zabezpieczeń) w ruchu klastra Eksplorator danych platformy Azure.
* Połącz sieć lokalną z podsiecią klastra Eksplorator danych platformy Azure.
* Zabezpiecz źródła połączenia danych ([centrum zdarzeń](/azure/event-hubs/event-hubs-about) i [Event Grid](/azure/event-grid/overview)) za pomocą [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

> [!NOTE]
> Integracja Virtual Network i wdrożenie jest w trybie podglądu. Aby włączyć tę funkcję, Otwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Uzyskiwanie dostępu do klastra usługi Azure Eksplorator danych w sieci wirtualnej

Możesz uzyskać dostęp do klastra usługi Azure Eksplorator danych przy użyciu następujących adresów IP dla każdej usługi (usługi zarządzania aparatem i danymi):

* **Prywatny adres IP**: służy do uzyskiwania dostępu do klastra w sieci wirtualnej.
* **Publiczny adres IP**: używany do uzyskiwania dostępu do klastra spoza sieci wirtualnej na potrzeby zarządzania i monitorowania oraz jako adres źródłowy dla połączeń wychodzących uruchomionych z klastra.

Następujące rekordy DNS są tworzone w celu uzyskania dostępu do usługi: 

* `[clustername].[geo-region].kusto.windows.net` (aparat) `ingest-[clustername].[geo-region].kusto.windows.net` (zarządzanie danymi) są mapowane na publiczny adres IP dla każdej usługi. 

* `private-[clustername].[geo-region].kusto.windows.net` (aparat) `private-ingest-[clustername].[geo-region].kusto.windows.net` (zarządzanie danymi) są mapowane na prywatny adres IP dla każdej usługi.

## <a name="plan-subnet-size-in-your-vnet"></a>Planowanie rozmiaru podsieci w sieci wirtualnej

Nie można zmienić rozmiaru podsieci używanej do hostowania klastra Eksplorator danych platformy Azure po wdrożeniu podsieci. W sieci wirtualnej platforma Azure Eksplorator danych używa jednego prywatnego adresu IP dla każdej maszyny wirtualnej i dwóch prywatnych adresów IP dla wewnętrznych modułów równoważenia obciążenia (aparat i zarządzanie danymi). Sieć platformy Azure używa również pięciu adresów IP dla każdej podsieci. Na platformie Azure Eksplorator danych są nadane dwie maszyny wirtualne dla usługi zarządzania danymi. Obsługa maszyn wirtualnych usługi aparatu jest obsługiwana na mocy skalowania konfiguracji użytkownika.

Łączna liczba adresów IP:

| Użycie | Liczba adresów |
| --- | --- |
| Usługa aparatu | 1 na wystąpienie |
| Usługa zarządzania danymi | 2 |
| Wewnętrzne moduły równoważenia obciążenia | 2 |
| Adresy zastrzeżone platformy Azure | 5 |
| **Ogólnego** | **#engine_instances + 9** |

> [!IMPORTANT]
> Rozmiar podsieci musi być zaplanowany z wyprzedzeniem, ponieważ nie można go zmienić po wdrożeniu usługi Azure Eksplorator danych. W związku z tym należy odpowiednio zarezerwować wymagany rozmiar podsieci.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Punkty końcowe usługi do nawiązywania połączenia z usługą Azure Eksplorator danych

[Punkty końcowe usługi platformy Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) umożliwiają zabezpieczenie zasobów wielu dzierżawców platformy Azure w sieci wirtualnej.
Wdrożenie klastra usługi Azure Eksplorator danych w podsieci pozwala na konfigurowanie połączeń danych za pomocą [centrum zdarzeń](/azure/event-hubs/event-hubs-about) lub [Event Grid](/azure/event-grid/overview) podczas ograniczania podstawowych zasobów podsieci Eksplorator danych platformy Azure.

## <a name="dependencies-for-vnet-deployment"></a>Zależności dotyczące wdrożenia sieci wirtualnej

### <a name="network-security-groups-configuration"></a>Konfiguracja sieciowych grup zabezpieczeń

[Sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](/azure/virtual-network/security-overview) zapewniają możliwość kontrolowania dostępu do sieci w ramach połączenia sieciowego. Dostęp do platformy Azure Eksplorator danych można uzyskać przy użyciu dwóch punktów końcowych: HTTPs (443) i TDS (1433). Następujące reguły sieciowej grupy zabezpieczeń muszą zostać skonfigurowane tak, aby zezwalać na dostęp do tych punktów końcowych w celu zarządzania, monitorowania i prawidłowej operacji klastra.

#### <a name="inbound-nsg-configuration"></a>Konfiguracja sieciowej grupy zabezpieczeń ruchu przychodzącego

| **Korzystanie**   | **Wniosek**   | **Do**   | **Protokół**   |
| --- | --- | --- | --- |
| Zarządzanie  |[ADX Management addresses](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (ServiceTag) | Podsieć ADX: 443  | TCP  |
| Monitorowanie kondycji  | [ADX adresy monitorowania kondycji](#health-monitoring-addresses)  | Podsieć ADX: 443  | TCP  |
| Wewnętrzna komunikacja ADX  | Podsieć ADX: wszystkie porty  | Podsieć ADX: wszystkie porty  | Wszyscy  |
| Zezwalaj na ruch przychodzący modułu równoważenia obciążenia platformy Azure (sonda kondycji)  | AzureLoadBalancer  | Podsieć ADX: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Konfiguracja wychodzącej sieciowej grupy zabezpieczeń

| **Korzystanie**   | **Wniosek**   | **Do**   | **Protokół**   |
| --- | --- | --- | --- |
| Zależność od usługi Azure Storage  | Podsieć ADX  | Magazyn: 443  | TCP  |
| Zależność od Azure Data Lake  | Podsieć ADX  | AzureDataLake: 443  | TCP  |
| Pozyskiwanie i monitorowanie usług EventHub  | Podsieć ADX  | EventHub: 443, 5671  | TCP  |
| Publikowanie metryk  | Podsieć ADX  | AzureMonitor: 443 | TCP  |
| Pobieranie konfiguracji Azure Monitor  | Podsieć ADX  | [Adresy punktów końcowych konfiguracji Azure monitor](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (jeśli dotyczy) | Podsieć ADX | Usługi azureactivedirectory: 443 | TCP |
| Urząd certyfikacji | Podsieć ADX | Internet: 80 | TCP |
| Komunikacja wewnętrzna  | Podsieć ADX  | Podsieć ADX: wszystkie porty  | Wszyscy  |
| Porty używane na potrzeby wtyczek `sql\_request` i `http\_request`  | Podsieć ADX  | Internet: niestandardowe  | TCP  |

### <a name="relevant-ip-addresses"></a>Odpowiednie adresy IP

#### <a name="azure-data-explorer-management-ip-addresses"></a>Adresy IP zarządzania usługą Azure Eksplorator danych

| Region | Adresy |
| --- | --- |
| Australia Środkowa | 20.37.26.134 |
| Australia Central2 | 20.39.99.177 |
| Australia Wschodnia | 40.82.217.84 |
| Australia Południowo-Wschodnia | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Kanada Środkowa | 40.82.188.208 |
| Kanada Wschodnia | 40.80.255.12 |
| Indie Środkowe | 40.81.249.251 |
| Środkowe stany USA | 40.67.188.68 |
| Środkowe stany USA — EUAP | 40.89.56.69 |
| Azja Wschodnia | 20.189.74.103 |
| Wschodnie stany USA | 52.224.146.56 |
| Wschodnie stany USA 2 | 52.232.230.201 |
| Wschód stany USA 2 — EUAP | 52.253.226.110 |
| Francja Środkowa | 40.66.57.91 |
| Francja Południowa | 40.82.236.24 |
| Japonia Wschodnia | 20.43.89.90 |
| Japonia Zachodnia | 40.81.184.86 |
| Korea Środkowa | 40.82.156.149 |
| Korea Południowa | 40.80.234.9 |
| Środkowo-północne stany USA | 40.81.45.254 |
| Europa Północna | 52.142.91.221 |
| Północna Republika Południowej Afryki | 102.133.129.138 |
| Północna Republika Południowej Afryki | 102.133.0.97 |
| Środkowo-południowe stany USA | 20.45.3.60 |
| Azja Południowo-Wschodnia | 40.119.203.252 |
| Indie Południowe | 40.81.72.110 |
| Południowe Zjednoczone Królestwo | 40.81.154.254 |
| Zachodnie Zjednoczone Królestwo | 40.81.122.39 |
| Środkowo-zachodnie stany USA | 52.159.55.120 |
| Europa Zachodnia | 51.145.176.215 |
| Indie Zachodnie | 40.81.88.112 |
| Zachodnie stany USA | 13.64.38.225 |
| Zachodnie stany USA 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Adresy monitorowania kondycji

| Region | Adresy |
| --- | --- |
| Australia Środkowa | 191.239.64.128 |
| Australia Środkowa 2 | 191.239.64.128 |
| Australia Wschodnia | 191.239.64.128 |
| Australia Południowo-Wschodnia | 191.239.160.47 |
| Brazylia Południowa | 23.98.145.105 |
| Kanada Środkowa | 168.61.212.201 |
| Kanada Wschodnia | 168.61.212.201 |
| Indie Środkowe | 23.99.5.162 |
| Środkowe stany USA | 168.61.212.201 |
| Środkowe stany USA — EUAP | 168.61.212.201 |
| Azja Wschodnia | 168.63.212.33 |
| Wschodnie stany USA | 137.116.81.189 |
| Wschodnie stany USA 2 | 137.116.81.189 |
| Wschodnie stany USA 2 — EUAP | 137.116.81.189 |
| Francja Środkowa | 23.97.212.5 |
| Francja Południowa | 23.97.212.5 |
| Japonia Wschodnia | 138.91.19.129 |
| Japonia Zachodnia | 138.91.19.129 |
| Korea Środkowa | 138.91.19.129 |
| Korea Południowa | 138.91.19.129 |
| Środkowo-północne stany USA | 23.96.212.108 |
| Europa Północna | 191.235.212.69 
| Północna Republika Południowej Afryki | 104.211.224.189 |
| Zachodnia Republika Południowej Afryki | 104.211.224.189 |
| Środkowo-południowe stany USA | 23.98.145.105 |
| Indie Południowe | 23.99.5.162 |
| Azja Południowo-Wschodnia | 168.63.173.234 |
| Południowe Zjednoczone Królestwo | 23.97.212.5 |
| Zachodnie Zjednoczone Królestwo | 23.97.212.5 |
| Środkowo-zachodnie stany USA | 168.61.212.201 |
| Europa Zachodnia | 23.97.212.5 |
| Indie Zachodnie | 23.99.5.162 |
| Zachodnie stany USA | 23.99.5.162 |
| Zachodnie stany USA 2 | 23.99.5.162 | 

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Adresy punktów końcowych konfiguracji Azure Monitor

| Region | Adresy |
| --- | --- |
| Australia Środkowa | 52.148.86.165 |
| Australia Środkowa 2 | 52.148.86.165 |
| Australia Wschodnia | 52.148.86.165 |
| Australia Południowo-Wschodnia | 52.148.86.165 |
| Brazylia Południowa | 13.68.89.19 |
| Kanada środkowa | 13.90.43.231 |
| Kanada Wschodnia | 13.90.43.231 |
| Indie Środkowe | 13.71.25.187 |
| Środkowe stany USA | 52.173.95.68 |
| Środkowe stany USA — EUAP | 13.90.43.231 |
| Azja Wschodnia | 13.75.117.221 |
| Wschodnie stany USA | 13.90.43.231 |
| Wschodnie stany USA 2 | 13.68.89.19 | 
| Wschodnie stany USA 2 — EUAP | 13.68.89.19 |
| Francja środkowa | 52.174.4.112 |
| Francja Południowa | 52.174.4.112 |
| Japonia Wschodnia | 13.75.117.221 |
| Japonia Zachodnia | 13.75.117.221 |
| Korea środkowa | 13.75.117.221 |
| Korea Południowa | 13.75.117.221 |
| Północno-środkowe stany USA | 52.162.240.236 |
| Europa Północna | 52.169.237.246 |
| Północna Republika Południowej Afryki | 13.71.25.187 |
| Zachodnia Republika Południowej Afryki | 13.71.25.187 |
| Południowo-środkowe stany USA | 13.84.173.99 |
| Indie Południowe | 13.71.25.187 |
| Azja Południowo-Wschodnia | 52.148.86.165 |
| Południowe Zjednoczone Królestwo | 52.174.4.112 |
| Zachodnie Zjednoczone Królestwo | 52.169.237.246 |
| Środkowo-zachodnie stany USA | 52.161.31.69 |
| Europa Zachodnia | 52.174.4.112 |
| Indie Zachodnie | 13.71.25.187 |
| Zachodnie stany USA | 40.78.70.148 |
| Zachodnie stany USA 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Konfiguracja ExpressRoute

Użyj ExpressRoute, aby nawiązać połączenie z siecią lokalną do Virtual Network platformy Azure. Typową konfiguracją jest anonsowanie trasy domyślnej (0.0.0.0/0) za pomocą sesji Border Gateway Protocol (BGP). Wymusza to ruch wychodzący z Virtual Network, który zostanie przekazany do sieci lokalnej klienta, co może spowodować przerwanie przepływów wychodzących. Aby wyeliminować to ustawienie domyślne, można skonfigurować [trasę zdefiniowaną przez użytkownika (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0), a następnym przeskokiem będzie *Internet*. Ponieważ UDR ma pierwszeństwo przed protokołem BGP, ruch będzie kierowany do Internetu.

## <a name="securing-outbound-traffic-with-firewall"></a>Zabezpieczanie ruchu wychodzącego za pomocą zapory

Jeśli chcesz zabezpieczyć ruch wychodzący przy użyciu [zapory systemu Azure](/azure/firewall/overview) lub dowolnego urządzenia wirtualnego, aby ograniczyć nazwy domen, w zaporze musi być dozwolona następująca w pełni kwalifikowana nazwa domeny (FQDN).

* prod.warmpath.msftcloudes.com:443
* production.diagnostics.monitoring.core.windows.net:443
* graph.windows.net:443
* *. update.microsoft.com:443
* shavamanifestcdnprod1.azureedge.net:443
* login.live.com:443
* wdcp.microsoft.com:443
* login.microsoftonline.com:443
* azureprofilerfrontdoor.cloudapp.net:443
* *. core.windows.net:443
* *. servicebus.windows.net:443
* shoebox2.metrics.nsatc.net:443
* production.diagnostics.monitoring.core.windows.net:443
* prod-dsts.dsts.core.windows.net:443
* ocsp.msocsp.com:80
* *. windowsupdate.com:80
* ocsp.digicert.com:80
* go.microsoft.com:80
* dmd.metaservices.microsoft.com:80
* www.msftconnecttest.com:80
* crl.microsoft.com:80
* www.microsoft.com:80
* adl.windows.com:80
* crl3.digicert.com:80

Należy również zdefiniować [tabelę tras](/azure/virtual-network/virtual-networks-udr-overview) w podsieci z [adresami zarządzania](#azure-data-explorer-management-ip-addresses) i [adresami monitorowania kondycji](#health-monitoring-addresses) z *Internetem* w następnym przeskoku, aby zapobiec problemom z trasami asymetryczny.

Na przykład dla regionu **zachodnie stany USA** należy zdefiniować następujące UDR:

| Nazwa | Przedrostek adresu | Następny przeskok |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Wdrażanie klastra usługi Azure Eksplorator danych w sieci wirtualnej przy użyciu szablonu Azure Resource Manager

Aby wdrożyć klaster Eksplorator danych platformy Azure w sieci wirtualnej, użyj szablonu [Wdróż klaster Eksplorator danych platformy Azure w swoim szablonie Azure Resource Manager sieci wirtualnej](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) .

Ten szablon służy do tworzenia klastra, sieci wirtualnej, podsieci, sieciowej grupy zabezpieczeń i publicznych adresów IP.
