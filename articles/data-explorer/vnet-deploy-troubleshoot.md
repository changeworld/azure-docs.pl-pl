---
title: Rozwiązywanie problemów z dostępem, pozyskiwaniam i działaniem klastra usługi Azure Data Explorer w sieci wirtualnej
description: Rozwiązywanie problemów z łącznością, pozyskiwania, tworzenia klastra i obsługi klastra usługi Azure Data Explorer w sieci wirtualnej
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241662"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Rozwiązywanie problemów z dostępem, pozyskiwaniam i działaniem klastra usługi Azure Data Explorer w sieci wirtualnej

W tej sekcji dowiesz się, jak rozwiązywać problemy z łącznością, funkcją operacyjną i tworzeniem klastra dla klastra wdrożonego w [sieci wirtualnej](/azure/virtual-network/virtual-networks-overview).

## <a name="access-issues"></a>Problemy z dostępem

Jeśli masz problem podczas uzyskiwania dostępu do klastra przy użyciu publicznego (cluster.region.kusto.windows.net) lub prywatnego (private-cluster.region.kusto.windows.net) punktu końcowego i podejrzewasz, że jest on związany z konfiguracją sieci wirtualnej, wykonaj następujące czynności, aby rozwiązać problem.

### <a name="check-tcp-connectivity"></a>Sprawdź łączność TCP

Pierwszy krok obejmuje sprawdzanie łączności TCP przy użyciu systemu operacyjnego Windows lub Linux.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Pobierz [TCping](https://www.elifulkerson.com/projects/tcping.php) do komputera łączącego się z klastrem.
   2. Polecenie pingowanie miejsca docelowego z komputera źródłowego za pomocą następującego polecenia:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Zainstaluj *netcat* w komputerze łączącym się z klastrem

    ```bash
    $ apt-get install netcat
     ```

   2. Polecenie pingowanie miejsca docelowego z komputera źródłowego za pomocą następującego polecenia:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Jeśli test nie powiedzie się, należy wykonać następujące kroki. Jeśli test zakończy się pomyślnie, problem nie jest spowodowany problemem z łącznością TCP. Przejdź do [problemów operacyjnych,](#cluster-creation-and-operations-issues) aby rozwiązać dalsze problemy.

### <a name="check-the-network-security-group-nsg"></a>Sprawdź grupę zabezpieczeń sieciowych (NSG)

   Sprawdź, czy [sieciowa grupa zabezpieczeń (NSG)](/azure/virtual-network/security-overview) dołączona do podsieci klastra ma regułę przychodzącą, która umożliwia dostęp z adresu IP komputera klienckiego dla portu 443.

### <a name="check-route-table"></a>Sprawdź tabelę tras

   Jeśli podsieć klastra ma ustawienia tunelowania siłowego do zapory (podsieć z [tabelą marszruty](/azure/virtual-network/virtual-networks-udr-overview) zawierającą domyślną trasę "0.0.0.0/0"), upewnij się, że adres IP komputera ma trasę z [następnym typem przeskoku](/azure/virtual-network/virtual-networks-udr-overview) do VirtualNetwork/Internet. Ta trasa jest wymagana, aby zapobiec problemom z trasą asymetryczną.

## <a name="ingestion-issues"></a>Problemy z połkniemy

Jeśli występują problemy z pojądaniem i podejrzewasz, że jest to związane z konfiguracją sieci wirtualnej, wykonaj następujące kroki.

### <a name="check-ingestion-health"></a>Sprawdź stan spożycia

Sprawdź, czy [metryki pozyskiwania klastra](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) wskazują na stan dobrej kondycji.

### <a name="check-security-rules-on-data-source-resources"></a>Sprawdzanie reguł zabezpieczeń zasobów źródła danych

Jeśli metryki wskazują, że żadne zdarzenia nie zostały przetworzone ze źródła danych *(Zdarzenia przetworzone* metryki dla usługi Event/IoT Hubs), upewnij się, że zasoby źródła danych (Centrum zdarzeń lub magazyn) umożliwiają dostęp z podsieci klastra w regułach zapory lub punktach końcowych usługi.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Sprawdzanie reguł zabezpieczeń skonfigurowanych w podsieci klastra

Upewnij się, że podsieć klastra ma nsg, UDR i reguły zapory są poprawnie skonfigurowane. Ponadto przetestuj łączność sieciową dla wszystkich zależnych punktów końcowych. 

## <a name="cluster-creation-and-operations-issues"></a>Problemy z tworzeniem i operacjami klastra

Jeśli występują problemy z tworzeniem lub działaniem klastra i podejrzewasz, że jest to związane z konfiguracją sieci wirtualnej, wykonaj następujące kroki, aby rozwiązać ten problem.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnozowanie sieci wirtualnej za pomocą interfejsu API REST

[ARMClient](https://chocolatey.org/packages/ARMClient) jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. 

1. Zaloguj się za pomocą ARMClient

   ```powerShell
   armclient login
   ```

1. Wywoływanie operacji diagnozowania

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
    
   Poczekaj, aż właściwość *status* pokazuje *Zakończone*, a następnie pole *właściwości* powinien pokazać:

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

Jeśli *findings* właściwość pokazuje pusty wynik, oznacza to, że wszystkie testy sieciowe przeszły i nie połączenia są przerywane. Jeśli zostanie wyświetlony następujący błąd, *zależność wychodząca "{dependencyName}:{port}" może nie być spełniona (Wychodzące),* klaster nie może dotrzeć do punktów końcowych usługi zależnej. Postępuj zgodnie z następującymi krokami.

### <a name="check-network-security-group-nsg"></a>Sprawdź grupę zabezpieczeń sieci (NSG)

Upewnij się, że [sieciowa grupa zabezpieczeń](/azure/virtual-network/security-overview) jest poprawnie skonfigurowana zgodnie z instrukcjami w [zależnościach dla wdrożenia sieci wirtualnej](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Sprawdź tabelę tras

Jeśli podsieć klastra ma ustawioną tunelowanie siłowe na zaporę (podsieć z [tabelą tras](/azure/virtual-network/virtual-networks-udr-overview) zawierającą domyślną trasę "0.0.0.0/0") upewnij się, że [adresy IP zarządzania)](vnet-deployment.md#azure-data-explorer-management-ip-addresses)i [adresy IP monitorowania kondycji](vnet-deployment.md#health-monitoring-addresses) mają trasę z następnym [typem przeskoku](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*i [prefiks adresu źródłowego](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) *do "management-ip/32"* i *"health-monitoring-ip/32".* Ta trasa jest wymagana, aby zapobiec problemom z trasą asymetryczną.

### <a name="check-firewall-rules"></a>Sprawdzanie reguł zapory

Jeśli wymusisz ruch wychodzący podsieci tunelowej do zapory, upewnij się, że wszystkie zależności FQDN (na przykład *.blob.core.windows.net*) są dozwolone w konfiguracji zapory, zgodnie z opisem [przy zabezpieczaniu ruchu wychodzącego za pomocą zapory](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).
