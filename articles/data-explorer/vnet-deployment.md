---
title: Wdrażanie Eksploratora danych platformy Azure w sieci wirtualnej
description: Dowiedz się, jak wdrożyć Eksploratora danych platformy Azure w sieci wirtualnej
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c1ad8390bc5db72636c637c2ffb817e34674d0fa
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548847"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Wdrażanie klastra usługi Azure Data Explorer w sieci wirtualnej

W tym artykule opisano zasoby, które są obecne podczas wdrażania klastra usługi Azure Data Explorer w niestandardowej sieci wirtualnej platformy Azure. Te informacje ułatwią wdrożenie klastra w podsieci w sieci wirtualnej.This information will help you deploy a cluster into a subss w your Virtual Network (VNet). Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Co to jest usługa Azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![Diagram sieci vnet](media/vnet-deployment/vnet-diagram.png)

Usługa Azure Data Explorer obsługuje wdrażanie klastra w podsieci w sieci wirtualnej(VNet). Ta funkcja umożliwia:

* Wymuszanie reguł [sieciowej grupy zabezpieczeń](/azure/virtual-network/security-overview) (NSG) w ruchu klastra usługi Azure Data Explorer.
* Połącz sieć lokalną z podsiecią klastra usługi Azure Data Explorer.
* Zabezpiecz źródła połączeń danych[(Centrum zdarzeń](/azure/event-hubs/event-hubs-about) i [siatka zdarzeń)](/azure/event-grid/overview)za pomocą [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Uzyskiwanie dostępu do klastra usługi Azure Data Explorer w sieci wirtualnej

Dostęp do klastra usługi Azure Data Explorer można uzyskać przy użyciu następujących adresów IP dla każdej usługi (usługi zarządzania aparatem i danymi):

* **Prywatny adres IP**: Służy do uzyskiwania dostępu do klastra wewnątrz sieci wirtualnej.
* **Publiczny adres IP:** Służy do uzyskiwania dostępu do klastra spoza sieci wirtualnej do zarządzania i monitorowania oraz jako adres źródłowy dla połączeń wychodzących uruchomionych z klastra.

Aby uzyskać dostęp do usługi, tworzone są następujące rekordy DNS: 

* `[clustername].[geo-region].kusto.windows.net`(silnik) `ingest-[clustername].[geo-region].kusto.windows.net` (zarządzanie danymi) są mapowane do publicznego adresu IP dla każdej usługi. 

* `private-[clustername].[geo-region].kusto.windows.net`(silnik) `private-ingest-[clustername].[geo-region].kusto.windows.net` (zarządzanie danymi) są mapowane na prywatny adres IP dla każdej usługi.

## <a name="plan-subnet-size-in-your-vnet"></a>Planowanie rozmiaru podsieci w sieci wirtualnej

Nie można zmienić rozmiaru podsieci używanej do hostowania klastra usługi Azure Data Explorer po wdrożeniu podsieci. W sieci wirtualnej usługa Azure Data Explorer używa jednego prywatnego adresu IP dla każdej maszyny Wirtualnej i dwóch prywatnych adresów IP dla wewnętrznych modułów równoważenia obciążenia (zarządzanie aparatem i danymi). Sieć platformy Azure używa również pięciu adresów IP dla każdej podsieci. Usługa Azure Data Explorer udostępnia dwie maszyny wirtualne dla usługi zarządzania danymi. Maszyny wirtualne usługi aparatu są aprowizowana na pojemność skali konfiguracji użytkownika.

Całkowita liczba adresów IP:

| Użycie | Liczba adresów |
| --- | --- |
| Serwis silnika | 1 na wystąpienie |
| Usługa zarządzania danymi | 2 |
| Wewnętrzne moduły równoważenia obciążenia | 2 |
| Adresy zarezerwowane platformy Azure | 5 |
| **Łącznie** | **#engine_instances + 9** |

> [!IMPORTANT]
> Rozmiar podsieci należy zaplanować z wyprzedzeniem, ponieważ nie można go zmienić po wdrożeniu Usługi Azure Data Explorer. W związku z tym rezerwa potrzebny rozmiar podsieci odpowiednio.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Punkty końcowe usługi do łączenia się z Eksploratorem danych platformy Azure

[Punkty końcowe usługi Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) umożliwia zabezpieczenie zasobów wielu dzierżaw platformy Azure do sieci wirtualnej.
Wdrażanie klastra Usługi Azure Data Explorer w podsieci umożliwia konfigurowanie połączeń danych z [Centrum zdarzeń](/azure/event-hubs/event-hubs-about) lub [siatką zdarzeń](/azure/event-grid/overview) przy jednoczesnym ograniczeniu podstawowych zasobów dla podsieci Usługi Azure Data Explorer.

> [!NOTE]
> Podczas korzystania z konfiguracji EventGrid z [magazynem](/azure/storage/common/storage-introduction) i [Centrum zdarzeń] konto magazynu używane w ramach subskrypcji może być zablokowane z punktami końcowymi usługi w podsieci Usługi Azure Data Explorer, umożliwiając jednocześnie zaufane usługi platformy Azure w [konfiguracji zapory,](/azure/storage/common/storage-network-security)ale Centrum zdarzeń nie może włączyć punktu końcowego usługi, ponieważ nie obsługuje zaufanych [usług platformy Azure.](/azure/event-hubs/event-hubs-service-endpoints)

## <a name="dependencies-for-vnet-deployment"></a>Zależności dla wdrażania sieci wirtualnej

### <a name="network-security-groups-configuration"></a>Konfiguracja sieciowych grup zabezpieczeń

[Sieciowe grupy zabezpieczeń umożliwiają](/azure/virtual-network/security-overview) kontrolowanie dostępu do sieci wirtualnej. Dostęp do usługi Azure Data Explorer można uzyskać przy użyciu dwóch punktów końcowych: HTTPs (443) i TDS (1433). Następujące reguły sieciowej grupy danych sieciowych muszą być skonfigurowane tak, aby umożliwić dostęp do tych punktów końcowych do zarządzania, monitorowania i prawidłowego działania klastra.

#### <a name="inbound-nsg-configuration"></a>Konfiguracja przychodzącej sieciowej grupy zabezpieczeń

| **Użycie**   | **Od**   | **Do**   | **Protokół**   |
| --- | --- | --- | --- |
| Zarządzanie  |[Adresy zarządzania ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | Podsieć ADX:443  | TCP  |
| Monitorowanie kondycji  | [Adresy monitorowania kondycji ADX](#health-monitoring-addresses)  | Podsieć ADX:443  | TCP  |
| Komunikacja wewnętrzna ADX  | Podsieć ADX: Wszystkie porty  | Podsieć ADX:Wszystkie porty  | Wszystkie  |
| Zezwalaj na przychodzące moduły równoważenia obciążenia platformy Azure (sonda kondycji)  | AzureLoadBalancer  | Podsieć ADX:80 443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Konfiguracja wychodzącej sieciowej grupy zabezpieczeń

| **Użycie**   | **Od**   | **Do**   | **Protokół**   |
| --- | --- | --- | --- |
| Zależność od usługi Azure Storage  | Podsieć ADX  | Przechowywanie:443  | TCP  |
| Zależność od usługi Azure Data Lake  | Podsieć ADX  | Usługa AzureDataLake:443  | TCP  |
| Monitorowanie pozyskiwania i usługi EventHub  | Podsieć ADX  | EventHub:443,5671  | TCP  |
| Publikowanie danych  | Podsieć ADX  | Usługa AzureMonitor:443 | TCP  |
| Pobieranie konfiguracji usługi Azure Monitor  | Podsieć ADX  | [Adresy końcowe konfiguracji usługi Azure Monitor](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Usługa Active Directory (jeśli dotyczy) | Podsieć ADX | Usługa AzureActiveDirectory:443 | TCP |
| Urząd certyfikacji | Podsieć ADX | Internet:80 | TCP |
| Komunikacja wewnętrzna  | Podsieć ADX  | Podsieć ADX:Wszystkie porty  | Wszystkie  |
| Porty używane `sql\_request` dla `http\_request` i wtyczki  | Podsieć ADX  | Internet:Niestandardowe  | TCP  |

### <a name="relevant-ip-addresses"></a>Odpowiednie adresy IP

#### <a name="azure-data-explorer-management-ip-addresses"></a>Adresy IP zarządzania usługą Azure Data Explorer

| Region | Adresy |
| --- | --- |
| Australia Środkowa | 20.37.26.134 |
| Australia Central2 | 20.39.99.177 |
| Australia Wschodnia | 40.82.217.84 |
| Australia Południowo-Wschodnia | 20.40.161.39 |
| BrazyliaSouth | 191.233.25.183 |
| Kanada Środkowa | 40.82.188.208 |
| Kanada Wschodnia | 40.80.255.12 |
| Indie Środkowe | 40.81.249.251 |
| Środkowe stany USA | 40.67.188.68 |
| Centralna amerykańska EUAP | 40.89.56.69 |
| Azja Wschodnia | 20.189.74.103 |
| Wschodnie stany USA | 52.224.146.56 |
| Wschodnie stany USA 2 | 52.232.230.201 |
| Wschodnie STANY USA2 EUAP | 52.253.226.110 |
| Francja Środkowa | 40.66.57.91 |
| Francja Południowa | 40.82.236.24 |
| Japonia Wschodnia | 20.43.89.90 |
| Japonia Zachodnia | 40.81.184.86 |
| Korea Środkowa | 40.82.156.149 |
| Korea Południowa | 40.80.234.9 |
| Północno-środkowe stany USA | 40.81.45.254 |
| Europa Północna | 52.142.91.221 |
| Republika Południowej Afryki Północ | 102.133.129.138 |
| Republika Południowej Afryki Zachód | 102.133.0.97 |
| Południowo-środkowe stany USA | 20.45.3.60 |
| Azja Południowo-Wschodnia | 40.119.203.252 |
| Indie Południowe | 40.81.72.110 |
| Południowe Zjednoczone Królestwo | 40.81.154.254 |
| Zachodnie Zjednoczone Królestwo | 40.81.122.39 |
| Zachodnio-środkowe stany USA | 52.159.55.120 |
| Europa Zachodnia | 51.145.176.215 |
| Indie Zachodnie | 40.81.88.112 |
| Zachodnie stany USA | 13.64.38.225 |
| Zachodnie stany USA 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Adresy monitorowania stanu zdrowia

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
| Centralna amerykańska EUAP | 168.61.212.201 |
| Azja Wschodnia | 168.63.212.33 |
| Wschodnie stany USA | 137.116.81.189 |
| Wschodnie stany USA 2 | 137.116.81.189 |
| Wschodnie Stany Zjednoczone 2 EUAP | 137.116.81.189 |
| Francja Środkowa | 23.97.212.5 |
| Francja Południowa | 23.97.212.5 |
| Japonia Wschodnia | 138.91.19.129 |
| Japonia Zachodnia | 138.91.19.129 |
| Korea Środkowa | 138.91.19.129 |
| Korea Południowa | 138.91.19.129 |
| Północno-środkowe stany USA | 23.96.212.108 |
| Europa Północna | 191.235.212.69 
| Republika Południowej Afryki Północ | 104.211.224.189 |
| Republika Południowej Afryki Zachód | 104.211.224.189 |
| Południowo-środkowe stany USA | 23.98.145.105 |
| Indie Południowe | 23.99.5.162 |
| Azja Południowo-Wschodnia | 168.63.173.234 |
| Południowe Zjednoczone Królestwo | 23.97.212.5 |
| Zachodnie Zjednoczone Królestwo | 23.97.212.5 |
| Zachodnio-środkowe stany USA | 168.61.212.201 |
| Europa Zachodnia | 23.97.212.5 |
| Indie Zachodnie | 23.99.5.162 |
| Zachodnie stany USA | 23.99.5.162 |
| Zachodnie stany USA 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Adresy punktów końcowych konfiguracji usługi Azure Monitor

| Region | Adresy |
| --- | --- |
| Australia Środkowa | 52.148.86.165 |
| Australia Środkowa 2 | 52.148.86.165 |
| Australia Wschodnia | 52.148.86.165 |
| Australia Południowo-Wschodnia | 52.148.86.165 |
| Brazylia Południowa | 13.68.89.19 |
| Kanada Środkowa | 13.90.43.231 |
| Kanada Wschodnia | 13.90.43.231 |
| Indie Środkowe | 13.71.25.187 |
| Środkowe stany USA | 52.173.95.68 |
| Centralna amerykańska EUAP | 13.90.43.231 |
| Azja Wschodnia | 13.75.117.221 |
| Wschodnie stany USA | 13.90.43.231 |
| Wschodnie stany USA 2 | 13.68.89.19 |    
| Wschodnie Stany Zjednoczone 2 EUAP | 13.68.89.19 |
| Francja Środkowa | 52.174.4.112 |
| Francja Południowa | 52.174.4.112 |
| Japonia Wschodnia | 13.75.117.221 |
| Japonia Zachodnia | 13.75.117.221 |
| Korea Środkowa | 13.75.117.221 |
| Korea Południowa | 13.75.117.221 |
| Północno-środkowe stany USA | 52.162.240.236 |
| Europa Północna | 52.169.237.246 |
| Republika Południowej Afryki Północ | 13.71.25.187 |
| Republika Południowej Afryki Zachód | 13.71.25.187 |
| Południowo-środkowe stany USA | 13.84.173.99 |
| Indie Południowe | 13.71.25.187 |
| Azja Południowo-Wschodnia | 52.148.86.165 |
| Południowe Zjednoczone Królestwo | 52.174.4.112 |
| Zachodnie Zjednoczone Królestwo | 52.169.237.246 |
| Zachodnio-środkowe stany USA | 52.161.31.69 |
| Europa Zachodnia | 52.174.4.112 |
| Indie Zachodnie | 13.71.25.187 |
| Zachodnie stany USA | 40.78.70.148 |
| Zachodnie stany USA 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Konfiguracja usługi ExpressRoute

Użyj usługi ExpressRoute do łączenia się w sieci lokalnej z siecią wirtualną platformy Azure. Powszechną konfiguracją jest anonsowanie trasy domyślnej (0.0.0.0/0) za pośrednictwem sesji protokołu BGP (Border Gateway Protocol). Wymusza to ruch wychodzący z sieci wirtualnej do przekazania do sieci lokalnej klienta, która może spowodować spadek ruchu, powodując przerwanie przepływów wychodzących. Aby przezwyciężyć ten domyślny, można skonfigurować [trasę zdefiniowaną przez użytkownika (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0), a następnym przeskokem będzie *Internet*. Ponieważ UDR ma pierwszeństwo przed BGP, ruch będzie przeznaczony do Internetu.

## <a name="securing-outbound-traffic-with-firewall"></a>Zabezpieczanie ruchu wychodzącego za pomocą zapory

Jeśli chcesz zabezpieczyć ruch wychodzący przy użyciu [Zapory azure](/azure/firewall/overview) lub dowolnego urządzenia wirtualnego w celu ograniczenia nazw domen, w zaporze muszą być dozwolone następujące w pełni kwalifikowane nazwy domen (FQDN).

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Należy również zdefiniować [tabelę tras](/azure/virtual-network/virtual-networks-udr-overview) w podsieci z [adresami zarządzania](#azure-data-explorer-management-ip-addresses) i [adresami monitorowania kondycji](#health-monitoring-addresses) za pomocą następnego przeskoku *Internet,* aby zapobiec problemom z trasami asymetrycznymi.

Na przykład dla regionu **Zachodnie stany USA** należy zdefiniować następujące UDR:

| Nazwa | Przedrostek adresu | Następny przeskok |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Wdrażanie klastra usługi Azure Data Explorer w sieci wirtualnej przy użyciu szablonu usługi Azure Resource Manager

Aby wdrożyć klaster Usługi Azure Data Explorer w sieci wirtualnej, użyj [klastra Wdrażanie usługi Azure Data Explorer w szablonie](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) usługi Azure Resource Manager sieci wirtualnej.

Ten szablon tworzy klaster, sieć wirtualną, podsieć, sieciową grupę zabezpieczeń i publiczne adresy IP.
