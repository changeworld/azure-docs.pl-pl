---
title: Konfigurowanie odbiornika równoważenia obciążenia sieciowego dla grup dostępności (classic)
description: W tym samouczku użyto zasobów utworzonych za pomocą klasycznego modelu wdrażania i tworzy odbiornik grupy zawsze włączone dla maszyny Wirtualnej programu SQL Server na platformie Azure, która używa wewnętrznego modułu równoważenia obciążenia.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f26c5a6c6fc2774d19beaa021015357a1991f0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978166"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurowanie odbiornika równoważenia obciążenia sieciowego dla grup dostępności na maszynach wirtualnych programu Sql Server platformy Azure
> [!div class="op_single_selector"]
> * [Odbiornik wewnętrzny](../classic/ps-sql-int-listener.md)
> * [Odbiornik zewnętrzny](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Usługi Azure Resource Manager i classic.](../../../azure-resource-manager/management/deployment-models.md) W tym artykule opisano użycie klasycznego modelu wdrażania. Zaleca się, aby większość nowych wdrożeń używała modelu Menedżera zasobów.

Aby skonfigurować odbiornik dla grupy dostępności zawsze włączone w modelu Menedżera zasobów, zobacz [Konfigurowanie modułu równoważenia obciążenia dla grupy dostępności zawsze włączone na platformie Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Grupa dostępności może zawierać repliki, które są tylko lokalne lub tylko platformy Azure lub które obejmują zarówno lokalnie i platformy Azure dla konfiguracji hybrydowych. Repliki platformy Azure mogą przebywać w tym samym regionie lub w wielu regionach, które używają wielu sieci wirtualnych. Procedury w tym artykule zakładają, że masz już [skonfigurowaną grupę dostępności,](../classic/portal-sql-alwayson-availability-groups.md) ale nie skonfigurowano jeszcze odbiornika.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Wytyczne i ograniczenia dla słuchaczy wewnętrznych
Użycie wewnętrznego modułu równoważenia obciążenia (ILB) z detektorem grupy dostępności na platformie Azure podlega następującym wskazówkom:

* Odbiornik grupy dostępności jest obsługiwany w systemach Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Tylko jeden odbiornik wewnętrznej grupy dostępności jest obsługiwany dla każdej usługi w chmurze, ponieważ odbiornik jest skonfigurowany do modułu równoważenia obciążenia sieciowego i istnieje tylko jeden moduł równoważenia obciążenia sieciowego dla każdej usługi w chmurze. Istnieje jednak możliwość utworzenia wielu zewnętrznych odbiorników. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zewnętrznego odbiornika dla grup dostępności Zawsze włączone na platformie Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Określanie dostępności odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

W tym artykule koncentruje się na tworzeniu odbiornika, który używa równoważenia obciążenia. Jeśli potrzebujesz odbiornika publicznego lub zewnętrznego, zobacz wersję tego artykułu, w której omówiono konfigurowanie [odbiornika zewnętrznego](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzenie punktów końcowych maszyny wirtualnej z równoważenia obciążenia z bezpośrednim powrotem serwera
Najpierw należy utworzyć modułU równoważenia obciążenia sieciowego, uruchamiając skrypt w dalszej części tej sekcji.

Utwórz punkt końcowy z równoważenia obciążenia dla każdej maszyny Wirtualnej, która obsługuje replikę platformy Azure. Jeśli masz repliki w wielu regionach, każda replika dla tego regionu musi znajdować się w tej samej usłudze w chmurze w tej samej sieci wirtualnej platformy Azure. Tworzenie replik grup dostępności obejmujących wiele regionów platformy Azure wymaga skonfigurowania wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania łączności między sieciami wirtualnymi, zobacz [Konfigurowanie sieci wirtualnej do łączności z siecią wirtualną](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W witrynie Azure portal przejdź do każdej maszyny Wirtualnej, która obsługuje replikę, aby wyświetlić szczegóły.

2. Kliknij kartę **Punkty końcowe** dla każdej maszyny wirtualnej.

3. Sprawdź, czy **nazwa** i **port publiczny** punktu końcowego odbiornika, który chcesz użyć nie są już w użyciu. W przykładzie w tej sekcji nazwa to *MyEndpoint*, a port to *1433*.

4. Na kliencie lokalnym pobierz i zainstaluj najnowszy [moduł programu PowerShell](https://azure.microsoft.com/downloads/).

5. Uruchom program Azure PowerShell.  
    Zostanie otwarta nowa sesja programu PowerShell z załadowanymi modułami administracyjnymi platformy Azure.

6. Uruchom polecenie `Get-AzurePublishSettingsFile`. To polecenie cmdlet kieruje użytkownika do przeglądarki w celu pobrania pliku ustawień publikowania do katalogu lokalnego. Może zostać wyświetlony monit o poświadczenia logowania dla subskrypcji platformy Azure.

7. Uruchom następujące `Import-AzurePublishSettingsFile` polecenie ze ścieżką pobranego pliku ustawień publikowania:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po zaimportowaniu pliku ustawień publikowania można zarządzać subskrypcją platformy Azure w sesji programu PowerShell.

8. W przypadku *równoważenia obciążenia sieciowego*należy przypisać statyczny adres IP. Sprawdź bieżącą konfigurację sieci wirtualnej, uruchamiając następujące polecenie:

        (Get-AzureVNetConfig).XMLConfiguration
9. Zanotuj nazwę *podsieci* podsieci zawierającej maszyny wirtualne, które hostują repliki. Ta nazwa jest używana w parametrze $SubnetName w skrypcie.

10. Zanotuj nazwę *VirtualNetworkSite* i początkową *poprawkę AddressPrefix* dla podsieci zawierającej maszyny wirtualne hostowane repliki. Poszukaj dostępnego adresu IP, `Test-AzureStaticVNetIP` przekazując obie wartości do polecenia i sprawdzając *adresy availableaddresses*. Na przykład jeśli sieć wirtualna ma nazwę *MyVNet* i ma zakres adresów podsieci, który rozpoczyna się od *172.16.0.128,* następujące polecenie będzie lista dostępnych adresów:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Wybierz jeden z dostępnych adresów i użyj go w $ILBStaticIP parametru skryptu w następnym kroku.

12. Skopiuj następujący skrypt programu PowerShell do edytora tekstu i ustaw wartości zmiennych zgodnie z odpowiednim środowiskiem. Dla niektórych parametrów podano wartości domyślne.  

    Istniejące wdrożenia korzystające z grup koligacji nie mogą dodać równoważenia obciążenia sieciowego. Aby uzyskać więcej informacji na temat wymagań równoważenia obciążenia sieciowego, zobacz [Omówienie wewnętrznego modułu równoważenia obciążenia](../../../load-balancer/load-balancer-internal-overview.md).

    Ponadto jeśli grupa dostępności obejmuje regiony platformy Azure, należy uruchomić skrypt raz w każdym centrum danych dla usługi w chmurze i węzłów, które znajdują się w tym centrum danych.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Po ustawieniu zmiennych skopiuj skrypt z edytora tekstu do sesji programu PowerShell, aby go uruchomić. Jeśli monit nadal **>>** jest wyświetlany , naciśnij klawisz Enter ponownie, aby upewnić się, że skrypt zostanie uruchomiony.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sprawdź, czy w razie potrzeby jest zainstalowana aktualizacja KB2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otwieranie portów zapory w węzłach grupy dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

Utwórz odbiornik grupy dostępności w dwóch krokach. Najpierw utwórz zasób klastra punktów dostępu klienta i skonfiguruj zależności. Po drugie, skonfiguruj zasoby klastra w programie PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Tworzenie punktu dostępu klienta i konfigurowanie zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. W przypadku równoważenia obciążenia sieciowego należy użyć adresu IP równoważenia obciążenia sieciowego, który został utworzony wcześniej. Aby uzyskać ten adres IP w programie PowerShell, użyj następującego skryptu:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednej z maszyn wirtualnych skopiuj skrypt programu PowerShell dla systemu operacyjnego do edytora tekstu, a następnie ustaw zmienne na wartości, które zostały wcześniej odnotowane.

    W systemie Windows Server 2012 lub nowszym użyj następującego skryptu:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    W systemie Windows Server 2008 R2 użyj następującego skryptu:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Po ustawieniu zmiennych otwórz okno programu Windows PowerShell z podwyższonym poziomem uprawnień, wklej skrypt z edytora tekstu do sesji programu PowerShell, aby go uruchomić. Jeśli monit nadal **>>** jest wyświetlany , Naciśnij przycisk Wprowadź ponownie, aby upewnić się, że skrypt zostanie uruchomiony.

4. Powtórz poprzednie kroki dla każdej maszyny Wirtualnej.  
    Ten skrypt konfiguruje zasób adresu IP z adresem IP usługi w chmurze i ustawia inne parametry, takie jak port sondy. Gdy zasób adresu IP jest przenoszony do trybu online, może odpowiadać na sondowanie na porcie sondowania z punktu końcowego równoważenia obciążenia, który został utworzony wcześniej.

## <a name="bring-the-listener-online"></a>Przewiń słuchacza w tryb online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy uzupełniające
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testowanie odbiornika grupy dostępności (w tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
