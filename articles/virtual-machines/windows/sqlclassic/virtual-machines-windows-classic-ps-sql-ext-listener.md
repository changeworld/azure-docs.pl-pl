---
title: Konfigurowanie odbiornika zewnętrznego dla grup dostępności
description: W tym samouczku otrzymasz od ciebie kroków tworzenia odbiornika grupy zawsze włączonej dostępności na platformie Azure, który jest dostępny zewnętrznie przy użyciu publicznego wirtualnego adresu IP skojarzonej usługi w chmurze.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: ca13d5e8369d007188a17352913519172ed8744e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978182"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurowanie zewnętrznego odbiornika dla grup dostępności na maszynach wirtualnych programu Sql Server platformy Azure
> [!div class="op_single_selector"]
> * [Odbiornik wewnętrzny](../classic/ps-sql-int-listener.md)
> * [Odbiornik zewnętrzny](../classic/ps-sql-ext-listener.md)
> 
> 

W tym temacie pokazano, jak skonfigurować odbiornik dla grupy zawsze dostępnej dostępności, która jest zewnętrznie dostępna w Internecie. Jest to możliwe dzięki skojarzeniu z odbiornikiem publicznego adresu **wirtualnego adresu IP (VIP)** usługi w chmurze.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Grupa dostępności może zawierać repliki, które są tylko lokalne, tylko platforma Azure lub obejmuje zarówno lokalnie, jak i na platformie Azure dla konfiguracji hybrydowych. Repliki platformy Azure mogą przebywać w tym samym regionie lub w wielu regionach przy użyciu wielu sieci wirtualnych (sieci wirtualnych). Poniższe kroki zakładają, że grupa dostępności została już [skonfigurowana,](../classic/portal-sql-alwayson-availability-groups.md) ale nie skonfigurowano odbiornika.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Wytyczne i ograniczenia dla słuchaczy zewnętrznych
Należy zwrócić uwagę na następujące wskazówki dotyczące odbiornika grupy dostępności na platformie Azure podczas wdrażania przy użyciu publicznego adresu VIP usługi w chmurze:

* Odbiornik grupy dostępności jest obsługiwany w systemach Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Aplikacja kliencka musi znajdować się w innej usłudze w chmurze niż ta, która zawiera maszyny wirtualne grupy dostępności. Platforma Azure nie obsługuje bezpośredniego zwracania serwera z klientem i serwerem w tej samej usłudze w chmurze.
* Domyślnie kroki opisane w tym artykule pokazują, jak skonfigurować jednego odbiornika do używania adresu wirtualnego adresu IP (VIP) usługi w chmurze. Można jednak zarezerwować i utworzyć wiele adresów VIP dla usługi w chmurze. Dzięki temu można użyć kroków w tym artykule, aby utworzyć wiele odbiorników, które są skojarzone z innym adresu VIP. Aby uzyskać informacje na temat tworzenia wielu adresów VIP, zobacz [Wiele adresów VIP na usługę w chmurze](../../../load-balancer/load-balancer-multivip.md).
* Jeśli tworzysz odbiornik dla środowiska hybrydowego, sieć lokalna musi mieć łączność z publicznym Internetem oprócz sieci VPN lokacja-lokacja za pomocą sieci wirtualnej platformy Azure. W podsieci platformy Azure odbiornik grupy dostępności jest osiągalny tylko przez publiczny adres IP odpowiedniej usługi w chmurze.
* Nie jest obsługiwany do tworzenia odbiornika zewnętrznego w tej samej usłudze w chmurze, gdzie masz również odbiornik wewnętrzny przy użyciu modułu wewnętrznego równoważenia obciążenia (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Określanie dostępności odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

W tym artykule skupiono się na tworzeniu odbiornika korzystającego z **zewnętrznego równoważenia obciążenia**. Jeśli chcesz, aby odbiornik, który jest prywatny dla sieci wirtualnej, zobacz wersję tego artykułu, który zawiera kroki konfigurowania [odbiornika z ilb](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzenie punktów końcowych maszyny wirtualnej z równoważenia obciążenia z bezpośrednim powrotem serwera
Zewnętrzne równoważenie obciążenia używa wirtualnego publicznego wirtualnego adresu IP usługi w chmurze, która obsługuje maszyny wirtualne. Dlatego nie trzeba tworzyć ani konfigurować modułu równoważenia obciążenia w tym przypadku.

Należy utworzyć punkt końcowy z równoważenia obciążenia dla każdej maszyny Wirtualnej hosting repliki platformy Azure. Jeśli masz repliki w wielu regionach, każda replika dla tego regionu musi znajdować się w tej samej usłudze w chmurze w tej samej sieci wirtualnej. Tworzenie replik grupy dostępności obejmujących wiele regionów platformy Azure wymaga skonfigurowania wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania łączności między sieciami wirtualnymi, zobacz Konfigurowanie sieci [wirtualnej do łączności sieci wirtualnej](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W witrynie Azure portal przejdź do każdej maszyny Wirtualnej obsługującej replikę i wyświetl szczegóły.
2. Kliknij kartę **Punkty końcowe** dla każdej maszyny wirtualnej.
3. Sprawdź, czy **nazwa** i **port publiczny** punktu końcowego odbiornika, którego chcesz użyć, nie jest już używany. W poniższym przykładzie nazwa to "MyEndpoint", a port to "1433".
4. Na kliencie lokalnym pobierz i zainstaluj [najnowszy moduł programu PowerShell](https://azure.microsoft.com/downloads/).
5. Uruchom **program Azure PowerShell**. Nowa sesja programu PowerShell jest otwierana z załadowanymi modułami administracyjnymi platformy Azure.
6. Uruchom **plik Get-AzurePublishSettingsFile**. To polecenie cmdlet kieruje użytkownika do przeglądarki w celu pobrania pliku ustawień publikowania do katalogu lokalnego. Może zostać wyświetlony monit o poświadczenia logowania dla subskrypcji platformy Azure.
7. Uruchom polecenie **Import-AzurePublishSettingsFile** ze ścieżką pobranego pliku ustawień publikowania:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po zaimportowaniu pliku ustawień publikowania można zarządzać subskrypcją platformy Azure w sesji programu PowerShell.
    
1. Skopiuj skrypt programu PowerShell poniżej do edytora tekstu i ustaw wartości zmiennych zgodnie z twoim środowiskiem (określono wartości domyślne dla niektórych parametrów). Należy zauważyć, że jeśli grupa dostępności obejmuje regiony platformy Azure, należy uruchomić skrypt raz w każdym centrum danych dla usługi w chmurze i węzłów, które znajdują się w tym centrum danych.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Po ustawieniu zmiennych skopiuj skrypt z edytora tekstu do sesji programu Azure PowerShell, aby go uruchomić. Jeśli monit nadal jest wyświetlany >>, wpisz enter ponownie, aby upewnić się, że skrypt zostanie uruchomiony.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sprawdź, czy w razie potrzeby jest zainstalowana aktualizacja KB2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otwieranie portów zapory w węzłach grupy dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

Utwórz odbiornik grupy dostępności w dwóch krokach. Najpierw utwórz zasób klastra punktów dostępu klienta i skonfiguruj zależności. Po drugie, skonfiguruj zasoby klastra za pomocą programu PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Tworzenie punktu dostępu klienta i konfigurowanie zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. W przypadku równoważenia obciążenia zewnętrznego należy uzyskać publiczny wirtualny adres IP usługi w chmurze zawierającej repliki. Zaloguj się do witryny Azure Portal. Przejdź do usługi w chmurze, która zawiera maszynę wirtualną grupy dostępności. Otwórz widok **pulpitu nawigacyjnego.**
2. Zanotuj adres wyświetlany w **obszarze Publiczny wirtualny adres IP (VIP)**. Jeśli rozwiązanie obejmuje sieci wirtualne, powtórz ten krok dla każdej usługi w chmurze, która zawiera maszynę wirtualną, która obsługuje replikę.
3. Na jednej z maszyn wirtualnych skopiuj poniższy skrypt programu PowerShell do edytora tekstu i ustaw zmienne na wartości, które zostały wcześniej odnotowane.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Po ustawieniu zmiennych otwórz okno programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie skopiuj skrypt z edytora tekstu i wklej do sesji programu Azure PowerShell, aby go uruchomić. Jeśli monit nadal jest wyświetlany >>, wpisz enter ponownie, aby upewnić się, że skrypt zostanie uruchomiony.
5. Powtórz tę czynność na każdej maszynie wirtualnej. Ten skrypt konfiguruje zasób adres IP z adresem IP usługi w chmurze i ustawia inne parametry, takie jak port sondy. Gdy zasób adres IP jest przenoszony do trybu online, może następnie odpowiedzieć na sondowanie na porcie sondowania z punktu końcowego równoważenia obciążenia utworzonego wcześniej w tym samouczku.

## <a name="bring-the-listener-online"></a>Przewiń słuchacza w tryb online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy uzupełniające
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testowanie odbiornika grupy dostępności (w tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testowanie odbiornika grupy dostępności (przez Internet)
Aby uzyskać dostęp do odbiornika spoza sieci wirtualnej, należy używać równoważenia obciążenia zewnętrznego/publicznego (opisanego w tym temacie), a nie równoważenia obciążenia sieciowego, które jest dostępne tylko w tej samej sieci wirtualnej. W ciągu połączenia należy określić nazwę usługi w chmurze. Na przykład, jeśli masz usługę w chmurze o nazwie *mycloudservice*, sqlcmd instrukcji będzie w następujący sposób:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

W przeciwieństwie do poprzedniego przykładu musi być używane uwierzytelnianie SQL, ponieważ wywołujący nie może używać uwierzytelniania systemu Windows przez Internet. Aby uzyskać więcej informacji, zobacz [Zawsze w grupie dostępności w usłudze Azure VM: Scenariusze łączności klienta](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Podczas korzystania z uwierzytelniania SQL, upewnij się, że tworzysz ten sam login na obu replikach. Aby uzyskać więcej informacji na temat rozwiązywania problemów z logowaniami za pomocą grup dostępności, zobacz [Jak mapować logowania lub używać użytkownika bazy danych SQL do łączenia się z innymi replikami i mapowania baz danych dostępności](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Jeśli repliki always on znajdują się w różnych podsieciach, klienci muszą określić **MultisubnetFailover=True** w ciągu połączenia. Powoduje to równoległe próby połączenia replik w różnych podsieciach. Należy zauważyć, że ten scenariusz obejmuje międzyregionów zawsze na dostępność wdrożenia grupy.

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

