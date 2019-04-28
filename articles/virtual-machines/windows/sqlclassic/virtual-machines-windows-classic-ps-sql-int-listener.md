---
title: Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure | Dokumentacja firmy Microsoft
description: Ten samouczek używa zasobów utworzonych za pomocą klasycznego modelu wdrażania, i tworzy Always On odbiornik grupy dostępności na platformie Azure, która używa wewnętrznego modułu równoważenia obciążenia.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0e6a52ea2fdd05546a4da9f8cd1165b41ed27944
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097740"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure
> [!div class="op_single_selector"]
> * [Wewnętrznego odbiornika](../classic/ps-sql-int-listener.md)
> * [Zewnętrznego odbiornika](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługa Azure Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Zaleca się, że większości nowych wdrożeń korzystać z modelu usługi Resource Manager.

Aby skonfigurować odbiornik grupy dostępności Always On w modelu usługi Resource Manager, zobacz [Konfigurowanie modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Grupy dostępności może zawierać replik, które są tylko lokalnie lub na platformie Azure tylko lub który obejmować zarówno lokalnych, jak i platformy Azure dla konfiguracji hybrydowych. Repliki systemu Azure mogą znajdować się w tym samym regionie lub w wielu regionach, które używają wielu sieci wirtualnych. Procedury przedstawione w tym artykule przyjęto założenie, że masz już [skonfigurowane grupy dostępności](../classic/portal-sql-alwayson-availability-groups.md) , ale nie skonfigurowano jeszcze odbiornik.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Wytyczne i ograniczenia dotyczące odbiorników wewnętrznych
Użycie wewnętrznego modułu równoważenia obciążenia (ILB) przy użyciu odbiornika grupy dostępności na platformie Azure podlega następujące wytyczne:

* Odbiornik grupy dostępności jest obsługiwana w systemie Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Tylko jeden odbiornik grupy dostępności wewnętrznego jest obsługiwana dla każdej usługi w chmurze, ponieważ odbiornika jest skonfigurowany do wewnętrznego modułu równoważenia obciążenia, a istnieje tylko jeden wewnętrznego modułu równoważenia obciążenia dla każdej usługi w chmurze. Jednak jest możliwość tworzenia wielu odbiorników zewnętrznych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odbiornika zewnętrznego dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Ustalić dostępność odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ten artykuł koncentruje się na tworzeniu odbiornik, który korzysta z wewnętrznym modułem równoważenia obciążenia. Odbiornik publicznego lub zewnętrzne, należy wyświetlić wersję tego artykułu, który w tym artykule omówiono ustawienia zapasowej [zewnętrznego odbiornika](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzenie ze zrównoważonym obciążeniem punktów końcowych maszyny Wirtualnej przy użyciu bezpośrednie serwera return
Najpierw utwórz wewnętrznym modułem równoważenia obciążenia, uruchamiając skrypt później w tej sekcji.

Utwórz punkt końcowy z równoważeniem obciążenia dla każdej maszyny Wirtualnej, który jest hostem repliki platformy Azure. Jeśli masz replik w wielu regionach w każdej repliki dla tego regionu musi być w tej samej usługi w chmurze w tej samej sieci wirtualnej platformy Azure. Tworzenie dostępności replik grupy, obejmujących wiele regionów platformy Azure wymagane jest skonfigurowanie wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania między sieciami wirtualnymi, zobacz [Konfigurowanie sieci wirtualnej do łączności z siecią wirtualną](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W witrynie Azure portal przejdź do każdej maszyny Wirtualnej, który obsługuje replikę, aby wyświetlić szczegóły.

2. Kliknij przycisk **punktów końcowych** kartę dla każdej maszyny Wirtualnej.

3. Upewnij się, że **nazwa** i **Port publiczny** odbiornika punktu końcowego, którego chcesz używać nie są już używane. W tym przykładzie w tej sekcji nazwa to *MyEndpoint*, oraz numer portu to *1433*.

4. Na lokalnym kliencie, Pobierz i zainstaluj najnowszą wersję [modułu programu PowerShell](https://azure.microsoft.com/downloads/).

5. Uruchom program Azure PowerShell.  
    Nowej sesji programu PowerShell zostanie otwarta z administracyjne moduły platformy Azure, załadowane.

6. Uruchom polecenie `Get-AzurePublishSettingsFile`. To polecenie cmdlet kieruje do przeglądarki, aby pobrać plik ustawień publikowania do katalogu lokalnego. Może być monit o poświadczenia logowania dla subskrypcji platformy Azure.

7. Uruchom następujące polecenie `Import-AzurePublishSettingsFile` polecenia przy użyciu ścieżki pliku ustawień publikowania, który został pobrany:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po zaimportowaniu ustawień publikowania można zarządzać subskrypcją platformy Azure, w sesji programu PowerShell.

8. Aby uzyskać *wewnętrznego modułu równoważenia obciążenia*, Przypisz statyczny adres IP. Sprawdź bieżącą konfigurację sieci wirtualnej, uruchamiając następujące polecenie:

        (Get-AzureVNetConfig).XMLConfiguration
9. Uwaga *podsieci* nazwy podsieci zawierającej maszyny wirtualne hostujące replik. Ta nazwa jest używana w parametrze $SubnetName w skrypcie.

10. Uwaga *VirtualNetworkSite* nazwy i początkowy *AddressPrefix* dla podsieci zawierającej maszyny wirtualne, które hostują replik. Wyszukaj dostępny adres IP, przekazując obie wartości do `Test-AzureStaticVNetIP` poleceń i przez badanie *AvailableAddresses*. Na przykład, jeśli sieć wirtualna ma nazwę *MyVNet* i ma zakres adresów podsieci, która rozpoczyna się od *172.16.0.128*, następującego polecenia pojawi się lista dostępnych adresów:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Wybierz jedną z dostępnych adresów i używać go w parametrze $ILBStaticIP skryptu w następnym kroku.

12. Skopiuj poniższy skrypt programu PowerShell do edytora tekstów i ustaw wartości zmiennych w zależności od środowiska. Wartości domyślne zostały przewidziane w niektórych parametrów.  

    Istniejące wdrożenia, które korzysta z grup koligacji nie można dodać wewnętrznym modułem równoważenia obciążenia. Aby uzyskać więcej informacji na temat wymagań dotyczących wewnętrznego modułu równoważenia obciążenia, zobacz [omówienie modułu równoważenia obciążenia wewnętrznego](../../../load-balancer/load-balancer-internal-overview.md).

    Ponadto jeśli grupy dostępności obejmuje regiony platformy Azure, skrypt należy uruchomić jeden raz w każdym centrum danych do usługi w chmurze i węzły, które znajdują się w tym centrum danych.

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

13. Po ustawieniu zmiennych, skopiuj skrypt w edytorze tekstowym do sesji środowiska PowerShell, aby go uruchomić. Jeśli nadal wyświetlany jest monit **>>**, naciśnij klawisz Enter, ponownie, aby upewnić się, że uruchomieniu skryptu.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sprawdź, czy KB2854082 jest zainstalowana w razie potrzeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otwórz porty zapory w węzły grupy dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Utwórz odbiornik grupy dostępności

Utwórz odbiornik grupy dostępności w dwóch krokach. Najpierw utwórz zasób klastra punktu dostępu klienta i skonfigurować zależności. Po drugie Skonfiguruj zasoby klastra w programie PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Utwórz punkt dostępu klienta i skonfiguruj zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. Dla wewnętrznego modułu równoważenia obciążenia należy użyć adres IP wewnętrznego modułu równoważenia obciążenia, który został utworzony wcześniej. Aby uzyskać ten adres IP w programie PowerShell, użyj następującego skryptu:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednym z maszyn wirtualnych Skopiuj skrypt programu PowerShell dla systemu operacyjnego do edytora tekstów, a następnie ustaw zmienne do wartości, które możesz zauważyć, wcześniej.

    Dla systemu Windows Server 2012 lub nowszym Użyj następującego skryptu:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Dla systemu Windows Server 2008 R2 Użyj następującego skryptu:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Po ustawieniu zmiennych, Otwórz okno programu Windows PowerShell z podwyższonym, wklej skrypt w edytorze tekstowym do sesji programu PowerShell, aby go uruchomić. Jeśli nadal wyświetlany jest monit **>>**, naciśnij klawisz Enter, ponownie, aby upewnić się, że skrypt uruchamiania.

4. Powtórz te czynności dla każdej maszyny Wirtualnej.  
    Ten skrypt konfiguruje się zasobu adresu IP przy użyciu adresu IP usługi w chmurze i ustawia inne parametry, takie jak port sondy. Jeśli zasób adresu IP w tryb online, mogą odpowiadać na sondowanie na porcie sondowania z równoważeniem obciążenia punktu końcowego utworzonego wcześniej.

## <a name="bring-the-listener-online"></a>Przenieś odbiornika w trybie online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy monitowania
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testowanie odbiornika grupy dostępności (w ramach tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
