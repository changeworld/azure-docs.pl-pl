---
title: Skonfiguruj odbiornik ILB dla zawsze włączonych grup dostępności na platformie Azure | Dokumentacja firmy Microsoft
description: Ten samouczek używa zasobów utworzone za pomocą klasycznym modelu wdrożenia, i tworzy zawsze na odbiornik grupy dostępności na platformie Azure, która używa wewnętrznego modułu równoważenia obciążenia.
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
ms.openlocfilehash: 0466265ad5a24e8ea6dc5079e2b4006d74e7dde0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240752"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Skonfiguruj odbiornik ILB dla zawsze włączonych grup dostępności w systemie Azure
> [!div class="op_single_selector"]
> * [Odbiornik wewnętrzny](../classic/ps-sql-int-listener.md)
> * [Odbiornik zewnętrzny](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Przegląd

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono korzystanie z klasycznym modelu wdrażania. Zaleca się, że większości nowych wdrożeń Użyj modelu Resource Manager.

Aby skonfigurować odbiornik grupy dostępności AlwaysOn w modelu usługi Resource Manager, zobacz [skonfigurowania funkcji równoważenia obciążenia dla grupy dostępności AlwaysOn w Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Grupy dostępności może zawierać replik z lokalnymi tylko lub tylko Azure lub który obejmować zarówno lokalnych, jak i Azure dla hybrydowych konfiguracje. Azure replik może się znajdować w tym samym regionie lub w wielu regionach, które używają wielu sieci wirtualnych. Procedury przedstawione w tym artykule założono, że istnieje już [skonfigurowane grupy dostępności](../classic/portal-sql-alwayson-availability-groups.md) , ale nie ma jeszcze skonfigurowany odbiornik.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Zasady i ograniczenia dotyczące odbiorników wewnętrzny
Użycie wewnętrznego modułu równoważenia obciążenia (ILB) z odbiornika grupy dostępności na platformie Azure podlega następujących wytycznych:

* Odbiornik grupy dostępności jest obsługiwana w systemie Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Tylko jeden odbiornik grupy dostępności wewnętrznego jest obsługiwana dla każdej usługi w chmurze, ponieważ odbiornika jest skonfigurowany do ILB, a istnieje tylko jeden ILB dla każdej usługi w chmurze. Istnieje możliwość utworzenia wielu odbiorników zewnętrznych. Aby uzyskać więcej informacji, zobacz [skonfigurować odbiornik zewnętrzny dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Ustalić dostępność odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ten artykuł skupia się na utworzenie odbiornika, który używa ILB. Odbiornik publiczny lub zewnętrzne, należy wyświetlić wersję tego artykułu, w którym omówiono ustawienia zapasowej [zewnętrznych odbiornika](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzyć równoważeniem obciążenia punkty końcowe maszyny Wirtualnej z serwerem bezpośredniego zwrotu
Należy najpierw utworzyć ILB, uruchamiając skrypt później w tej sekcji.

Tworzenie punktu końcowego z równoważeniem obciążenia dla każdej maszyny Wirtualnej, który obsługuje replikę Azure. Jeśli masz repliki w wielu regionach, każdej repliki dla tego regionu musi być w tej samej usługi w chmurze w tej samej sieci wirtualnej platformy Azure. Tworzenie dostępności replik grupy, obejmujące wiele regionów platformy Azure wymaga konfigurowania wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania granic łączność sieci wirtualnej, zobacz [Konfigurowanie sieci wirtualnej do sieci wirtualnej łączności](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W portalu Azure przejdź do każdej maszyny Wirtualnej, który obsługuje replikę, aby wyświetlić szczegóły.

2. Kliknij przycisk **punkty końcowe** kartę dla każdej maszyny Wirtualnej.

3. Sprawdź, czy **nazwa** i **Port publiczny** nasłuchującego punktu końcowego, który chcesz użyć nie są już używane. W tym przykładzie w tej sekcji jest nazwa *MyEndpoint*, a numer portu to *1433*.

4. Na kliencie lokalnym, Pobierz i zainstaluj najnowszą [modułu PowerShell](https://azure.microsoft.com/downloads/).

5. Uruchom program Azure PowerShell.  
    Umożliwia otwarcie nowej sesji programu PowerShell z Azure załadowanych modułów administracyjnych.

6. Uruchom polecenie `Get-AzurePublishSettingsFile`. To polecenie cmdlet kieruje do przeglądarki, aby pobrać plik ustawień publikowania do katalogu lokalnego. Użytkownik może zostać poproszony o podanie poświadczeń logowania dla subskrypcji platformy Azure.

7. Uruchom następujące polecenie `Import-AzurePublishSettingsFile` polecenia ze ścieżką pobrany plik ustawień publikowania:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po zaimportowaniu ustawień publikowania, można zarządzać subskrypcją platformy Azure w sesji programu PowerShell.

8. Aby uzyskać *ILB*, Przypisz statyczny adres IP. Sprawdź bieżącą konfigurację sieci wirtualnej, uruchamiając następujące polecenie:

        (Get-AzureVNetConfig).XMLConfiguration
9. Uwaga *podsieci* nazwy podsieci, która zawiera maszyny wirtualnej tego hosta repliki. Ta nazwa jest używana w parametrze $SubnetName w skrypcie.

10. Uwaga *VirtualNetworkSite* nazwy i początkowe *prefiks adresu* dla podsieci, która zawiera maszyny wirtualne, które hosta repliki. Wyszukaj dostępnego adresu IP przez przekazanie obie wartości `Test-AzureStaticVNetIP` polecenia i po sprawdzeniu *AvailableAddresses*. Na przykład, jeśli sieć wirtualną o nazwie *MyVNet* i ma zakres adresów podsieci, która rozpoczyna się od *172.16.0.128*, polecenie pojawi się lista dostępnych adresów:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Wybierz jedną z dostępnych adresów, a następnie użyć jej w parametrze $ILBStaticIP skryptu w następnym kroku.

12. Skopiuj poniższy skrypt programu PowerShell do edytora tekstu, a następnie ustaw wartości zmiennych do potrzeb środowiska. Wartości domyślne zostały dołączone niektórych parametrów.  

    Istniejące wdrożenia, które korzysta z grup koligacji nie można dodać ILB. Aby uzyskać więcej informacji na temat wymagań dotyczących ILB, zobacz [Omówienie usługi równoważenia obciążenia wewnętrznego](../../../load-balancer/load-balancer-internal-overview.md).

    Ponadto jeśli grupy dostępności obejmuje regiony platformy Azure, musi uruchamiania skryptu raz w każdym centrum danych do usługi w chmurze i węzły, które znajdują się w tym centrum danych.

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

13. Po ustawieniu zmienne, skopiuj skrypt w edytorze tekstu do sesji programu PowerShell, aby go uruchomić. Jeśli nadal wyświetlany jest monit **>>**, naciśnij klawisz Enter ponownie, aby upewnić się, uruchomienie skryptu.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sprawdź, czy KB2854082 jest zainstalowana w razie potrzeby
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otworzyć porty zapory w węzłach grupy dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

Tworzenie odbiornika grupy dostępności w dwóch krokach. Najpierw należy utworzyć zasobu klastra punkt dostępu klienta i skonfigurować zależności. Po drugie Skonfiguruj zasoby klastra w programie PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Utwórz punkt dostępu klienta i skonfiguruj zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. ILB należy użyć adresu IP ILB, który został utworzony wcześniej. Aby uzyskać ten adres IP w programie PowerShell, użyj następującego skryptu:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednym z maszyn wirtualnych Skopiuj skrypt programu PowerShell dla systemu operacyjnego do edytora tekstu, a następnie ustaw zmienne do wartości, które wcześniej zapisany.

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

3. Po ustawieniu zmienne, Otwórz okno programu Windows PowerShell z podwyższonym poziomem uprawnień, wklej skrypt w edytorze tekstowym w sesji programu PowerShell, aby go uruchomić. Jeśli nadal wyświetlany jest monit **>>**, naciśnij klawisz Enter, ponownie, aby upewnić się, że skrypt zacznie działać.

4. Powtórz te czynności dla każdej maszyny Wirtualnej.  
    Ten skrypt konfiguruje zasobu adresów IP przy użyciu adresu IP usługi w chmurze i ustawia innych parametrów, takich jak port sondy. Gdy zasób adresu IP w tryb online, mogą odpowiadać na sondowanie na port sondy z równoważeniem obciążenia punktu końcowego, który został utworzony wcześniej.

## <a name="bring-the-listener-online"></a>Przełącz odbiornika w trybie online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy monitowania
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testowanie odbiornika grupy dostępności (w ramach tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
