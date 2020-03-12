---
title: Wdróż Eksplorator danych platformy Azure w Virtual Network
description: Dowiedz się, jak wdrożyć usługę Azure Eksplorator danych w Virtual Network
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 5a2731e26ba4f371177cf2ae649f0695f27e6304
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096765"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network"></a>Wdróż Eksplorator danych platformy Azure w Virtual Network

W tym artykule opisano zasoby, które są obecne podczas wdrażania klastra usługi Azure Eksplorator danych w niestandardowym Virtual Network platformy Azure. Te informacje ułatwią Wdrożenie klastra w podsieci w Virtual Network (VNet). Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [co to jest usługa azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![diagram sieci wirtualnej](media/vnet-deployment/vnet-diagram.png)

Usługa Azure Eksplorator danych obsługuje wdrażanie klastra w podsieci Virtual Network (VNet). Ta funkcja umożliwia:

* Wymuś reguły [sieciowej grupy zabezpieczeń](/azure/virtual-network/security-overview) (sieciowej grupy zabezpieczeń) w ruchu klastra Eksplorator danych platformy Azure.
* Połącz sieć lokalną z podsiecią klastra Eksplorator danych platformy Azure.
* Zabezpiecz źródła połączenia danych ([centrum zdarzeń](/azure/event-hubs/event-hubs-about) i [Event Grid](/azure/event-grid/overview)) za pomocą [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview).

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

> [!NOTE]
> W przypadku korzystania z Instalatora usługi EventGrid z [magazynem](/azure/storage/common/storage-introduction) i usługą [Event Hub] konto magazynu używane w ramach subskrypcji może być blokowane z punktami końcowymi usługi do podsieci platformy Azure Eksplorator danych podczas zezwalania na zaufane usługi platformy Azure w [konfiguracji zapory](/azure/storage/common/storage-network-security), ale centrum zdarzeń nie może włączyć punktu końcowego usługi, ponieważ nie obsługuje on zaufanych [usług platformy Azure](/azure/event-hubs/event-hubs-service-endpoints).

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
| Północno-środkowe stany USA | 40.81.45.254 |
| Europa Północna | 52.142.91.221 |
| Północna Republika Południowej Afryki | 102.133.129.138 |
| Zachodnia Republika Południowej Afryki | 102.133.0.97 |
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
| Północno-środkowe stany USA | 23.96.212.108 |
| Europa Północna | 191.235.212.69 
| Północna Republika Południowej Afryki | 104.211.224.189 |
| Zachodnia Republika Południowej Afryki | 104.211.224.189 |
| Południowo-środkowe stany USA | 23.98.145.105 |
| Indie Południowe | 23.99.5.162 |
| Azja Południowo-Wschodnia | 168.63.173.234 |
| Południowe Zjednoczone Królestwo | 23.97.212.5 |
| Zachodnie Zjednoczone Królestwo | 23.97.212.5 |
| Zachodnio-środkowe stany USA | 168.61.212.201 |
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
| Zachodnio-środkowe stany USA | 52.161.31.69 |
| Europa Zachodnia | 52.174.4.112 |
| Indie Zachodnie | 13.71.25.187 |
| Zachodnie stany USA | 40.78.70.148 |
| Zachodnie stany USA 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Konfiguracja ExpressRoute

Użyj ExpressRoute, aby nawiązać połączenie z siecią lokalną do Virtual Network platformy Azure. Typową konfiguracją jest anonsowanie trasy domyślnej (0.0.0.0/0) za pomocą sesji Border Gateway Protocol (BGP). Wymusza to ruch wychodzący z Virtual Network, który zostanie przekazany do sieci lokalnej klienta, co może spowodować przerwanie przepływów wychodzących. Aby wyeliminować to ustawienie domyślne, można skonfigurować [trasę zdefiniowaną przez użytkownika (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0), a następnym przeskokiem będzie *Internet*. Ponieważ UDR ma pierwszeństwo przed protokołem BGP, ruch będzie kierowany do Internetu.

## <a name="securing-outbound-traffic-with-firewall"></a>Zabezpieczanie ruchu wychodzącego za pomocą zapory

Jeśli chcesz zabezpieczyć ruch wychodzący przy użyciu [zapory systemu Azure](/azure/firewall/overview) lub dowolnego urządzenia wirtualnego, aby ograniczyć nazwy domen, w zaporze musi być dozwolona następująca w pełni kwalifikowana nazwa domeny (FQDN).

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

Należy również zdefiniować [tabelę tras](/azure/virtual-network/virtual-networks-udr-overview) w podsieci z [adresami zarządzania](#azure-data-explorer-management-ip-addresses) i [adresami monitorowania kondycji](#health-monitoring-addresses) z *Internetem* w następnym przeskoku, aby zapobiec problemom z trasami asymetryczny.

Na przykład dla regionu **zachodnie stany USA** należy zdefiniować następujące UDR:

| Name (Nazwa) | Przedrostek adresu | Następny przeskok |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Wdrażanie klastra usługi Azure Eksplorator danych w sieci wirtualnej przy użyciu szablonu Azure Resource Manager

Aby wdrożyć klaster Eksplorator danych platformy Azure w sieci wirtualnej, użyj szablonu [Wdróż klaster Eksplorator danych platformy Azure w swoim szablonie Azure Resource Manager sieci wirtualnej](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) .

Ten szablon służy do tworzenia klastra, sieci wirtualnej, podsieci, sieciowej grupy zabezpieczeń i publicznych adresów IP.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W tej sekcji dowiesz się, jak rozwiązywać problemy z łącznością, działaniem i tworzeniem klastra dla klastra wdrożonego w [Virtual Network](/azure/virtual-network/virtual-networks-overview).

### <a name="access-issues"></a>Problemy z dostępem

Jeśli wystąpi problem podczas uzyskiwania dostępu do klastra za pomocą publicznego (cluster.region.kusto.windows.net) lub prywatnego (private-cluster.region.kusto.windows.net) punktu końcowego i podejrzewasz, że jest on powiązany z konfiguracją sieci wirtualnej, wykonaj następujące kroki, aby Rozwiąż problem.

#### <a name="check-tcp-connectivity"></a>Sprawdź łączność TCP

Pierwszy krok obejmuje Sprawdzanie łączności TCP przy użyciu systemu operacyjnego Windows lub Linux.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Pobierz [TCping](https://www.elifulkerson.com/projects/tcping.php) na komputer, który nawiązuje połączenie z klastrem.
   2. Wyślij polecenie ping do miejsca docelowego z maszyny źródłowej za pomocą następującego polecenia:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Zainstaluj *netcat* na komputerze łączącym się z klastrem

    ```bash
    $ apt-get install netcat
     ```

   2. Wyślij polecenie ping do miejsca docelowego z maszyny źródłowej za pomocą następującego polecenia:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Jeśli test nie powiedzie się, wykonaj następujące czynności. Jeśli test zakończy się pomyślnie, problem nie jest spowodowany problemem z łącznością TCP. Przejdź do [zagadnień operacyjnych](#cluster-creation-and-operations-issues) , aby rozwiązać problem.

#### <a name="check-the-network-security-group-nsg"></a>Sprawdź sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń)

   Sprawdź, czy [sieciowa Grupa zabezpieczeń](/azure/virtual-network/security-overview) (sieciowej grupy zabezpieczeń) dołączona do podsieci klastra ma regułę ruchu przychodzącego zezwalającą na dostęp z adresu IP komputera klienckiego dla portu 443.

#### <a name="check-route-table"></a>Sprawdź tabelę tras

   Jeśli podsieć klastra zawiera konfigurację tunelowania wymuszonego na zaporze (podsieci z [tabelą tras](/azure/virtual-network/virtual-networks-udr-overview) , która zawiera domyślną trasę "0.0.0.0/0"), upewnij się, że adres IP komputera ma trasę z [typem następnego przeskoku](/azure/virtual-network/virtual-networks-udr-overview) do VirtualNetwork/Internet. Jest to wymagane, aby zapobiec problemom z trasą asymetryczną.

### <a name="ingestion-issues"></a>Problemy dotyczące pozyskiwania

Jeśli występują problemy z pozyskiwaniem i podejrzewasz, że jest ona powiązana z konfiguracją sieci wirtualnej, wykonaj następujące czynności.

#### <a name="check-ingestion-health"></a>Sprawdź kondycję pozyskiwania

    Check that the [cluster ingestion metrics](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicate a healthy state.

#### <a name="check-security-rules-on-data-source-resources"></a>Sprawdzanie reguł zabezpieczeń dotyczących zasobów źródła danych

Jeśli metryki wskazują, że żadne zdarzenia nie zostały przetworzone ze źródła danych (*zdarzenia przetwarzane* przez Centra zdarzeń/IoT), upewnij się, że zasoby źródła danych (centrum zdarzeń lub magazyn) umożliwiają dostęp z podsieci klastra w regułach zapory lub punktach końcowych usługi.

#### <a name="check-security-rules-configured-on-clusters-subnet"></a>Sprawdź reguły zabezpieczeń skonfigurowane w podsieci klastra

Upewnij się, że podsieć klastra ma sieciowej grupy zabezpieczeń, UDR i reguły zapory są prawidłowo skonfigurowane. Dodatkowo przetestuj łączność sieciową dla wszystkich zależnych punktów końcowych. 

### <a name="cluster-creation-and-operations-issues"></a>Problemy z tworzeniem klastrów i operacjami

Jeśli występują problemy z tworzeniem lub działaniem klastra i podejrzewasz, że jest on związany z konfiguracją sieci wirtualnej, wykonaj następujące kroki, aby rozwiązać problem.

#### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnozuj sieć wirtualną za pomocą interfejsu API REST

[ARMClient](https://chocolatey.org/packages/ARMClient) jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. 

1. Logowanie za pomocą ARMClient

   ```powerShell
   armclient login
   ```

1. Wywołaj operację diagnostyki

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Sprawdź odpowiedź

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Poczekaj na zakończenie operacji

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Poczekaj, aż Właściwość *status* zostanie *zakończona*, a następnie pole *Właściwości* powinno zawierać:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Jeśli właściwość *spostrzeżenia* pokazuje pusty wynik, oznacza to, że wszystkie testy sieci zakończyły się niepowodzeniem, a połączenia nie są przerywane. Jeśli wystąpi błąd w następujący sposób: *zależność wychodząca "{dependencyname}: {Port}" może nie być spełniona (wychodzące)* , klaster nie może połączyć się z punktami końcowymi zależnych usług. Wykonaj następujące kroki, aby rozwiązać problem.

#### <a name="check-network-security-group-nsg"></a>Sprawdź grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń)

Upewnij się, że [sieciowa Grupa zabezpieczeń](/azure/virtual-network/security-overview) jest prawidłowo skonfigurowana zgodnie z instrukcjami w [zależności od wdrożenia sieci wirtualnej](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

#### <a name="check-route-table"></a>Sprawdź tabelę tras

Jeśli podsieć klastra ma ustawioną tunelowanie wymuszone na zaporę (podsieć z [tabelą tras](/azure/virtual-network/virtual-networks-udr-overview) , która zawiera domyślną trasę "0.0.0.0/0"), upewnij się, że adresy IP [zarządzania i administracyjne](#azure-data-explorer-management-ip-addresses) [monitorowanie kondycji](#health-monitoring-addresses) mają trasę z [typem następnego przeskoku](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*oraz [prefiksem adresu źródłowego](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) do *"Zarządzanie-IP/32"* i *"kondycja-monitorowanie-IP/32"* . Jest to wymagane, aby zapobiec problemom z trasą asymetryczną.

#### <a name="check-firewall-rules"></a>Sprawdź reguły zapory

Jeśli Wymuś ruch wychodzący z podsieci tunelu do zapory, upewnij się, że w konfiguracji zapory są dozwolone wszystkie nazwy FQDN zależności (na przykład *. blob.Core.Windows.NET*), zgodnie z opisem w temacie [Zabezpieczanie ruchu wychodzącego za pomocą zapory](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).
